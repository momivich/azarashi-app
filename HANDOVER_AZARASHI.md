# アザラシファミリーアプリ 引き継ぎプロンプト

## このファイルの使い方
新しいチャットにこのファイルの内容をそのまま貼り付けてください。

---

## プロジェクト概要

**アザラシファミリーアプリ** — 日本の水族館・動物園のアザラシ情報をまとめたSPA（Single Page App）。
コアラアプリ（koala-app.html）と同じアーキテクチャ・設計思想で作成。個人利用。

- **本番URL**: （GitHub Pages: 未設定）
- **リポジトリ**: （未作成）
- **ローカルファイル**: `azarashi-app.html`、`azarashi-sw.js`、`azarashi-manifest.json`
- **SW キャッシュバージョン**: 現在 `azarashi-app-v1`（更新時はインクリメント必須）

---

## アーキテクチャ

### ファイル構成
```
azarashi-app.html         メインSPA（全機能入り）
azarashi-sw.js            Service Worker（キャッシュ管理）
azarashi-manifest.json    PWAマニフェスト
azarashi-icon-192.png     PWAアイコン（要作成）
azarashi-icon-512.png     PWAアイコン（要作成）
azarashi-images/          アザラシ画像（キャラ名でサブフォルダ分け）
```

### SPA構造
- タブ切り替えは `showTab(name, btn)` で div を `display:block/none`
- タブ一覧: **図鑑**、**家系図**、**クイズ**、**もぐれ**（拡張予定）

---

## アザラシデータ（AK配列）

```javascript
const AK = [
  {
    id: 1,
    name: 'ウミ',          // 表示名
    kana: 'うみ',          // ひらがな（検索用）
    sex: 'f',              // 'm' or 'f' or 'u'（不明）
    born: '2019-06-15',   // YYYY-MM-DD（不明なら null）
    died: null,            // 死亡日（生存中は null）
    zoo: '旭山動物園',     // 施設名
    species: 'ゴマフアザラシ', // 種類
    img: null,             // 画像パス（例: 'azarashi-images/ウミ/photo.jpg'）
    alive: true,           // 生存フラグ
    parents: [],           // 親のIDリスト（例: [3,4]）
    children: [2, 3],     // 子のIDリスト
    memo: '性格や特徴のメモ'
  },
  ...
]
```

### 種類（species）に使うコアラ
| 和名 | 英名 |
|------|------|
| ゴマフアザラシ | Spotted Seal |
| ゼニガタアザラシ | Harbor Seal |
| ワモンアザラシ | Ringed Seal |
| アゴヒゲアザラシ | Bearded Seal |
| タテゴトアザラシ | Harp Seal |

---

## 実装済み機能

### 図鑑（buildZukan）
- 名前・水族館名で検索フィルター
- 性別・施設でドロップダウンフィルター
- カードタップでモーダル詳細表示
- `sealFace(a, size)` でSVGプレースホルダー生成（img:null のとき）

### 家系図（buildTree）
- 施設ごとにセクション分け
- 親子関係をインデントで表現
- カードタップでモーダル表示
- 初回タブ表示時に自動ビルド

### クイズ（initQuiz）
- AKデータから自動生成（施設名・種類クイズ）
- 8問ランダム出題
- 正解で緑・不正解で赤ハイライト

### もぐれゲーム（initMogure）
- Canvasベース（320×460）
- 魚絵文字をタップして得点
- サメ・くらげは触るとミス
- 30秒タイムアタック

---

## UIテーマ

### カラー
| 用途 | 値 |
|------|-----|
| body背景 | `#e5f3fb` |
| ヘッダー | `linear-gradient(135deg, #0a2d4a, #1a5f8a, #2896c8)` |
| ナビ | `linear-gradient(#1a5f8a, #0a3d5c)` |
| アクティブタブ下線 | `#64d0f5` |
| カード背景 | white |
| カードface | `linear-gradient(160deg, #b0d8f5, #d4eeff)` |
| プライマリ色 | `#0a3d5c` |
| アクセント | `#2896c8` |

### SVGアザラシ（sealFace）
- 性別で色変化: オス=青系 `#64a0d0`、メス=紫系 `#c07ab8`
- ひげ・前足・後足も描画

---

## 画像の追加方法

1. `azarashi-images/<名前>/` フォルダに画像を置く
2. AKの該当エントリの `img` を設定:
   ```javascript
   img: 'azarashi-images/ウミ/asahiyama_2023.jpg'
   ```
3. `azarashi-sw.js` の `PRECACHE` に画像パスを追加
4. SW バージョンをインクリメント

---

## git 運用（未設定）

リポジトリ作成後:
```bash
cd <azarashiフォルダ>
git init
git add azarashi-app.html azarashi-sw.js azarashi-manifest.json
git commit -m "feat: initial template"
git remote add origin https://github.com/<user>/azarashi-app.git
git push -u origin main
```

デプロイ: GitHub Pages（Settings → Pages → Branch: main / root）
**SW更新時は `azarashi-sw.js` の `const CACHE = 'azarashi-app-vNN'` をインクリメント必須**

---

## 拡張候補（未実装）

- サウンドノベル（コアラアプリのエンジンを移植）
- 記憶ゲーム（神経衰弱）
- タワーディフェンス系ゲーム
- 誕生日カレンダー（今月誰が誕生日？）
- 水族館マップ（施設ごとの在籍一覧）
- ゴマフ / ゼニガタ など種類別図鑑ビュー

---

## 参考リンク（施設公式）

- 旭山動物園: https://www.city.asahikawa.hokkaido.jp/asahiyamazoo/
- 名古屋港水族館: https://nagoyaaqua.jp/
- 鴨川シーワールド: https://www.kamogawa-seaworld.jp/
- サンシャイン水族館: https://sunshinecity.jp/aquarium/
- すみだ水族館: https://www.sumida-aquarium.com/
- 上野動物園: https://www.tokyo-zoo.net/zoo/ueno/

---

## 直近の作業履歴

- テンプレート初作成（2026-06-17）
- プレースホルダーデータ10頭（旭山・名古屋港・鴨川・サンシャイン・すみだ）
- 機能: 図鑑・家系図・クイズ・もぐれゲーム
