# Apple-tube

YouTube風のUIで動画を検索・視聴できる、**1ファイルで動く非公式の動画クライアント**です。
ビルドもサーバーも不要で、`Apple-tube.html` をブラウザで開くだけで動きます。

> ⚠️ 非公式クライアントです。YouTube・Invidious とは提携していません。
> 動画の保存は各サービスの利用規約と著作権法を必ず確認してください。

## 主な機能

- **ホーム / 急上昇**: カテゴリ(ゲーム・ライブ・音楽・ニュース・エンタメ・料理・ドキュメンタリー)で絞り込み
- **検索**: キーワードで動画を検索
- **ショート**: 縦スクロール形式のショート動画
- **視聴ページ**: 画質切り替え、コメント表示、関連動画
- **チャンネルページ** と **チャンネル登録**(ブラウザ内に保存)
- **通知**: 登録チャンネルの新着やエラーをベル通知で表示
- **再生方式の切り替え**(⚙設定)
  - ダウンロードして再生(標準)
  - 直接ストリーミング
  - 自動(redirect.invidious.io に振り分け)
  - サーバ試行(従来)
- **一時保存の管理**: 保存時間の設定と自動削除、手動削除
- **クレジット画面**
- スマホ対応のレスポンシブUI

## 仕組み

| 項目 | 内容 |
| --- | --- |
| 構成 | 単一の HTML ファイル(HTML + CSS + JavaScript) |
| 動画データ | [Invidious](https://invidious.io/) の公開インスタンスの API |
| サムネイル | `i.ytimg.com` |
| ルーティング | URLのハッシュ(`#/home`, `#/watch/...` など) |
| 保存先 | `localStorage`(設定・登録チャンネル)、`IndexedDB`(一時保存した動画) |
| 依存ライブラリ | なし |

複数のInvidiousインスタンスを上から順に試し、取得できたものを使います。
サーバー側の処理は一切ないため、データはすべてブラウザ内に保存されます。

## ローカルで試す

```bash
git clone https://github.com/Mugis-Green-Apple/Appple-Tube.git
cd Appple-Tube
```

`Apple-tube.html` をブラウザで開けば動作します。
Invidiousへのアクセスで問題が出る場合は、簡易サーバーを立ててください。

```bash
python3 -m http.server 8000
# http://localhost:8000/Apple-tube.html を開く
```

## デプロイ

静的サイトなので、ビルドコマンドは不要です。
どのサービスでも、まず**エントリーファイルを `index.html` にリネームしておく**のが最も簡単です。

```bash
git mv Apple-tube.html index.html
```

リネームしない場合は、下記の各サービスの手順にある「`/` を `Apple-tube.html` に向ける設定」が必要です。

### Vercel

1. [Vercel](https://vercel.com/) で **Add New → Project** からこのリポジトリをインポート
2. **Framework Preset** は `Other`、**Build Command** と **Output Directory** は空のままにする
3. **Deploy** を押す

`index.html` にリネームしない場合は、リポジトリ直下に `vercel.json` を追加します。

```json
{
  "rewrites": [{ "source": "/", "destination": "/Apple-tube.html" }]
}
```

### Render

1. [Render](https://render.com/) で **New → Static Site** を選び、このリポジトリを接続
2. **Build Command** は空、**Publish Directory** は `.` にする
3. **Create Static Site** を押す

`index.html` にリネームしない場合は、**Redirects/Rewrites** に次のルールを追加します。

| Source | Destination | Action |
| --- | --- | --- |
| `/` | `/Apple-tube.html` | Rewrite |

### Railway

1. [Railway](https://railway.com/) で **New Project → Deploy from GitHub repo** からこのリポジトリを選択
2. ルートに `index.html` があれば、静的サイトとして配信されます
3. **Settings → Networking** で **Generate Domain** を押して公開URLを発行

自動で配信されない場合は、`package.json` を追加して `serve` で配信します。

```json
{
  "name": "apple-tube",
  "private": true,
  "scripts": {
    "start": "npx serve -s . -l $PORT"
  }
}
```

## 設定のカスタマイズ

`Apple-tube.html` の `<script>` 冒頭にある定数を編集します。

| 定数 | 説明 |
| --- | --- |
| `INSTANCES` | 使用するInvidiousインスタンスの一覧(上から順に試行) |
| `REDIRECT` | 「自動」モードで使う振り分けURL |
| `GAS_PROXY` | Google Apps Script のプロキシURL(任意。最後の予備として使用) |
| `CREDIT_ROWS` | クレジット画面に表示する内容 |

## トラブルシューティング

- **動画が読み込めない / 再生できない**: Invidiousの公開インスタンスは停止や制限が頻繁に起こります。⚙設定で再生方式を変えるか、`INSTANCES` を最新の稼働中のものに更新してください。
- **ページが 404 になる**: エントリーファイル名が `Apple-tube.html` のままになっていないか、上記「デプロイ」を確認してください。

## クレジット

- 作成者: Mugis-Green-Apple
- 協力: Proxoo-team
- UI参考: YouTube

## ライセンス

未設定です。公開・再配布する場合は、`LICENSE` ファイルを追加してください。
