# AGENTS.md

力の種類あて：身のまわりの場面を見て、はたらいた力（重力・弾性の力・摩擦の力・磁力・電気の力）を当てる1日5問の学習ゲーム。正解したら「この力のとくちょうは？」（引っぱるだけ・引き合うことも反発することもある・変化に逆らう）も選ぶ。`index.html` と `data.js`（問題の種類）を GitHub Pages で公開する。

## 準備（最初に一度）

```bash
bash scripts/setup.sh   # Node.js（版は .nvmrc）を確かめ、検査の依存とブラウザを入れる。何度実行してもよい
```

Claude Code のクラウドセッションでは `.claude/settings.json` のフックが自動で実行する。

## push する前の確認

```bash
node qa/run.mjs   # 問題（18種類×3000問：選び肢・答えは1つ・答えと2段目と力のもとが力の表どおり・出典）・今日の5問と上級（3年分）・とことん・通しで遊ぶ・続きから・2段目・復習・幅320px・PWA・引き継ぎ・見やすさ（CI の QA と同じ）
```

## このリポジトリの決まり

- 中身は千葉県教育委員会「やる気アップ 理科」中学1年 身近な物理現象（力の働き。解答。<https://www.pref.chiba.lg.jp/kyouiku/shidou/gakuryoku/yaruki/rika/documents/04r01.pdf>）・大阪府教育委員会の確認プリント（解答）・茨城県教育研修センターの学習指導案で確かめたことだけ（2026-11-17 に読んだ）。場面を足すときも出典に書いてあることだけにし、`qa/run.mjs` の `TB`（別に書いた表）にも足す。出典メモは共有フォルダ games/chikara-research.md。
- むずかしいには、同じ場面に力がいくつもはたらき、問いの向き（下向き・斜め上向き）で答えが決まる問題を入れる（`g_clip`・`j_ukasu`・`g_omori`。問いの目立つ一語だけでは当たらないように。QA が見る。2026-11-25）。
- 問いに力の名前（重力・弾性の力・摩擦の力・磁力・電気の力）を書かない（QA が見る）。力の大きさの計算（ばねののび・圧力）は扱わない（てこのつりあい teko-tsuriai と重ねない）。
- 答えの決まり（2段目・力のもと・決め手）は `data.js` の `RULE` だけに書き、種類はそこから読む。`qa/run.mjs` の `CL` が別に書いた表で確かめる。
- 1問を深くする：`depth.js` と `qa/depth-check.mjs` は shared-workflows `template/` の写しで、ここでは直さない。自信あり・2段目「この力のとくちょうは？」・復習（1→3→7日後）。記録 `cks-v1`・文字 `cks-fs`・復習 `cks-depth`。
- 画面の土台は季節の天気あて（kisetsu-tenki。もとは気体あて・液性よみ・植物なかま分け・動物なかま分け・なに税？）から写した。写し元の言葉が残っていないかを QA が見る。
- 問題は、やさしいが選び肢3つ、ふつう・むずかしいが4つ（5つから）。2回目のヒントはまちがいを消して2つにしぼり、決め手（`key`）も見せる。
- 今日の5問（やさしい2・ふつう2・むずかしい1、種類は重ねない）・上級（むずかしい3、今日のむずかしいと別）は日本時間の日付の種で決まり、だれでも同じ。`Math.random()` は練習・とことんの種だけ。乱数は `sort` の比較の中で引かない。`KINDS`・言い回しの並びは最後に足すだけ（QA の `PIN` が第1回の1問目を見る）。種類を足すときは `from`（その日から今日の5問・上級に出す。`NEW_FROM`）を付け、過去の日の問題を変えない（QA が 2026-11-17〜25 の指紋で見る）。`data.js` を変える PR は自動ではマージされない（`hold_paths`）。
- ★はまちがい＋ヒントの数（0で★3・1で★2・2以上で★1）。上級はヒントなし。時間制限なし。
- 好きなだけ遊べる：チケット・ライフ・回数制限・🔒で開くモードを入れない（shared-workflows `routines/game-dev.md` F1〜F7。QA が見る）。とことん（`runs.e`）はまちがえても続き、「ここで降りる」で終わる。
- 共有は遊ぶ人が押したときに開くリンクだけ。シェアの文に問題・答えを出さない。感想は Issue フォーム（`.github/ISSUE_TEMPLATE/feedback.yml`）を開くだけ。応援は Ko-fi へのただのリンクだけ。
- 外へ通信しない（CSP の `connect-src 'self'` を広げない）。見やすさ（`<!-- a11y:begin -->`〜`end`・`qa/a11y.mjs`）と引き継ぎ（`transfer.js`・`qa/transfer-check.mjs`）は shared-workflows `template/` の写しで、ここでは直さない。
- PWA：画面のファイルを足したら `sw.js` の `SHELL` にも足し、中身を大きく変えたら `CACHE` の版を上げる。アイコンは `node tools/make-icons.cjs`、`og.png` は `node tools/make-og.cjs`。
- 公開リポジトリなので、個人情報・鍵を入れない。

## 公開

- GitHub Pages は `main` のルート（`/`）を公開する。PR の QA と Secret scan が ✅ になると `automerge.yml` が自動でマージする。止めたいときは下書きにするか、ラベル「自動マージしない」を付ける。
- `sw.js`・`manifest.webmanifest`・`data.js` を変える PR は自動ではマージされない（`hold_paths`）。CodeRabbit のレビューを読み、手でマージする直前に今の head へのレビューを読み直す。CodeRabbit は PR に `@coderabbitai review` とコメントして頼む。

## 共通のきまり

- PR の作り方・マージの仕方は satou-aaaaa/shared-workflows の `routines/pr.md`、全体のきまりは `routines/common.md` に従う。
- CI の部品は shared-workflows の共通部品を呼ぶ。リポジトリ側の .github/workflows には呼び出しと設定値だけを書く。
- Claude 以外の AI・人が作業するときは、shared-workflows の `routines/tool-map.md` と `routines/without-claude.md` を読む。
