# Books Repository - データ形式仕様

このリポジトリは小説・書籍コンテンツの管理を行うものです。コンテンツは自動的に[novel-viewer](https://github.com/yar0316/novel-viewer)に同期されます。

## 📁 ディレクトリ構成

```
books/
├── 作品フォルダ1/
│   ├── manuscript/
│   │   ├── info.yml          # 作品情報
│   │   ├── chapter1.md       # 各エピソード
│   │   ├── chapter2.md
│   │   └── ...
│   ├── characters/           # キャラクター設定（任意）
│   ├── world/               # 世界設定（任意）
│   └── plot/                # プロット（任意）
├── 作品フォルダ2/
└── ...
```

## 📝 必須ファイル形式

### 1. 作品情報ファイル (`info.yml`)

各作品のメタデータを定義します。

```yaml
# 小説の基本情報
id: "作品ID"                    # 必須: 英数字とハイフンのみ
title: "作品タイトル"            # 必須: 表示用タイトル
author: "著者名"                # 必須: 作者名
description: |                 # 必須: 作品概要
  作品の説明文
  複数行可能
genre: "ジャンル"               # 必須: 作品ジャンル
tags:                          # 任意: タグリスト
  - "タグ1"
  - "タグ2"
status: "連載中"               # 必須: 連載中/完結/休載
created_at: "2025-07-14"       # 必須: 作成日(YYYY-MM-DD)
```

### 2. エピソードファイル (`.md`)

各エピソード・チャプターのファイルです。**必須のfrontmatter**が必要です。

```markdown
---
id: "episode-001"              # 必須: エピソード固有ID
title: "第1話 始まりの物語"     # 必須: エピソードタイトル
episode_number: 1              # 必須: エピソード番号（整数）
published_at: "2025-07-14"    # 任意: 公開日(YYYY-MM-DD)
---

# エピソードタイトル

本文内容...
```

## ✅ 必須フィールド

### info.yml
- `id`: 作品の固有識別子（英数字とハイフン、アンダースコアのみ）
- `title`: 作品タイトル
- `author`: 著者名
- `description`: 作品説明
- `genre`: ジャンル
- `status`: 連載状況
- `created_at`: 作成日

### エピソードファイル
- `id`: エピソード固有ID（重複禁止）
- `title`: エピソードタイトル
- `episode_number`: エピソード番号（整数、並び順決定に使用）

## 🚫 バリデーションエラー

以下の場合、GitHub Actionsでエラーが発生します：

1. **frontmatterの不備**
   - `id`、`title`、`episode_number`が未設定
   - `episode_number`が整数でない

2. **ID重複**
   - 同じ`id`のエピソードが存在

3. **ファイル形式**
   - `.md`ファイルがMarkdown形式でない
   - info.ymlがYAML形式でない

## 📋 作業手順

1. **新しい作品の追加**
   ```bash
   mkdir 新作品名
   cd 新作品名
   mkdir manuscript
   # info.ymlとエピソードファイルを作成
   ```

2. **エピソードの追加**
   - `manuscript/`にMarkdownファイルを作成
   - 必須frontmatterを記述
   - 一意の`id`を設定

3. **同期確認**
   - GitHub Actionsが自動実行
   - エラーがあれば修正
   - 成功すればnovel-viewerに反映

## 🔧 バリデーション

ローカルでのバリデーション実行：

```bash
python scripts/validate_data.py
```

## 📚 例

### 完全な例

```
my-novel/
├── manuscript/
│   ├── info.yml
│   ├── chapter1.md
│   └── chapter2.md
└── characters/
    └── protagonist.md
```

**info.yml**:
```yaml
id: "my-novel"
title: "私の小説"
author: "作者名"
description: "面白い小説です"
genre: "ファンタジー"
status: "連載中"
created_at: "2025-07-14"
```

**chapter1.md**:
```markdown
---
id: "my-novel-ch1"
title: "第1章 始まり"
episode_number: 1
published_at: "2025-07-14"
---

# 第1章 始まり

物語の本文...
```

この仕様に従うことで、コンテンツが正常にnovel-viewerに同期され、適切に表示されます。