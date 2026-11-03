# サイトの公開

GitHub Wiki には `x-robots-tag: none` が付き、検索エンジンにインデックスされない。\
そこで、Wiki を原本にしたまま、GitHub Pages に静的サイトとして公開する。

- 公開先：<https://netdevinfrawginossconsortium.github.io/NetDevInfraWiki/>
- 変換：`build_site.py`（Wiki のファイルは書き換えない）
- 実行：`.github/workflows/publish-site.yml`（Wiki の更新、このリポジトリの `tools/` の更新、手動）

## 公開するもの

| 対象 | 扱い |
| --- | --- |
| ページ（`*.md`） | 公開する |
| 添付・画像 | ページから参照されたものだけ公開する |
| `AGENTS`・`CLAUDE`・`DONE`・`TODO`・`WATCH`、先頭が `_` のファイル | 公開しない。リンクは Wiki へ向ける |

## 導入の手順（1 回だけ）

1. このリポジトリの Settings → Pages → Source を「GitHub Actions」にする。
2. Actions で `publish-site` を手動で実行し、公開を確かめる。
3. Wiki を `git push` で更新し、`publish-site` が動くかを確かめる。動かなければ、ワークフローに週次の `schedule` を足す。
4. Google Search Console に登録する（下の表）。

### Google Search Console への登録

| # | 作業 | 担当 |
| --- | --- | --- |
| 1 | Search Console で URL プレフィックスに `https://netdevinfrawginossconsortium.github.io/NetDevInfraWiki/` を入力する | 人 |
| 2 | 確認方法で「HTML タグ」を選び、`content="…"` の中身だけをコピーする | 人 |
| 3 | リポジトリの Settings → Secrets and variables → Actions → **Repository variables** に、`GOOGLE_SITE_VERIFICATION` として登録する | 人 |
| 4 | Actions で `publish-site` を手動で実行する | 人 |
| 5 | `<meta name="google-site-verification" …>` を全ページに埋め込んで公開する | **Actions** |
| 6 | Search Console に戻り、「確認」を押す | 人 |
| 7 | サイトマップに `sitemap.xml` を入力して送信する | 人 |

- プロジェクト サイトでは `robots.txt` が効かないので、サイトマップは Search Console で登録する。

## 手元で試す

```powershell
pip install -r tools/requirements.txt
python tools/build_site.py --wiki ..\NetDevInfraWiki.wiki --out _site `
  --base-url https://netdevinfrawginossconsortium.github.io/NetDevInfraWiki/ --report report.json
python -m http.server 8000 --directory _site
```

`--report` の JSON に、リンク切れ、アンカーの切れ、参照されていない添付が出る。Actions では `build-report` の成果物に入る。
