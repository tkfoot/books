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
# id: 1                        # 任意: 既存作品の更新時のみ指定（データベースID）
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
published: true                # 必須: 公開フラグ（true: 公開, false: 非公開）
updated: false                 # 必須: 更新フラグ（true: 更新, false: 新規作成）
created_at: "2025-07-14"       # 必須: 作成日(YYYY-MM-DD)
```

#### 作品の処理ロジック
- **新規作成**: `id`を指定せず、`updated: false`
- **既存更新**: `id`を指定し、`updated: true`
- **非公開**: `published: false`（処理をスキップ）
- **無視**: `id`指定あり + `updated: false`（処理をスキップ）

### 2. エピソードファイル (`.md`)

各エピソード・チャプターのファイルです。**必須のfrontmatter**が必要です。

```markdown
---
id: "episode-001"              # 必須: エピソード固有ID
title: "第1話 始まりの物語"     # 必須: エピソードタイトル
episode_number: 1              # 必須: エピソード番号（整数）
published_at: "2025-07-14"    # 任意: 公開日(YYYY-MM-DD)
status: "new"                  # 必須: エピソードステータス
---

# エピソードタイトル

本文内容...
```

#### エピソードのステータス
- **`new`**: 新規追加（データベースにINSERT）
- **`updated`**: 更新（データベースをUPDATE）
- **`draft`**: 下書き（処理をスキップ）
- **`deleted`**: 削除（データベースからDELETE）

## ✅ 必須フィールド

### info.yml
- `title`: 作品タイトル
- `author`: 著者名
- `description`: 作品説明
- `genre`: ジャンル
- `status`: 連載状況
- `published`: 公開フラグ
- `updated`: 更新フラグ
- `created_at`: 作成日

### エピソードファイル
- `id`: エピソード固有ID（重複禁止）
- `title`: エピソードタイトル
- `episode_number`: エピソード番号（整数、並び順決定に使用）
- `status`: エピソードステータス（new/updated/draft/deleted）

## 🚫 バリデーションエラー

以下の場合、GitHub Actionsでエラーが発生します：

1. **frontmatterの不備**
   - `id`、`title`、`episode_number`、`status`が未設定
   - `episode_number`が整数でない

2. **ID重複**
   - 同じ`id`のエピソードが存在

3. **ファイル形式**
   - `.md`ファイルがMarkdown形式でない
   - info.ymlがYAML形式でない

4. **必須フィールド不足**
   - `published`、`updated`フィールドが未設定

## 📋 作業手順

### 新しい作品の追加
```bash
mkdir 新作品名
cd 新作品名
mkdir manuscript
# info.ymlとエピソードファイルを作成
```

**info.yml**（新規作品）:
```yaml
title: "新作品"
author: "作者名"
description: "新しい作品です"
genre: "ファンタジー"
status: "連載中"
published: true
updated: false  # 新規作成
created_at: "2025-07-17"
```

### 既存作品の更新
**info.yml**（既存作品更新）:
```yaml
id: 123  # データベースのID
title: "既存作品（更新版）"
author: "作者名"
description: "更新された作品です"
genre: "ファンタジー"
status: "連載中"
published: true
updated: true  # 更新フラグ
created_at: "2025-07-17"
```

### エピソードの管理

**新規エピソード**:
```markdown
---
id: "new-episode-001"
title: "新しいエピソード"
episode_number: 5
status: "new"
---
```

**既存エピソードの更新**:
```markdown
---
id: "existing-episode-001"
title: "更新されたエピソード"
episode_number: 1
status: "updated"
---
```

**エピソードの削除**:
```markdown
---
id: "delete-episode-001"
title: "削除予定エピソード"
episode_number: 2
status: "deleted"
---
```

**下書きエピソード**:
```markdown
---
id: "draft-episode-001"
title: "下書きエピソード"
episode_number: 3
status: "draft"
---
```

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
title: "私の小説"
author: "作者名"
description: "面白い小説です"
genre: "ファンタジー"
status: "連載中"
published: true
updated: false
created_at: "2025-07-17"
```

**chapter1.md**:
```markdown
---
id: "my-novel-ch1"
title: "第1章 始まり"
episode_number: 1
status: "new"
published_at: "2025-07-17"
---

# 第1章 始まり

物語の本文...
```

この仕様に従うことで、コンテンツが正常にnovel-viewerに同期され、適切に表示されます。

## 🔄 同期処理の流れ

1. **作品の処理**
   - `published: false` → スキップ
   - `published: true` + `id`なし → 新規作成
   - `published: true` + `id`あり + `updated: true` → 更新
   - `published: true` + `id`あり + `updated: false` → スキップ

2. **エピソードの処理**
   - `status: draft` → スキップ
   - `status: new` → 新規作成
   - `status: updated` → 更新
   - `status: deleted` → 削除

この仕組みにより、柔軟で効率的なコンテンツ管理が可能になります。