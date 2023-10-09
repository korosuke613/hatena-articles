---
Title: GitHub Actionsの歴史（2021/12/1 更新）
Category:
- CI/CD
- 生産性向上
Date: 2021-12-01T19:25:55+09:00
URL: https://korosuke613.hatenablog.com/entry/history-of-github-actions
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/13574176438037716057
---

<!-- 
検索文字列: \[(.*?)\]\((.*?)\)
置換文字列: <a href="$2" target="_blank" rel="noopener">$1</a>
-->

<!-- ここに導入を書く -->

<h1 id="start"># はじめに</h1>

GitHub Changelog を元に、GitHub Actions がどのように更新されていったかを簡単にまとめました。

あまり深いところまでは書いてないので、気になった変更があったら各自調べてください（もっと色々書きたかったけど時間なかった）。

~~わりと雑に作ったので~~漏れや間違いがあったらコメントとか下さい。

**2021/12/01 までの情報をもとにこの記事は書かれています。**

**この記事は <a href="https://qiita.com/advent-calendar/2021/github-actions" target="_blank" rel="noopener">GitHub Actions Advent Calendar 2021</a> の 1 日目の記事です 🎅🎂**

<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

- [# はじめに](#start)
- [# 歴史](#history)
  - [## 発表 〜 正式リリース（2018/10 〜 2019/11）](#beta)
  - [## 2020](#2020)
    - [### 1Q + α](#2020_1Q)
    - [### 2Q](#2020_2Q)
    - [### 3Q](#2020_3Q)
    - [### 4Q](#2020_4Q)
  - [## 2021](#2021)
    - [### 1Q](#2021_1Q)
    - [### 2Q](#2021_2Q)
    - [### 3Q](#2021_3Q)
    - [### 4Q](#2021_4Q)
  - [## 2022](#2022)
    - [### 1Q](#2022_1Q)
- [# おわりに](#end)

---

<!-- ここに広告が入る -->

<!-- リリース前 https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A%2Ccd_max%3A11%2F19%2F2019 -->
<!-- 2020/1Q https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A11%2F20%2F2019%2Ccd_max%3A3%2F31%2F2020 -->
<!-- 2020/2Q https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A4%2F1%2F2020%2Ccd_max%3A6%2F30%2F2020 -->
<!-- 2020/3Q https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A7%2F1%2F2020%2Ccd_max%3A9%2F30%2F2020 -->
<!-- 2020/4Q https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A10%2F1%2F2020%2Ccd_max%3A12%2F31%2F2020 -->
<!-- 2021/1Q https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A1%2F1%2F2021%2Ccd_max%3A3%2F31%2F2021 -->
<!-- 2021/2Q https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A4%2F1%2F2021%2Ccd_max%3A6%2F30%2F2021 -->
<!-- 2021/3Q https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A7%2F1%2F2021%2Ccd_max%3A9%2F30%2F2021 -->
<!-- 2021/4Q https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A10%2F1%2F2021%2Ccd_max%3A12%2F31%2F2021 -->

<h1 id="history"># 歴史</h1>

まず、<a href="https://github.blog/changelog/" target="_blank" rel="noopener">GitHub Changelog</a> をひたすら目 grep して Actions に関わる変更を<a href="https://zenn.dev/korosuke613/articles/github_actions_history_all_list" target="_blank" rel="noopener">リスト化</a>し、Zenn のスクラップ（<a href="https://zenn.dev/korosuke613/scraps/f43dd22a243e18" target="_blank" rel="noopener">GitHub Actionsの歴史を調査</a>）にまとめました。

そして、その中でも特にエンドユーザに関係ありそうな変更をピックアップしてこの記事に載っけています。


**各トピックに貼られているラベルの説明**

| ラベル名           | 説明                                         |
| ------------------ | -------------------------------------------- |
| `FEATURE🚀`        | 新しい機能                                   |
| `BRAKING CHANGE💥` | 破壊的な変更                                 |
| `SECURITY🔒`       | セキュリティ向上などセキュリティが理由の変更 |
| `PW💪`             | Productivity Weekly に詳細を書いたトピック   |

**各カテゴリの説明**

| カテゴリ     | 説明                                                                                                                       |
| ------------ | -------------------------------------------------------------------------------------------------------------------------- |
| System       | GitHubのシステム面での変更（わりとおおざっぱなくくりです）                                                                                                                           |
| Workflow     | ワークフロー関係に関する変更                                                                                               |
| Action       | アクションに関する変更。公式アクション((actions/cache や actions/setup-node などの actions organization 配下のアクション。))含む                                                               |
| Environments | Enviromnents 機能に関する変更                                                                                              |
| Runner       | ランナーの仕様に関する変更。GitHub-hosted runner は GitHub が提供するランナー。Self-hosted runner はセルフホストランナー。 |
| API          | Actions を操作する API に関する変更                                                                                        |
| Management   | 監査や料金、機能の制限などに関わる変更                                                                                     |
| WebUI        | ワークフロー一覧の見た目や検索など、主に WebUI に関する変更                                                                |

<h2 id="beta">## 発表 〜 正式リリース（2018/10 〜 2019/11）</h1>

GitHub Actions は 2018 年 10 月に <a href="https://github.blog/2018-10-17-action-demos/" target="_blank" rel="noopener">GitHub Universe2018</a> で発表されました。当時は <a href="https://github.blog/changelog/2018-10-16-github-actions-limited-beta/" target="_blank" rel="noopener">limited public beta</a> ということで、選ばれしもののみが利用できていたようです。

現在の GitHub Actions は YAML でワークフローの記述を行いますが、発表された当時は GUI 上でのワークフロー構築または HCL(HashiCorp Configuration Language) を使ってワークフローを記述できました（参考：<a href="https://www.kaizenprogrammer.com/entry/2019/01/13/194053" target="_blank" rel="noopener">GitHub Actions 入門 - 生産性向上ブログ</a>、<a href="https://www.matsub.net/posts/2018/12/08/gh-actions" target="_blank" rel="noopener">GitHub Actions で遊ぶ | matsub</a>）。また、ジョブは Docker コンテナ上での実行のみがサポートされており、Docker イメージを用意する必要がありました。

発表から約半年は音沙汰がなく、<a href="https://github.blog/changelog/2019-08-08-updates-to-github-actions-limited-public-beta/" target="_blank" rel="noopener">2019 年 8 月に新バージョンが発表</a>されます。何がどう変更されたかはもはや公式ドキュメントから見つけられなかったのですが、<a href="https://www.kaizenprogrammer.com/entry/2019/08/18/205010" target="_blank" rel="noopener">新 GitHub Actions 入門 - 生産性向上ブログ</a> によると、新バージョンで YAML が使えるようになったことがわかります（HCL が非推奨となることもこの時点で判明していたようです）。

改めて、旧 GitHub Actions と比べて大きく以下の変更がありました。

- ワークフローの記述方法が YAML に変更
- Linux/macOS/Windows インスタンスが利用可能に

その後<a href="https://github.blog/changelog/2019-09-17-github-actions-will-stop-running-workflows-written-in-hcl/" target="_blank" rel="noopener">実際に HCL が非推奨を通り越して停止されることが Changelog で広く発表されます</a>。発表が 9/17、停止が 9/30 なので既に本格利用していたところにとってはなかなか大変だったことでしょう。

YAML を利用する GitHub Actions が登場してから正式リリースまでも多数の変更が行われています。正式リリース前なので、大きな変更（BRAKING CHANGES 相当）だけピックアップします。

- <a href="https://github.blog/changelog/2019-10-17-github-actions-default-shell-on-windows-runners-is-changing-to-powershell/" target="_blank" rel="noopener">Windows ランナーのデフォルトシェルが cmd から PowerShell に変更</a>
- <a href="https://github.blog/changelog/2019-10-17-github-actions-removing-python-3-4-and-ruby-2-3-from-the-virtual-environments/" target="_blank" rel="noopener">Python 3.4 と Ruby 2.3 がランナーから削除</a>
- <a href="https://github.blog/changelog/2019-10-28-github-actions-update-to-job-concurrency-usage-limits/" target="_blank" rel="noopener">ジョブの同時実行数に制限が設けられる</a>（具体的な数字は不明）
- <a href="https://github.blog/changelog/2019-10-31-github-actions-macos-virtual-environment-is-updating-to-catalina-and-dropping-mojave-support/" target="_blank" rel="noopener">macOS Mojave ランナーが削除。代わりに macOS Catalina ランナーが登場</a>
- <a href="https://github.blog/changelog/2019-11-01-github-actions-scheduled-jobs-maximum-frequency-is-changing/" target="_blank" rel="noopener">スケジュールされたジョブの実行間隔の最小値が 1 分から 5 分に変更</a>
- <a href="https://github.blog/changelog/2019-11-04-github-actions-adds-dependency-caching/" target="_blank" rel="noopener">actions/cache が登場。依存関係をキャッシュできるように</a>
- <a href="https://github.blog/changelog/2019-11-05-github-actions-self-hosted-runners/" target="_blank" rel="noopener">セルフホストランナーが登場</a>

そして、<a href="https://github.blog/changelog/2019-11-11-github-actions-is-generally-available/" target="_blank" rel="noopener">2019 年 11 月 11 日に GitHub Actions が正式リリースされました</a>。発表から約 1 年でした。正式にリリースされたことで、全てのリポジトリで利用可能になりました。

<a href="https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A%2Ccd_max%3A11%2F19%2F2019" target="_blank" rel="noopener">当時の記事とかはこちら</a>

<h2 id="2020">## 2020</h2>

<h3 id="2020_1Q">### 1Q + α</h3>

ここからは四半期ごとの変更を書いていきます（**正直リリース前ほど書くことはないです**）。

正式リリースから 2020/1Q の間にあった Changelog を以下にまとめています。`actions/runner リポジトリは今でこそランナー自体の仕様を確認するために見にいくことがありますが、当時はまだ公開されてなかったんですね。API が生えたのもこの頃なので、色々なツールとの連携やセルフホストランナー登録の自動化などが捗ったことでしょう。

<a href="https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A11%2F20%2F2019%2Ccd_max%3A3%2F31%2F2020" target="_blank" rel="noopener">当時の記事とかはこちら</a>。

#### #### Workflow

- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2019-12-03-github-actions-changes-to-release-event/" target="_blank" rel="noopener">ドラフトリリースで `release` イベントをトリガーしないように</a>

#### #### Action

- `FEATURE🚀`, `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2020-01-14-github-actions-changes-to-artifact-download-experience/" target="_blank" rel="noopener">actions/download-artifact アクションがアーティファクトダウンロード時にファイルを展開するように</a>
- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2019-12-10-github-actions-aws-and-gcloud-actions-deprecated/" target="_blank" rel="noopener">actions/aws、actions/gcloud アクションが Deplicated に</a>（その後利用不可となった）

#### #### Runner

- `FEATURE🚀`: <a href="https://github.blog/changelog/2019-12-19-github-actions-the-runner-is-now-open-sourced/" target="_blank" rel="noopener">ランナーのソースコード（actions/runner）がオープンソースに</a>

##### ##### GitHub-hosted runner

- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2019-10-31-github-actions-windows-server-2016-r2-virtual-environment-will-be-removed-on-november-7/" target="_blank" rel="noopener">Windows Server 2016 ランナーが削除</a>
- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2020-02-21-github-actions-breaking-change-ubuntu-virtual-environments-will-no-longer-start-the-mysql-service-automatically/" target="_blank" rel="noopener">Ubuntu ランナーにおいて MySQL が自動で起動しないように</a>
- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2020-02-27-github-actions-breaking-change-python-2-being-removed-from-all-virtual-environments/" target="_blank" rel="noopener">ランナーから Python 2 が削除</a>

##### ##### Self-hosted runner

- `FEATURE🚀`: <a href="https://github.blog/changelog/2019-12-03-github-actions-self-hosted-runners-on-arm-architectures/" target="_blank" rel="noopener">セルフホストランナーが ARM/ARM64 アーキテクチャをサポート</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-02-12-github-actions-proxy-support-for-self-hosted-runners/" target="_blank" rel="noopener">セルフホストランナーがプロキシ環境下で利用可能に</a>

#### #### API

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-01-27-github-actions-api-beta/" target="_blank" rel="noopener">GitHub Actions API が登場</a>（<a href="https://github.blog/changelog/2020-03-24-github-actions-api-is-now-generally-available/" target="_blank" rel="noopener">正式リリース</a>）
- `FEATURE🚀`, `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2020-03-09-new-filter-parameter-in-workflow-jobs-api/" target="_blank" rel="noopener">workflow run の一覧を返す Actions API がデフォルトで最新のジョブのみを返すように</a>

#### #### WebUI

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-03-10-event-specific-details-on-the-actions-tab/" target="_blank" rel="noopener">WebUI の Actions タブが見やすくなった</a>

<h3 id="2020_2Q">### 2Q</h3>

2020/2Q にあった Changelog を以下にまとめています。

1Q と比べて圧倒的に `FEATURE🚀` が多いですね。この頃で嬉しかったのはやはりワークフロー周りの変更でしょうか。デフォルトのシェルやワーキングディレクトリーを設定できたり、ジョブ間でのパラメータ引き渡しが簡単になったり、`fromJSON` が追加されたりと、高度なことを簡単に描けるようになりました。公式アクションの使い勝手も向上し、体験が良くしてきている印象です。

<a href="https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A4%2F1%2F2020%2Ccd_max%3A6%2F30%2F2020" target="_blank" rel="noopener">当時の記事とかはこちら</a>

#### #### System

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-05-26-github-actions-re-run-successful-jobs/" target="_blank" rel="noopener">ジョブの成功失敗に関わらず re-run できるように</a>。それまではジョブ失敗時しか re-run できなかった

#### #### Workflow

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-15-github-actions-new-workflow-features/" target="_blank" rel="noopener">ジョブ・ワークフローレベルで `shell` と `working_directory` をまとめて指定できる `defaults` が追加</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-15-github-actions-new-workflow-features/" target="_blank" rel="noopener">ジョブ間で簡単にパラメータを渡せる `jobs.<job_id>.outputs` が追加</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-15-github-actions-new-workflow-features/" target="_blank" rel="noopener">ジョブがこけてもワークフローを失敗させない `jobs.<job_id>.continue-on-error` が追加</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-15-github-actions-new-workflow-features/" target="_blank" rel="noopener">JSON オブジェクトをプロパティにバインドする `fromJSON()` メソッドが追加（たとえば動的なマトリックスビルドワークフローが作れる）</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-15-github-actions-new-workflow-features/" target="_blank" rel="noopener">リポジトリの所有者を返す `github.repository_owner` コンテキストが追加</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-15-github-actions-new-workflow-features/" target="_blank" rel="noopener">job id を返す `github.job` が追加</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-15-github-actions-new-workflow-features/" target="_blank" rel="noopener">`jobs.<job_id>.continue-on-error` 適用**前**のステップの結果（成功か失敗かなど）を返す `steps.<step_id>.outcome` コンテキストが追加</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-15-github-actions-new-workflow-features/" target="_blank" rel="noopener">`jobs.<job_id>.continue-on-error` 適用**後**のステップの結果（成功か失敗かなど）を返す `steps.<step_id>.conclusion` コンテキストが追加</a>

#### #### Action

- `FEATURE🚀`: <a href="https://github.com/actions/runner/pull/389" target="_blank" rel="noopener">アクションにおいてステップ実行前に任意のコードを実行する `pre` が利用可能に</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-28-github-actions-v2-artifact-actions/" target="_blank" rel="noopener">actions/upload-artifact アクションにおいて、アップロード時にワイルドカードを利用可能に</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-28-github-actions-v2-artifact-actions/" target="_blank" rel="noopener">actions/upload-artifact アクションにおいて、アーティファクト名（`name`）を省略可能に</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-28-github-actions-v2-artifact-actions/" target="_blank" rel="noopener">actions/download-artifact アクションにおいて、全てのアーティファクトを一括ダウンロード可能に</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-30-github-actions-v2-setup-python-action/" target="_blank" rel="noopener">actions/setup-python アクションにおいて、GitHub-hosted ランナーにプリインストールされてないバージョンのインストールが可能に</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-30-github-actions-v2-setup-python-action/" target="_blank" rel="noopener">actions/setup-python アクションにおいて、セルフホストランナーでのセットアップが容易に</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-05-27-github-actions-v2-cache-actions/" target="_blank" rel="noopener">actions/cache アクションが V2 となった</a>
  - 複数パス、ワイルドカードを使ったパス、単一のファイルパスをキャッシュ可能に
  - @actions/cache npm パッケージが登場。他のアクションからキャッシュが利用可能に
  - Linux/macOS ランナーにおいてパフォーマンスを向上

#### #### Runner

- `FEATURE🚀`, `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2020-04-15-github-actions-sets-the-ci-environment-variable-to-true/" target="_blank" rel="noopener">ランナーの環境変数にデフォルトで `CI=true` がセットされるように</a>

##### ##### Self-hosted runner

- `FEATURE🚀`: <a href="https://github.com/actions/runner/pull/414" target="_blank" rel="noopener">セルフホストランナーに任意のラベルを付与できるカスタムラベルが登場</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-04-22-github-actions-organization-level-self-hosted-runners/" target="_blank" rel="noopener">セルフホストランナーを Organization に対して登録できるように</a>
- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2020-04-20-github-actions-cleanup-dormant-self-hosted-runners/" target="_blank" rel="noopener">30 日間接続されてないセルフホストランナーの登録が削除されるように</a>

#### #### API

- `FEATURE🚀`, `SECURITY🔐`: <a href="https://github.blog/changelog/2020-06-03-personal-access-tokens-workflow-permission/" target="_blank" rel="noopener">パーソナルアクセストークンのスコープに `workflow` が追加</a>。権限を絞れるように

#### #### Management

- `FEATURE🚀`, `SECURITY🔐`: <a href="https://github.blog/changelog/2020-04-21-github-actions-logs-can-now-be-deleted/" target="_blank" rel="noopener">ジョブ実行時のログを削除できるように</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-05-18-github-actions-api-workflow-usage-public-beta/" target="_blank" rel="noopener">Actions API において、`workflow_id`、`run_id` ごとにランナーの Billing Usage を取得できるように（Beta）</a>

#### #### WebUI

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-06-23-github-actions-workflow-templates/" target="_blank" rel="noopener">ワークフローテンプレートが利用可能に</a>。Organization 内リポジトリにおいて WebUI の Actions タブから「New workflow」でテンプレを利用してワークフローを作れるように

<h3 id="2020_3Q">### 3Q</h3>

2020/3Q にあった Changelog を以下にまとめています。

特にワークフローを手動実行できる `workflow_dispatch` はとにかく嬉しかったです。重いワークフローは手動実行したいから手動実行がない GitHub Actions には移行出来ないというところも多かったのではないでしょうか。また、composite action が登場したのもこのころです。それまでは JavaScript でやるか Docker でやるかしかなかったので、結構ハードルが高かったです。

<a href="https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A7%2F1%2F2020%2Ccd_max%3A9%2F30%2F2020" target="_blank" rel="noopener">当時の記事とかはこちら</a>

#### #### Workflow

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-07-06-github-actions-manual-triggers-with-workflow_dispatch/" target="_blank" rel="noopener">ワークフローを手動実行できる `workflow_dispatch` トリガーが追加</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-09-24-github-actions-private-registry-support-for-job-and-service-containers/" target="_blank" rel="noopener">`jobs.<job_name>.container` と `jobs.<job_name>.services` において、プライベートレジストリの Docker イメージを利用できるように</a>

#### #### Action

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-08-07-github-actions-composite-run-steps/" target="_blank" rel="noopener">複合実行アクション（composite action）が登場</a>。同じアクションにおいて複数のシェル言語が使えるように
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-07-02-dependabot-updates-your-github-actions-workflows/" target="_blank" rel="noopener">Dependabot がアクションの更新に対応</a>
- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2020-07-22-github-actions-better-support-for-alternative-default-branch-names/" target="_blank" rel="noopener">actions Organizations 下のアクションのメインブランチが `master` から `main` になった。`actions/cache@master` のようにバージョンを指定していた場合は、`actions/cache@main` に変更しないといけない</a>

#### #### Runner

##### ##### Self-hosted runner

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-08-05-github-actions-enterprise-runners-and-runner-groups/" target="_blank" rel="noopener">セルフホストランナーを Enterprise に対して登録できるように</a>
- `FEATURE🚀`, `SECURITY🔐`: <a href="https://github.blog/changelog/2020-08-05-github-actions-enterprise-runners-and-runner-groups/" target="_blank" rel="noopener">セルフホストランナーをグルーピングできるランナーグループが登場。グループごとにアクセス制御を行えるように</a>

#### #### API

- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2020-09-14-new-limits-affecting-the-checks-api/" target="_blank" rel="noopener">Checks API で利用する Checks において、同じ名前で保存される Check run の数が最大 1000 に制限されるように</a>。超えると古い Check run が削除される

#### #### Management

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-07-07-github-actions-ability-to-delete-workflow-runs/" target="_blank" rel="noopener">workflow runs を削除可能に</a>
- `FEATURE🚀`, `SECURITY🔐`: <a href="https://github.blog/changelog/2020-07-16-github-actions-new-settings-to-enable-actions/" target="_blank" rel="noopener">Enterprise において、リポジトリごとまたは Organization 全体に対して、アクションの利用を制限できるように</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-08-31-org-admins-in-enterprise-accounts-can-view-actions-and-packages-usage-in-the-billing-page/" target="_blank" rel="noopener">Enterprise の管理者（Admin）が WebUI からアクションの Billing usage を確認できるように</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-08-31-create-a-custom-list-of-email-addresses-to-receive-actions-and-packages-billing-threshold-notifications/" target="_blank" rel="noopener">Actions と Packages の Billing Usage の通知しきい値を超えた場合にメールを送るカスタムリストが作成可能に</a>（個人アカウントと Enterprise に所属していない Organization において設定できる）

#### #### WebUI

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-09-23-github-actions-log-improvements/" target="_blank" rel="noopener">ログの読みやすさや検索体験が改善</a>

<h3 id="2020_4Q">### 4Q</h3>

2020/4Q にあった Changelog を以下にまとめています。

大きな BRAKING CHANGE として `set-env`、`add-path` の廃止がありました。多くのユーザが対応を必要とされた BRAKING CHANGE はこれが初めてではないでしょうか。

ワークフローの可視化もこの時期です。複雑にジョブが絡み合う大規模ワークフローになってくるとジョブの依存関係がもう何が何だかわからなくなってくるので、可視化は必須でした。

そして新しい概念として Environments が登場しました。環境ごとにシークレットや承認者を切り替えられます。例えば環境ごとのシークレットを用意して別の名前をつける、内部で切り替えるなどをしなくてもよくなりましたし、権限のある人だけが本番環境にデプロイできるようにもなりました。楽になって安全性も増す良い機能です。

<a href="https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A10%2F1%2F2020%2Ccd_max%3A12%2F31%2F2020" target="_blank" rel="noopener">当時の記事とかはこちら</a>

#### #### System

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-10-01-ability-to-disable-actions-workflows/" target="_blank" rel="noopener">WebUI や API を使用してワークフローを一時的に無効にできるように。一時的に止めたい場合、ワークフローファイルを削除する必要無くなる</a>

#### #### Workflow

- `BRAKING CHANGE💥`, `SECURITY🔐`: <a href="https://github.blog/changelog/2020-10-01-github-actions-deprecating-set-env-and-add-path-commands/" target="_blank" rel="noopener">`set-env`、`add-path` ワークフローコマンドが Deplicated に。ランナーに脆弱性が見つかったため</a>（<a href="https://github.blog/changelog/2020-11-09-github-actions-removing-set-env-and-add-path-commands-on-november-16/" target="_blank" rel="noopener">その後削除</a>）

#### #### Action

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-10-08-github-actions-ability-to-change-retention-days-for-artifacts-and-logs/" target="_blank" rel="noopener">actions/upload-artifact アクションにおいて、アーティファクトとログの保存期間を指定できるように</a>。デフォルトは 90 日。

#### #### Environments

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-12-15-github-actions-environments-environment-protection-rules-and-environment-secrets-beta/" target="_blank" rel="noopener">Environments が登場</a>（<a href="https://github.blog/changelog/2021-06-22-github-actions-environments-environment-protection-rules-and-environment-secrets-are-generally-available/" target="_blank" rel="noopener">その後 GA</a>）。environment ごとにシークレットを用意して切り替えたり、ジョブ実行に必要な承認ができるレビュワーを指定できるようになったり。

#### #### Runner

##### ##### GitHub-hosted runner

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-10-29-github-actions-macos-big-sur-preview/" target="_blank" rel="noopener">macOS Big Sur ランナーがプレビューで利用可能に</a>（<a href="https://github.blog/changelog/2021-08-16-github-actions-macos-11-big-sur-is-generally-available-on-github-hosted-runners/" target="_blank" rel="noopener">その後 GA</a>）
- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-10-29-github-actions-ubuntu-latest-workflows-will-use-ubuntu-20-04/" target="_blank" rel="noopener">Ubuntu ランナーの `ubuntu-latest` が Ubuntu 20.04 を指すように</a>

##### ##### Self-hosted runner

- `BRAKING CHANGE💥`, `SECURITY🔐`: <a href="https://github.blog/changelog/2020-10-16-github-actions-self-hosted-runner-group-access-changes/" target="_blank" rel="noopener">全てのセルフホストランナーグループにおいて「Allow public repositories.」が無効になった</a>。デフォルト値も無効となるように変更。「Allow public repositories.」が無効の状態だとパブリックリポジトリでセルフホストランナーを利用できない。セキュリティ向上のための変更

#### #### Management

- `FEATURE🚀`, `SECURITY🔐`: <a href="https://github.blog/changelog/2020-10-01-github-actions-fine-tune-access-to-external-actions/" target="_blank" rel="noopener">Enterprise、Organization で制限できるアクションの種類を細かく設定できるように（全てのアクションを許可、ローカルアクションのみ許可、指定したアクションのみ許可）</a>

#### #### WebUI

- `FEATURE🚀`: <a href="https://github.blog/changelog/2020-12-08-github-actions-workflow-visualization/" target="_blank" rel="noopener">WebUI においてワークフローが可視化されるように</a>。ジョブ同士の関係がわかりやすくなった

<h2 id="2021">## 2021</h2>

<h3 id="2021_1Q">### 1Q</h3>

2021/1Q にあった Changelog を以下にまとめています。

いよいよ GitHub Enterprise Server ことオンプレ版 GitHub で GitHub Actions が使えるようになりました。ただしセルフホストランナーを建てる必要があります。そうだとしても GHES 勢待望の更新でした。

Dependabot のプルリクエストによってトリガーされるワークフローで使える `GITHUB_TOKEN` のパーミッションが `read-only` になりました。また、シークレットが読めなくなりました。この頃はサプライチェーン攻撃が話題になっていた覚えがあります。この変更によって各所では Dependabot によるプルリクが Check を通らなくなってマージできなくなる事件が相次ぎました((<a href="https://github.community/t/dependabot-prs-and-workflow-secrets/163269/10" target="_blank" rel="noopener">Dependabot PRs and Workflow Secrets - Code to Cloud / GitHub Actions - GitHub Support Community</a>))((<a href="https://shogo82148.github.io/blog/2021/03/17/actions-check-permissions/" target="_blank" rel="noopener">Dependabot が起動する GitHub Actions Workflow から write 権限が無くなった件</a>))（訳あって write なパーミッションが必要なワークフローにかぎる）。

<a href="https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A1%2F1%2F2021%2Ccd_max%3A3%2F31%2F2021" target="_blank" rel="noopener">当時の記事とかはこちら</a>

#### #### System

- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-01-15-the-github-enterprise-server-3-0-release-candidate-is-here/" target="_blank" rel="noopener">GitHub Enterprise Server（オンプレ版）において GitHub Actions が使えるように</a>。セルフホストランナーのみ利用可能
- `FEATURE🚀`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210210#github-actions%3A-skip-pull-request-and-push-workflows-with-%5Bskip-ci%5D---github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-02-08-github-actions-skip-pull-request-and-push-workflows-with-skip-ci/" target="_blank" rel="noopener">コミットメッセージに `[skip ci]` などの文字列を入れることで `push`、`pull_request` トリガーによるワークフロー実行をスキップできるように</a>
- `BRAKING CHANGE💥`, `SECURITY🔐`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210224#github-actions%3A-workflows-triggered-by-dependabot-prs-will-run-with-read-only-permissions---github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-02-19-github-actions-workflows-triggered-by-dependabot-prs-will-run-with-read-only-permissions/" target="_blank" rel="noopener">Dependabot の Pull Request によってトリガーされるワークフローで使える `GITHUB_TOKEN` のパーミッションが read-only に。また、シークレットの読み込み権限は無くなった</a>。サプライチェーン攻撃を防止するため。

#### #### Action

- `BRAKING CHANGE💥`, `SECURITY🔐`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210127#github-actions%3A-short-sha-deprecation---github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-01-21-github-actions-short-sha-deprecation/" target="_blank" rel="noopener">アクションの ref を指定する際に短い SHA（例：`actions/checkout@5a4ac90`）が使えなくなる。セキュリティ向上が理由</a>。

#### #### Environments

- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-02-17-github-actions-limit-which-branches-can-deploy-to-an-environment/" target="_blank" rel="noopener">Environments にデプロイできるブランチを制限できるように</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-02-23-github-actions-environment-environment-protection-rules-and-deployment-reviewers-api/" target="_blank" rel="noopener">Environments の作成や保護ルール設定を API でできるように</a>

#### #### Runner

##### ##### GitHub-hosted runner

- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2021-01-28-github-actions-breaking-change-net-core-3-0-will-be-removed-from-macos-and-ubuntu-images/" target="_blank" rel="noopener">.NET Core 3.0 が macOS ランナーと Ubuntu ランナーから削除</a>

#### #### Management

- `FEATURE🚀`, `SECURITY🔐`: <a href="https://github.blog/changelog/2021-02-23-github-actions-workflow-run-events-are-now-included-in-the-audit-log/" target="_blank" rel="noopener">ワークフローの実行に関する情報やセルフホストランナーのバージョン更新などが Audit Log に記録されるように</a>

<h3 id="2021_2Q">### 2Q</h3>

2021/2Q にあった Changelog を以下にまとめています。

ワークフロー/ジョブの同時実行を制限できるようになりました。デプロイなどの同時に動くとまずいワークフロー/ジョブは同時実行したくないので、待望の機能でした。

今まで同一リポジトリの PR だと read/write となっていた、`GITHUB_TOKEN` のパーミッションをスコープごとに細かく変更できるようになりました。必要な権限のみを渡すことで安全性を高められます。ただし、互換性のためかデフォルトはほぼ全てのスコープで read/write です。個人的にはデフォルト read-only を基本としてほしいです(( 一応リポジトリごと、Org ごとに一括でデフォルト権限を設定できるようにはなってます。))。

外部からの初回プルリクエストに対してワークフローが自動で実行されなくなる変更がありました。これはその頃？に GitHub Actions を悪用した仮想通貨マイニングが問題になってたからです。プールが枯渇して善良なユーザが困るだけでなく、PR を送ってきた fork 先ユーザに責任があるはずですが、fork 元ユーザの責任となってしまうといったことも当時起こっていました。承認が必要になり、ちょっと面倒にはなりましたが、それだけで仮想通貨マイニングを予防できるようになったのでコスパの良い変更だと思いました。

<a href="https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A4%2F1%2F2021%2Ccd_max%3A6%2F30%2F2021" target="_blank" rel="noopener">当時の記事とかはこちら</a>


#### #### System

- `BRAKING CHANGE💥`, `SECURITY🔐`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210428#github-actions-update%3A-helping-maintainers-combat-bad-actors---the-github-blog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-04-22-github-actions-maintainers-must-approve-first-time-contributor-workflow-runs/" target="_blank" rel="noopener">初回のコントリビューター（fork 先）からのプルリクエストに対して自動でワークフローが実行されなくなり、メンテナの承認が必要に</a>。仮想通貨のマイニング対策。

#### #### Workflow

- `FEATURE🚀`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210421#github-actions%3A-limit-workflow-run-or-job-concurrency---github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-04-19-github-actions-limit-workflow-run-or-job-concurrency/" target="_blank" rel="noopener">ワークフロー/ジョブの同時実行を制限する `concurrency` が追加</a>
- `FEATURE🚀`, `SECURITY🔐`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210421#github-actions%3A-control-permissions-for-github_token---github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-04-20-github-actions-control-permissions-for-github_token/" target="_blank" rel="noopener">`GITHUB_TOKEN` の権限を YAML で細かく制御できるように</a>。デフォルトはこれまで通りほぼ全てが write のまま。
- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-06-29-github-discussions-events-available-on-github-actions/" target="_blank" rel="noopener">`discussion` イベント、`discussion-comment` イベントをトリガーにできるように</a>

#### #### Action

- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-04-05-github-actions-setup-java-now-support-adopt-openjdk/" target="_blank" rel="noopener">actions/setup-java アクションが Adopt OpenJDK をサポート</a>

#### #### Runner

##### ##### GitHub-hosted runner

- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2021-05-19-github-actions-hosted-ubuntu-runners-will-only-contain-the-latest-patch-release-for-each-supported-version-of-the-net-sdk/" target="_blank" rel="noopener">Ubuntu ランナーにおいて、.NET SDK の最新パッチリリースのみが含まれるように</a>

#### #### API

- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-05-06-github-actions-beta-api-to-approve-actions-from-forks/" target="_blank" rel="noopener">初回コントリビュータからの PR によるワークフロー実行の承認を API で行えるように（ベータ）</a>

#### #### Manegement

- `FEATURE🚀`, `SECURITY🔐`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210421#github-actions%3A-control-permissions-for-github_token---github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-04-20-github-actions-control-permissions-for-github_token/" target="_blank" rel="noopener">Organization またはリポジトリごとに `GITHUB_TOKEN` の権限を WebUI を使って大まかに制御できるように</a>。`read` と `read/write`（規定値）のどちらか。

<h3 id="2021_3Q">### 3Q</h3>

2021/3Q にあった Changelog を以下にまとめています。

セルフホストランナーを使い捨てできるようになりました。セルフホストランナーはジョブを走らせるごとに環境をクリーンにしてくれません((apt-get でインストールしたコマンドが入ったまま、作ったファイルが置かれたままなど))。ワークフローを冪等に保つため、クリーンアップ処理を入れる必要がありました。セキュリティの観点から、Organization やリポジトリごとにランナーを用意する必要もありました。ジョブを実行したらそのまま登録解除すれば良いのでは？となりますが、仕様上それができないようになっていました。3Q でとうとう使い捨てできるオプションが追加され、こういった煩わしさから解放されました。これはセルフホストランナー勢にとって待望の機能でした。

composite action でとうとう他のアクションを呼び出せるようになりました。それまでは `uses` が使えなかったので再利用性が乏しかったです。これにより同じような `uses` をたくさん書かなくても良くなりました。革命でした。

actions/setup-\* アクションにキャッシュ機能が生え始めました。言語や環境ごとにキャッシュする内容は大体似通っているので、似たような actions/cache を毎回書くのは正直おっくうでした。actions/setup-node を皮切りに各 setup 系アクションにキャッシュ機構が追加されていきます。楽になりました。

<a href="https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A7%2F1%2F2021%2Ccd_max%3A9%2F30%2F2021" target="_blank" rel="noopener">当時の記事とかはこちら</a>

#### #### System

- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-07-01-github-actions-new-settings-for-maintainers/" target="_blank" rel="noopener">初回コントリビュータからの PR によるワークフロー実行の承認について、設定を細かくできるように</a>。（新しい GitHub アカウントかつ初回コントリビュータの場合のみ承認を必要とする、初回コントリビュータに対して承認を必要とする、全ての外部コントリビュータに対して承認を必要とする）

#### #### Action

- `FEATURE🚀`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210825#github-actions%3A-reduce-duplication-with-action-composition-%7C-github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-08-25-github-actions-reduce-duplication-with-action-composition/" target="_blank" rel="noopener">複合実行アクション（composite action）において、他のアクションを呼び出せるように</a>
- `FEATURE🚀`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210707#github-actions%3A-setup-node-now-supports-dependency-caching-%7C-github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-07-02-github-actions-setup-node-now-supports-dependency-caching/" target="_blank" rel="noopener">actions/setup-node アクションがキャッシュをサポート</a>。actions/cache のステップを書かずとも npm/Yarn のキャッシュを利用できるように。
- `FEATURE🚀`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210811#release-support-caching-for-mono-repos-and-repositories-with-complex-structure-%C2%B7-actions%2Fsetup-node" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-09-07-github-actions-setup-node-supports-dependency-caching-for-projects-with-monorepo-and-pnpm-package-manager/" target="_blank" rel="noopener">actions/setup-node アクションのキャッシュ機能がモノレポに対応</a>
- `FEATURE🚀`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210811#release-support-caching-for-mono-repos-and-repositories-with-complex-structure-%C2%B7-actions%2Fsetup-node" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-09-07-github-actions-setup-node-supports-dependency-caching-for-projects-with-monorepo-and-pnpm-package-manager/" target="_blank" rel="noopener">actions/setup-node アクションのキャッシュ機能が pnpm に対応</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-08-30-github-actions-setup-java-now-supports-dependency-caching/" target="_blank" rel="noopener">actions/setup-java アクションがキャッシュをサポート</a>。actions/cache のステップを書かずとも Maven/Gradle のキャッシュを利用できるように。

#### #### Runner

##### ##### GitHub-hosted runner

- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-08-23-github-actions-windows-server-2022-with-visual-studio-2022-is-now-available-on-github-hosted-runners-public-beta/" target="_blank" rel="noopener">Visual Studio 2022 インストール済みの Windows Server 2022 のランナーが Beta で利用できるように</a>（<a href="https://github.blog/changelog/2021-11-16-github-actions-windows-server-2022-with-visual-studio-2022-is-now-generally-available-on-github-hosted-runners/" target="_blank" rel="noopener">その後 GA</a>）
- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2021-04-29-github-actions-ubuntu-16-04-lts-virtual-environment-will-be-removed-on-september-20-2021/" target="_blank" rel="noopener">Ubuntu 16.04 ランナーが削除</a>
- `FEATURE🚀`, `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2021-09-29-github-actions-jobs-running-on-macos-latest-are-now-running-on-macos-big-sur-11/" target="_blank" rel="noopener">macOS ランナーの `macos-latest` がだんだんと macOS Big Sur を指すように</a>。移行期間中、`macos-latest` は macOS Big Sur と macOS Catalina のどちらかを指す（<a href="https://github.com/actions/virtual-environments/issues/4060" target="_blank" rel="noopener">参考</a>）

##### ##### Self-hosted runner

- `FEATURE🚀`, <a href="<https://zenn.dev/korosuke613/articles/productivity-weekly-20210915#support-the---ephemeral-flag-(%23660" target="_blank" rel="noopener">`PW💪`</a>-%C2%B7-actions%2Frunner>): <a href="https://github.blog/changelog/2021-09-20-github-actions-ephemeral-self-hosted-runners-new-webhooks-for-auto-scaling/" target="_blank" rel="noopener">使い捨てのセルフホストランナー（ephemeral runner）が使えるように</a>

#### #### API

- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-09-02-github-actions-filter-workflow-runs-by-created-date/" target="_blank" rel="noopener">API を使った workflow run の検索において、作成日でフィルターをかけられるように</a>
- `FEATURE🚀`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210922#github-actions%3A-ephemeral-self-hosted-runners-%26-new-webhooks-for-auto-scaling-%7C-github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-09-20-github-actions-ephemeral-self-hosted-runners-new-webhooks-for-auto-scaling/" target="_blank" rel="noopener">ワークフローの状態が変わるたびに飛んでくる `workflow_job` webhook が登場</a>。セルフホストランナーのオートスケールがしやすくなった

#### #### Management

- `FEATURE🚀`, `SECURITY🔐`: <a href="https://github.blog/changelog/2021-08-04-github-actions-self-hosted-runner-events-are-now-included-in-the-audit-log/" target="_blank" rel="noopener">監査ログにセルフホストランナー関連のイベントが記録されるように</a>
- `FEATURE🚀`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20210922#github-actions%3A-experience-refresh-for-the-management-of-self-hosted-runners-%7C-github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-09-20-github-actions-experience-refresh-for-the-management-of-self-hosted-runners/" target="_blank" rel="noopener">ランナーグループの管理機能が強化。ランナーグループの管理やランナーの状態の確認が容易に</a>

#### #### WebUI

- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-09-02-github-actions-filter-workflow-runs-by-created-date/" target="_blank" rel="noopener">WebUI を使った workflow run の検索において、作成日でフィルターをかけられるように</a>

<h3 id="2021_4Q">### 4Q</h3>

2021/4Q にあった Changelog を以下にまとめています。

OpenID Connect を利用できるようになりました。これにより、対応クラウドサービスのシークレットを GitHub 上に保存しなくても良くなりました。セキュリティが向上するだけでなくシークレットのローテーションもしなくてよくなりとても良いです。

ワークフローをワークフローから呼び出せるようになりました。ジョブ単位ではアクションを利用すればよかったのですが、ワークフロー単位での再利用はそれまでできませんでした。大規模プロジェクトでは必要となりうる機能です。

2021/1Q に Dependabot によるワークフローで使える `GITHUB_TOKEN` が read-only になってしまい、回避がめんどかったのです。しかし、2021/4Q で Dependabot によるワークフローが、ワークフロー内の `permission` を尊重するようになりました。また、Dependabot もアクセス可能なシークレットを設定できるようになりました。これらによってようやくまともに Dependabot \* Actions を使えるようになりました（セキュリティの強度は落ちてしまいますが...）。

<a href="https://www.google.com/search?q=github+actions&tbs=cdr%3A1%2Ccd_min%3A10%2F1%2F2021%2Ccd_max%3A12%2F31%2F2021" target="_blank" rel="noopener">当時の記事とかはこちら</a>

#### #### System

- `FEATURE🚀`, `SECURITY🔐`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20211108#github-actions%3A-secure-cloud-deployments-with-openid-connect-%7C-github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-10-27-github-actions-secure-cloud-deployments-with-openid-connect/" target="_blank" rel="noopener">OpenID Connect が利用可能に</a>（<a href="https://github.blog/changelog/2021-11-23-secure-cloud-deployments-with-oidc-is-now-ga/" target="_blank" rel="noopener">その後 GA</a>）。クラウドサービス利用をより安全にできるようになった
- `FEATURE🚀`, `SECURITY🔐`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20211013#github-actions%3A-workflows-triggered-by-dependabot-prs-will-respect-permissions-key-in-workflows-%7C-github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-10-06-github-actions-workflows-triggered-by-dependabot-prs-will-respect-permissions-key-in-workflows/" target="_blank" rel="noopener">Dependabot のプルリクエストによってトリガーされるワークフローが `permissions` を尊重するように</a>
- `FEATURE🚀`, `SECURITY🔐`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20211013#github-actions%3A-workflows-triggered-by-dependabot-prs-will-respect-permissions-key-in-workflows-%7C-github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-11-30-github-actions-workflows-triggered-by-dependabot-receive-dependabot-secrets/" target="_blank" rel="noopener">Dependabot の PR によってトリガーされるワークフローが Dependabot のシークレットを読み込めるように</a>

#### #### Workflow

- `FEATURE🚀`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20211006#github-actions%3A-dry-your-github-actions-configuration-by-reusing-workflows-%7C-github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-10-05-github-actions-dry-your-github-actions-configuration-by-reusing-workflows/" target="_blank" rel="noopener">ワークフローの再利用が可能に</a>。同リポジトリでも他のリポジトリでも利用できる
- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-11-24-github-actions-reusable-workflows-are-generally-available/" target="_blank" rel="noopener">再利用可能なワークフローが GA に</a>。ベータ版から以下の改善が行われた
  - 再利用可能なワークフローから呼び出し元ワークフロー内の他のジョブにデータを渡すための output が利用できるように
  - 再利用可能なワークフローに環境変数を渡せるように
  - 利用された再利用可能なワークフローが監査ログに記録されるように
- `FEATURE🚀`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20211117#github-actions%3A-input-types-for-manual-workflows-%7C-github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-11-10-github-actions-input-types-for-manual-workflows/" target="_blank" rel="noopener">`workflow_dispatch` の `inputs` において、入力の種類を選べるように</a>。従来の文字列（`string`）に加え、セレクトボックス（`choice`）、真偽値（`boolean`）、Environments（`environments`）が入力として使えるように
- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-11-01-actions-support-for-branch-protection-changes/" target="_blank" rel="noopener">`branch_protection_rule` トリガーが追加</a>。branch protection の作成、変更、削除時にワークフローを動かせるように

#### #### Action

- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-11-09-github-actions-conditional-execution-of-steps-in-actions/" target="_blank" rel="noopener">複合実行アクション（composite action）内の `step` において `if` が使えるように</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-11-17-debugging-codeql-code-scanning-made-easier-by-retaining-diagnostic-artifacts-in-actions/" target="_blank" rel="noopener">github/codeql-action/init アクションに CodeQL の利用時に問題が発生した際、デバッグを容易にするための設定（`debug`）が追加</a>。有効にすると CodeQL のログや DB などがアーティファクトに保存される。GitHub のサポートから求められる可能性もある
- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-11-23-github-actions-cache-size-is-now-increased-to-10gb-per-repository/" target="_blank" rel="noopener">キャッシュサイズが 5GB から 10GB に増加</a>
- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-11-23-github-actions-setup-python-now-supports-dependency-caching/" target="_blank" rel="noopener">actions/setup-python アクションがキャッシュをサポート</a>。actions/cache のステップを書かずとも pip/pipenv のキャッシュを利用できるように

#### #### Management

- `FEATURE🚀`, `SECURITY🔐`, <a href="https://zenn.dev/korosuke613/articles/productivity-weekly-20211013#github-actions%3A-granular-personal-access-token-scopes-for-self-hosted-runners-in-enterprises-%7C-github-changelog" target="_blank" rel="noopener">`PW💪`</a>: <a href="https://github.blog/changelog/2021-10-11-github-actions-granular-personal-access-token-scopes-for-self-hosted-runners-in-enterprises/" target="_blank" rel="noopener">Enterprise 内でセルフホストランナーを管理する場合に必要な権限のみを持つ `manage_runners:enterprise` スコープが登場</a>。`admin:enterprise` スコープを持つパーソナルアクセストークンが必要なくなった。不要な権限を与えずに済むように
- `FEATURE🚀`: <a href="https://github.blog/changelog/2021-11-25-api-support-for-managing-labels-of-actions-self-hosted-runners/" target="_blank" rel="noopener">API を使ってセルフホストランナーのラベルを管理できるように</a>

<h2 id="2022">## 2022</h2>

<h3 id="2022_1Q">### 1Q</h3>

2022/1Q に起こる予定の Changelog も書いておきます。

#### #### Runner

##### ##### GitHub-hosted runner

- `BRAKING CHANGE💥`: <a href="https://github.blog/changelog/2021-10-19-github-actions-the-windows-2016-runner-image-will-be-removed-from-github-hosted-runners-on-march-15-2022/" target="_blank" rel="noopener">Windows Server 2016 ランナーが 2022/03/15 に削除される予定</a>

<h1 id="end"># おわりに</h1>

GitHub Actions、もう長いこと付き合ってきたつもりでしたが、案外 2019 年中旬に今の形になってたので、まだ 2 年半くらいしか経っていないことに結構驚きました。そうか...まだ 2 年半なのか。

初期の頃はワークフロー可視化がなかったり、手動実行ができなかったり、composite action から composite action が呼び出せなかったり、ジョブの同時実行を制限できなかったりと、大規模プロジェクトでの利用はまだまだ難しそうとか思っていました。が、それらの機能はあっという間に実装されていきました。今回調べてみて改めて機能追加の頻度に驚きました。

それでもまだまだ欲しい機能がたくさんあります。

- <a href="https://github.com/github/roadmap/issues/271" target="_blank" rel="noopener">失敗したジョブのみの Re-Run</a> ← 特にこれ！！
- <a href="https://github.com/github/roadmap/issues/161" target="_blank" rel="noopener">GitHub-hosted runner のインスタンスサイズの拡充</a>
- <a href="https://github.com/github/roadmap/issues/273" target="_blank" rel="noopener">GHES でのキャッシュサポート</a>
- etc..

これらは <a href="https://github.com/github/roadmap/projects/1?card_filter_query=label%3Aactions" target="_blank" rel="noopener">github/roadmap</a> にすでに登録されているので、そのうち実装される予定です。楽しみですね。

今回、とりあえず登場から 2021/12/01 までの GitHub Actions の変更をリストアップしました。

気が向いたらまた更新するかもしれません。

<!-- 記事終わり線 -->

<!-- ここに脚注が来る -->
