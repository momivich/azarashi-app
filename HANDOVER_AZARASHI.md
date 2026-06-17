# アザラシファミリーアプリ 引き継ぎメモ

最終更新: 2026-06-17

---

## プロジェクト概要

**アザラシファミリーアプリ** — 日本の水族館・動物園のアザラシ情報をまとめたSPA。
主に男鹿水族館GAOの個体とその転出先を網羅。

- **本番URL**: https://momivich.github.io/azarashi-app/azarashi-app.html
- **リポジトリ**: https://github.com/momivich/azarashi-app
- **SW キャッシュバージョン**: 現在 `azarashi-app-v5`（更新時はインクリメント必須）
- **データ出典**: on-yasuken.hatenablog.com（2025-04-04更新）

---

## ファイル構成

```
azarashi-app.html       メインSPA（全データ・全機能入り）
azarashi-sw.js          Service Worker（キャッシュ管理）
azarashi-manifest.json  PWAマニフェスト
```

---

## AK配列 現在の状態（22頭）

### 写真あり ✅（15頭）

| id | 名前     | 施設                   | img出典 |
|----|----------|------------------------|---------|
| 1  | ナナ     | 男鹿水族館GAO（故）    | GAO公式ブログ 2011年記事 |
| 2  | マリー   | 男鹿水族館GAO（故）    | GAO公式ブログ 2011年記事 |
| 7  | みずき   | 男鹿水族館GAO          | note.com記事 |
| 8  | ゴクウ   | 男鹿水族館GAO          | note.com記事 |
| 9  | こまち   | 男鹿水族館GAO          | note.com記事（GIF） |
| 10 | オガ     | 新江ノ島水族館         | note.com記事 |
| 11 | ここ     | 新江ノ島水族館         | note.com記事 |
| 13 | あずき   | 円山動物園             | miyanomayu.com記事 |
| 14 | きなこ   | マリンピア日本海       | マリンピア公式記事 |
| 16 | ほおずき | 四国水族館             | note.com記事 |
| 17 | あんず   | 越前松島水族館         | note.com記事 |
| 18 | ジェット | 城崎マリンワールド     | 城崎公式サイトOGP |
| 19 | おんぷ   | 海遊館                 | note.com記事 |
| 20 | ターボ   | 男鹿水族館GAO          | note.com記事 |
| 21 | スカイ   | 男鹿水族館GAO          | note.com記事 |

### 写真なし・探索中 ❌

| id | 名前     | 施設                        | 状況 |
|----|----------|-----------------------------|------|
| 15 | わらび   | ドルフィンリゾート          | 未探索 |
| 22 | ジャンボ | 男鹿水族館GAO（2025-03-25死亡）| 未探索（うみたまご輸送中死亡） |
| 12 | ゴハン   | 京急油壺マリンパーク        | 施設2021年閉館→個体の行方不明、写真困難 |

### 写真困難（八景島4頭）

| id | 名前   | 施設                   | 備考 |
|----|--------|------------------------|------|
| 3  | イカク | 八景島シーパラダイス   | GAOスターティングメンバー、現在不在の可能性高い |
| 4  | ポーラ | 八景島シーパラダイス   | 同上 |
| 5  | マリコ | 八景島シーパラダイス   | ナナ×マリーの子 |
| 6  | つばき | 八景島シーパラダイス   | ナナ×マリーの子 |

---

## 最近の変更履歴

### 2026-06-17（今セッション）
- **写真追加5頭**: ナナ、マリー、ジェット、きなこ、あんず
- **zoo更新**: あんず → 越前松島水族館（2026年6月5日転出確認）
- **SW**: v4 → v5
- コミット済み（hash: 0b3dfc5、メッセージ "test"）
- **⚠️ git pushは未完了** → 下記「gitプッシュ手順」参照

### 前セッション
- 写真追加10頭: みずき、ゴクウ、こまち、オガ、ここ、あずき、ほおずき、おんぷ、ターボ、スカイ
- GitHubリポジトリ作成・初回push

---

## ⚠️ gitプッシュ手順（Windowsから）

サンドボックスからはGitHubに接続できないため、Windowsターミナルで実行すること。

```powershell
# ロックファイルを削除（存在する場合）
Remove-Item "C:\Users\ym\Desktop\アザラシアプリ作成@ClaudeCode\.git\HEAD.lock" -ErrorAction SilentlyContinue
Remove-Item "C:\Users\ym\Desktop\アザラシアプリ作成@ClaudeCode\.git\objects\maintenance.lock" -ErrorAction SilentlyContinue

# プッシュ
cd "C:\Users\ym\Desktop\アザラシアプリ作成@ClaudeCode"
git push origin main
```

---

## アーキテクチャ

### SPA構造
- タブ切り替えは `showTab(name, btn)` で div を `display:block/none`
- タブ一覧: **図鑑**、**家系図**、**クイズ**、**もぐれ**

### データ構造（AK配列エントリ）

```javascript
{
  id: 1,
  name: 'ナナ',         // 表示名
  kana: 'なな',         // ひらがな（検索用）
  sex: 'm',             // 'm' or 'f'
  born: null,           // YYYY-MM-DD or null
  died: '2011-05-23',   // YYYY-MM-DD or null
  zoo: '男鹿水族館GAO', // 施設名
  species: 'ゴマフアザラシ',
  img: 'https://...',   // 外部URL直リンク or null
  alive: false,
  parents: [],          // 親のIDリスト
  children: [5,6,7],    // 子のIDリスト
  memo: 'メモ'
}
```

### img追加ルール
- 外部URLを直接 `img` フィールドに設定（ホットリンク）
- SW の PRECACHE には追加不要（外部URLはキャッシュ対象外）
- ローカル画像を使う場合は `azarashi-images/<名前>/photo.jpg` に置いて PRECACHE に追加 + SW バージョンインクリメント

---

## UIテーマ

| 用途 | 値 |
|------|----|
| body背景 | `#e5f3fb` |
| ヘッダー | `linear-gradient(135deg, #0a2d4a, #1a5f8a, #2896c8)` |
| ナビ | `linear-gradient(#1a5f8a, #0a3d5c)` |
| アクティブタブ下線 | `#64d0f5` |
| プライマリ色 | `#0a3d5c` |
| アクセント | `#2896c8` |

---

## 写真探索メモ（情報源）

| 施設 | 有効な情報源 |
|------|-------------|
| 男鹿水族館GAO | gao-aqua.jp/gao-cms（WordPressブログ）、note.com（ヒロサキさん等のファン記事） |
| マリンピア日本海 | marinepia.or.jp/news（entry-XXXX.html） |
| 城崎マリンワールド | marineworld.hiyoriyama.co.jp |
| ドルフィンリゾート | dolphin-resort.jp |
| note.com | 「わらび ドルフィンリゾート」「ジャンボ GAO」等で検索 |

---

## 拡張候補

- 誕生日カレンダー（今月誰の誕生日？）
- 水族館マップ（施設ごとの在籍一覧）
- サウンドノベル
- 記憶ゲーム（神経衰弱）
