# hexyl Cheat Sheet

## Overview

`hexyl` は、バイナリデータを16進表記付きで確認するための CLI ツールです。実務ではバイナリの内容をざっと確認したいときに便利です。

## When to Use

- バイナリファイルの内容を確認したいとき
- 16進数と ASCII を一緒に見たいとき
- バイナリの構造をざっと把握したいとき

## Basic Syntax

```bash
hexyl [options] <file>
```

## Common Examples

```bash
# バイナリを表示
hexyl file.bin

# 先頭だけ表示
hexyl -n 64 file.bin

# ASCII 文字を見やすく表示
hexyl --color file.bin

# 先頭 32 バイトだけ確認
hexyl -n 32 file.bin
```

## Frequently Used Options

```bash
# 表示行数を指定
hexyl -n 128 file.bin

# ASCII 表示を有効化
hexyl -C file.bin

# 1 行ごとの表示幅を指定
hexyl -s 16 file.bin

# 16 進数の色付き表示
hexyl --color file.bin
```

## Notes

- バイナリ解析やデバッグ時に役立ちます。
- 16進値をそのまま確認したいときに便利です。
- 文字列や構造の確認に使い分けるとよいです。

## Related Links

- Download: https://github.com/sharkdp/hexyl/releases
- Install: https://github.com/sharkdp/hexyl#installation
- License: https://github.com/sharkdp/hexyl/blob/master/LICENSE-MIT
