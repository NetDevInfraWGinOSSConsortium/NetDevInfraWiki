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
4. Google Search Console に URL プレフィックスのプロパティを追加する。
   - 確認の方法に HTML タグを選び、コードを Actions の変数 `GOOGLE_SITE_VERIFICATION` に入れて、もう一度実行する。
   - `sitemap.xml` を登録する（`https://netdevinfrawginossconsortium.github.io/NetDevInfraWiki/sitemap.xml`）。プロジェクト サイトでは `robots.txt` が効かないので、ここで登録する。

## 手元で試す

```powershell
pip install -r tools/requirements.txt
python tools/build_site.py --wiki ..\NetDevInfraWiki.wiki --out _site `
  --base-url https://netdevinfrawginossconsortium.github.io/NetDevInfraWiki/ --report report.json
python -m http.server 8000 --directory _site
```

`--report` の JSON に、リンク切れ、アンカーの切れ、参照されていない添付が出る。Actions では `build-report` の成果物に入る。
