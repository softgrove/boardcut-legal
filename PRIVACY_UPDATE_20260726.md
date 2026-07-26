# Boardcut 公開プライバシーポリシー是正記録

作業日: 2026-07-26
対象: `https://softgrove.github.io/boardcut-legal/privacy.html`

## 結論

Boardcut v1.2.0 の匿名利用状況計測と矛盾していた公開プライバシーポリシーを、実装で実際に送信する情報と用途に合わせて是正した。プロジェクト内容は送信しないこと、匿名IDの性質、計測イベント、利用目的、送信先、第三者提供・広告利用・トラッキングをしないことを明記した。

## 根本原因と実装照合

原因は `privacy.html` の旧版が、v1.2.0 で追加された匿名利用状況計測を反映せず、次の断言を残していたこと。

- 旧 `privacy.html:26-27`: 情報を収集・送信しない、analytics はない
- 旧 `privacy.html:38-39`: usage analytics を収集しない
- 旧 `privacy.html:55-56`: 通信は Apple の購入関連だけ
- 旧 `privacy.html:59-60`: 誰からも情報を収集しない
- 旧 `privacy.html:63-64`: analytics、外部サーバはない

実装との照合結果:

- `Boardcut/Telemetry/SGTelemetry.swift:8-17`: 起動、最適化実行、結果表示、Pro案内表示、購入・復元関連のイベント定義
- `Boardcut/Telemetry/SGTelemetry.swift:121-156`: イベントに付加できる値は `trigger` と `sku` に限定。payload は app、version、匿名ID、event、timestamp、properties
- `Boardcut/Telemetry/SGTelemetry.swift:180-190`: 端末上でランダムUUIDを生成・保存
- `Boardcut/Telemetry/SGTelemetry.swift:5-7`: IDFA、広告データ、端末固有ID、ユーザーコンテンツを読まない
- `Boardcut/Telemetry/TelemetryClient.swift:5-9`: 送信先は開発者運用の `https://sg-events.softgrove.workers.dev/e`

## 変更前後

| 箇所 | 変更前 | 変更後 |
|---|---|---|
| 最終更新日 | July 3, 2026 | July 26, 2026 |
| 要約 | 何も収集・送信せず、analytics もない | プロジェクト内容は端末内、限定的な匿名利用状況イベントは開発者サーバへ送信 |
| 収集対象 | usage analytics を収集しない | 起動、最適化実行、結果表示、Pro案内表示、購入タップ・購入完了・復元タップ |
| 匿名ID | 記載なし | 端末でランダム生成したUUID。氏名、メール、アカウント、IDFA、IDFVと関連付けない |
| プロジェクト内容 | 端末保存との記載のみ | 部品名、寸法、在庫、価格、メモ、レイアウト、最適化の入力・結果を送信しないと明記 |
| 目的 | 記載なし | 機能利用と離脱箇所を把握し、Boardcutを改善するため |
| 通信 | Appleの購入関連通信だけ | 匿名イベントは開発者サーバ、購入関連はApple |
| 第三者等 | analytics・外部サーバなし | 第三者提供、販売、広告利用、アプリ・Web横断トラッキングなし。第三者analytics/広告SDKなし |

## 本番URLの実測

### 変更前

- 実測日時: 2026-07-26 15:03:56 JST
- `curl -fsSL https://softgrove.github.io/boardcut-legal/privacy.html?precheck=20260726`
- HTTP 200
- 旧 `Last updated: July 3, 2026`、`There is no ... analytics`、`The only network activity is Apple...`、`no ... external servers` を確認

### 変更後

- privacy変更commit: `a88bff2 Update privacy policy for anonymous usage analytics`
- `origin/main` へのpush成功後、5秒間隔・キャッシュバスター付きでリトライ
- 1〜4回目: HTTP 200だが旧本文
- 5回目の実測日時: 2026-07-26 15:07:48 JST
- 実測結果: HTTP 200
- response header: `last-modified: Sun, 26 Jul 2026 06:07:43 GMT`
- response header: `etag: "6a65a42f-13ea"`
- 本番本文で次を確認:
  - `Last updated: July 26, 2026`
  - `No project content is sent.`
  - `Anonymous usage analytics`
  - `UUID generated at random on your device`
  - `server operated by the developer`
  - `We do not sell or provide these events to third parties`
  - `track you across apps or websites`

## 他アプリで同種の是正が将来必要になるlegalページ

以下の18ページに「収集しない」「analytics はない」とする同種の断言がある。各アプリへ計測を実装するまでは現状が正しいため、今回は一切変更していない。

| アプリ | ローカル該当行 | 公開URL |
|---|---:|---|
| Banneton | `banneton-legal/privacy.html:30-31` | https://softgrove.github.io/banneton-legal/privacy.html |
| Combwise | `combwise-legal/privacy.html:30-31` | https://softgrove.github.io/combwise-legal/privacy.html |
| Coralog | `coralog-legal/privacy.html:30-31` | https://softgrove.github.io/coralog-legal/privacy.html |
| DiveLog | `divelog-legal/privacy.html:30-31` | https://softgrove.github.io/divelog-legal/privacy.html |
| Fibrolog | `fibrolog-legal/privacy.html:26-27,71` | https://softgrove.github.io/fibrolog-legal/privacy.html |
| GoutLog | `goutlog-legal/privacy.html:30-31,50` | https://softgrove.github.io/goutlog-legal/privacy.html |
| Kirairo | `kirairo-legal/privacy.html:30-31` | https://softgrove.github.io/kirairo-legal/privacy.html |
| Menilog | `menilog-legal/privacy.html:26-27,65` | https://softgrove.github.io/menilog-legal/privacy.html |
| MotorLog | `motorlog-legal/privacy.html:30-31` | https://softgrove.github.io/motorlog-legal/privacy.html |
| NerveLog | `nervelog-legal/privacy.html:26-27,69` | https://softgrove.github.io/nervelog-legal/privacy.html |
| Nibful | `nibful-legal/privacy.html:30-31` | https://softgrove.github.io/nibful-legal/privacy.html |
| PawLog | `pawlog-legal/privacy.html:30-31` | https://softgrove.github.io/pawlog-legal/privacy.html |
| PlantLog | `plantlog-legal/privacy.html:30-31` | https://softgrove.github.io/plantlog-legal/privacy.html |
| ReadLog | `readlog-legal/privacy.html:30-31` | https://softgrove.github.io/readlog-legal/privacy.html |
| RefluxLog | `refluxlog-legal/privacy.html:30-31,50` | https://softgrove.github.io/refluxlog-legal/privacy.html |
| Renewlog | `renewlog-legal/privacy.html:26-27` | https://softgrove.github.io/renewlog-legal/privacy.html |
| Settura | `settura-legal/privacy.html:30-31,50` | https://softgrove.github.io/settura-legal/privacy.html |
| Skinote | `skinote-legal/privacy.html:26-27,69` | https://softgrove.github.io/skinote-legal/privacy.html |

## softgrove-site 側の確認

Boardcutのlegalページとは別に、Softgrove公式サイトにも計測実装前の断言が残っている。今回の変更対象外として書き換えていない。

- `softgrove-site/docs/apps/boardcut/index.html:113,115`
  - `Everything you record stays only on your device`
  - `On your iPhone, and nowhere else`
  - `there is no ... cloud backend`
- `softgrove-site/_asc_descs.json:55`
  - `Boardcut collects nothing ... no analytics`
- `softgrove-site/build.py:231` と生成済み `docs/index.html:109`
  - 全アプリ共通で `the privacy policy is one page because there's nothing to disclose`

このうちBoardcut固有の2箇所は、公開プライバシーポリシー修正後も文言上の矛盾が残るため、別タスクで是正が必要。その他アプリの共通文言は、各アプリへの計測実装と同時に更新する。

## 変更範囲

- 変更: `privacy.html`
- 追加: `PRIVACY_UPDATE_20260726.md`
- 変更なし: 他アプリのlegalリポジトリ、`softgrove-site`

## 検証

- `xmllint --html --noout privacy.html`: PASS
- 必須記載22項目の固定文字列チェック: PASS
- 旧断言6パターンの残存チェック: 0件
- `terms.html` のローカルリンク先: 存在
- `git diff --check`: PASS
- privacy変更commitの差分: `privacy.html | 27 insertions(+), 14 deletions(-)`
- 他アプリlegalリポジトリと `softgrove-site`: 変更なし
- `git ls-remote origin refs/heads/main`: privacy変更commit `a88bff208f731c6f76b6807b1e2f653c1fa2bf12` を確認
- 本番URLの新本文: HTTP 200で確認

DONE
