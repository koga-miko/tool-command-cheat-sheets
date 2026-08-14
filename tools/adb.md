# adb Cheat Sheet

## Overview

`adb`（Android Debug Bridge）は、Android SDK の Platform-Tools に含まれる CLI で、PC から Android 実機・エミュレータと通信するためのツールです。実務ではアプリのインストール/アンインストール、ログ確認、ファイル転送、デバイス操作の自動化などに使います。

## When to Use

- 開発中の APK をインストール・再インストールして動作確認したいとき
- アプリのログ（logcat）を見ながらデバッグしたいとき
- デバイスと PC の間でファイルをやり取りしたいとき
- スクリーンショット・画面録画・タップ操作などを自動化したいとき
- 複数端末/エミュレータをまたいで操作したいとき

## Basic Syntax

```bash
adb [global options] <command> [args]
adb -s <serial> shell <on-device command>
```

## Common Examples

```bash
# 接続中のデバイス一覧を確認
adb devices -l

# APK をインストール
adb install app-debug.apk

# 既存アプリを上書きインストール（データは保持）
adb install -r app-debug.apk

# アプリをアンインストール
adb uninstall com.example.app

# デバイスから PC へファイルを取得
adb pull /sdcard/screenshot.png .

# PC からデバイスへファイルを転送
adb push local.txt /sdcard/local.txt

# 対話的なシェルに入る
adb shell

# シェルコマンドを1回だけ実行（インストール済みパッケージ一覧）
adb shell pm list packages

# ログを表示
adb logcat

# 端末を再起動
adb reboot
```

## Frequently Used Options

```bash
# 複数デバイス接続時に対象を指定（シリアル番号は adb devices で確認）
adb -s <serial> install app.apk

# USB接続のデバイスのみを対象にする
adb -d shell

# TCP/IP 経由でワイヤレス接続
adb connect 192.168.1.10:5555

# 切断
adb disconnect 192.168.1.10:5555

# ポートフォワード（PC側ポート → デバイス側ポート）
adb forward tcp:8080 tcp:8080

# 特定タグのログだけ表示
adb logcat -s MyAppTag

# エラー以上の優先度のログだけ表示
adb logcat *:E

# ログをクリア
adb logcat -c

# アプリの詳細情報を確認（権限・バージョンなど）
adb shell dumpsys package com.example.app

# アプリのデータ（キャッシュ含む）を消去
adb shell pm clear com.example.app

# アプリを強制終了
adb shell am force-stop com.example.app

# Activity を指定して起動
adb shell am start -n com.example.app/.MainActivity

# Activity をパラメータ（extra）付きで起動
adb shell am start -n com.example.app/.MainActivity \
  --es "user_name" "yoshi" \
  --ei "user_id" 42 \
  --ez "is_debug" true

# アクションと URI データを指定して起動（暗黙的インテントの例: URLを開く）
adb shell am start -a android.intent.action.VIEW -d "https://example.com"

# 起動完了まで待機し、起動時間を計測する
adb shell am start -W -n com.example.app/.MainActivity

# BroadcastReceiver に Intent を送る（コンポーネントを明示指定）
adb shell am broadcast -n com.example.app/.MyReceiver -a com.example.app.MY_ACTION --es "message" "hello"

# パッケージ名だけ指定してブロードキャスト（クラス名が分からない/複数ある場合）
adb shell am broadcast -p com.example.app -a com.example.app.MY_ACTION

# スクリーンショットを撮って PC に保存
adb shell screencap -p /sdcard/screen.png && adb pull /sdcard/screen.png .

# 画面を録画（Ctrl+C で停止、最大180秒）
adb shell screenrecord /sdcard/demo.mp4

# 端末のプロパティを確認（Androidバージョンなど）
adb shell getprop ro.build.version.release

# root 権限で adbd を再起動（エミュレータや対応端末のみ）
adb root
```

## Notes

- Android SDK の Platform-Tools に同梱されており、単体でも公式配布されています（Android Studio 本体は不要）。
- 端末側で USB デバッグを有効化し、初回接続時に PC の RSA 鍵を端末側で許可する必要があります。
- `am start`/`am broadcast` の extra（パラメータ）は型ごとにフラグが分かれています: `--es`（文字列）、`--ez`（真偽値）、`--ei`（int）、`--el`（long）、`--ef`（float）、`--eu`（URI）、`--ecn`（コンポーネント名）、`--eia`（int配列、カンマ区切り）、`--esa`（文字列配列、カンマ区切り）。
- Android 8.0（API 26）以降、多くの暗黙的ブロードキャスト（マニフェストで静的登録された `<receiver>` 宛の、コンポーネント指定なしのブロードキャスト）は制限されており届かないことがあります。テスト目的で確実に届けたい場合は、`-n <package>/<Receiver>` でコンポーネントを明示するか、`-p <package>` でパッケージを絞り込んでください。
- `am start -n` の書式は `パッケージ名/クラス名` で、クラス名がパッケージ名で始まる場合は `.MainActivity` のようにパッケージ名部分を省略できます。
- 複数のデバイス/エミュレータが同時接続されていると `more than one device/emulator` エラーになります。`adb devices` でシリアル番号を確認し、`-s <serial>` で対象を明示します。
- `adb shell <command>` の `<command>` は adb 自体のオプションではなく、端末上の Android フレームワークのコマンド（`pm`, `am`, `dumpsys`, `wm`, `settings` など）です。使えるサブコマンドは `adb shell <command> --help` で確認できます。
- `adb push`/`pull` は基本的に `/sdcard/` 配下などアプリからもアクセスできる領域が対象です。`/data/` 配下は多くの場合 root 権限（`adb root`、対応端末のみ）が必要です。
- ワイヤレス接続（`adb connect`）は Android 11 以降では先に `adb pair HOST:PORT PAIRING_CODE` でペアリングしておくのが推奨されています。

## Related Links

- Docs: https://developer.android.com/tools/adb
- Download (Platform-Tools): https://developer.android.com/tools/releases/platform-tools
- License: https://developer.android.com/studio/terms
