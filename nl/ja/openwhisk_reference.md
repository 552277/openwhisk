---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-27"

keywords: limits, details, entities, packages, runtimes, semantics, ordering actions

subcollection: cloud-functions

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:deprecated: .deprecated}

# システムの詳細および制限
{: #openwhisk_reference}

以下のセクションでは、{{site.data.keyword.openwhisk}} システムに関する技術的な詳細と、制限設定について説明します。
{: shortdesc}

## {{site.data.keyword.openwhisk_short}} のエンティティー
{: #openwhisk_entities}

### 名前空間とパッケージ
{: #openwhisk_entities_namespaces}

{{site.data.keyword.openwhisk_short}} のアクション、トリガー、およびルールは、名前空間に属し、場合によってはパッケージに属します。

パッケージは、アクションおよびフィードを含むことができます。 パッケージに別のパッケージを含めることはできないため、パッケージのネスティングはできません。 また、エンティティーは必ずしもパッケージに入れる必要はありません。

{{site.data.keyword.Bluemix_notm}} では、組織とスペースのペアが {{site.data.keyword.openwhisk_short}} 名前空間に対応します。 例えば、組織 `BobsOrg` とスペース `dev` は、{{site.data.keyword.openwhisk_short}} の名前空間 `/BobsOrg_dev` に対応します。



[Cloud Foundry 組織およびスペースを作成](/docs/openwhisk?topic=cloud-functions-cloudfunctions_cli#region_info)することにより、新しい Cloud Foundry ベースの名前空間を作成できます。名前空間 `/whisk.system` は、{{site.data.keyword.openwhisk_short}} システムと共に配布されるエンティティー用に予約済みです。


### 完全修飾名
{: #openwhisk_entities_fullyqual}

エンティティーの完全修飾名は、
`/namespaceName/[packageName]/entityName` です。名前空間、パッケージ、およびエンティティーを区切るには、`/` が使用されます。 また、名前空間には接頭部 `/` を付ける必要があります。

利便性のため、ユーザーのデフォルト名前空間の場合は名前空間を省略できます。 例えば、ユーザーのデフォルト名前空間が `/myOrg` であるとします。 以下に、いくつかのエンティティーの完全修飾名と別名の例を示します。



| 完全修飾名 | 別名 | 名前空間 | パッケージ | 名前 |
| --- | --- | --- | --- | --- |
| `/whisk.system/cloudant/read` |  | `/whisk.system` | `cloudant` | `read` |
| `/myOrg/video/transcode` | `video/transcode` | `/myOrg` | `video` | `transcode` |
| `/myOrg/filter` | `filter` | `/myOrg` |  | `filter` |

中でも特に {{site.data.keyword.openwhisk_short}} CLI を使用する場合、この命名体系を使用できます。

### エンティティー名
{: #openwhisk_entities_names}

アクション、トリガー、ルール、パッケージ、および名前空間を含めて、すべてのエンティティーの名前は、次の形式の文字列です。

* 先頭文字は、英数字または下線でなければなりません。
* 後続の文字には、英数字、スペース、または `_`、`@`、`.`、`-` のどの値でも使用できます。
* 最後の文字をスペースにすることはできません。

より正確に言うと、名前は次の (Java メタキャラクター構文で表した) 正規表現に一致する必要があります。`&#xa5;A([&#xa5;w]|[&#xa5;w][&#xa5;w@ .-]*[&#xa5;w@.-]+)&#xa5;z`。

## アクションのセマンティクス
{: #openwhisk_semantics}

以下のセクションでは、{{site.data.keyword.openwhisk_short}} アクションについて詳しく説明します。

### ステートレス
{: #openwhisk_semantics_stateless}

アクションの実装はステートレス、つまり*べき等* です。 システムはこの特性を強制しませんが、アクションによって保持された状態が呼び出しをまたがって使用可能である保証はありません。

さらに、1 つのアクションについて複数のインスタンス化が存在し、それぞれが独自の状態であることもあります。 アクション呼び出しは、これらのインスタンス化のうち任意のものにディスパッチされる可能性があります。

### 呼び出しの入力および出力
{: #openwhisk_semantics_invocationio}

アクションの入力および出力は、キーと値のペアのディクショナリーです。 キーはストリングで、値は有効な JSON 値です。

### アクションの呼び出しの順序付け
{: #openwhisk_ordering}

アクションの呼び出しは順序付けられません。 ユーザーがコマンド・ラインまたは REST API からアクションを 2 回呼び出すと、2 番目の呼び出しが最初の呼び出しよりも前に実行される可能性があります。 それらのアクションに副次作用がある場合、副次作用は任意の順序で発現する可能性があります。

また、アクションが自動的に実行されることは保証されません。 2 つのアクションが並行して実行され、それらのアクションの副次作用が混ざり合う可能性があります。 OpenWhisk は、副次作用に対する特定の並行整合性モデルを保証していません。 並行性の副次作用は実装に依存します。

### アクション実行の保証
{: #openwhisk_atmostonce}

システムは、呼び出し要求を受け取ると、その要求を記録し、アクティベーションをディスパッチします。

システムは、受け取ったことを確認するアクティベーション ID を (非ブロッキング呼び出しと共に) 返します。
ユーザーが HTTP 応答を受け取る前にネットワーク障害またはその他の障害があった場合、{{site.data.keyword.openwhisk_short}} が要求を受け取って処理した可能性があります。

システムがアクションを 1 回呼び出そうとした場合の結果は、次の 4 つのいずれかになります。
- *success*: アクション呼び出しは正常に完了しました。
- *application error*: アクション呼び出しは成功しましたが、アクションは、例えば引数の前提条件が満たされなかったなどの理由で、意図的にエラーを返しました。
- *action developer error*: アクションが呼び出されましたが、異常終了しました。例えば、アクションが例外を検出しなかったり、構文エラーが存在したりしました。
- *whisk internal error*: システムはアクションを呼び出すことができませんでした。
結果は、以下のセクションに示されているように、アクティベーション・レコードの `status` フィールドに記録されます。

正常に受信され、ユーザーに課金される可能性のあるすべての呼び出しには、アクティベーション・レコードがあります。

結果が*action developer error* の場合、アクションは部分的に実行され、外部的に可視の副次作用を生成することがあります。 そういった副次作用が起こったかどうかを確認し、必要であれば再試行ロジックを実行するのは、ユーザーの責任です。 一部の *whisk internal errors* は、アクションが実行を開始したが、アクションが完了を記録する前に失敗したことを示します。

## アクティベーション・レコード
{: #openwhisk_ref_activation}

アクション呼び出しおよびトリガー起動のたびに、アクティベーション・レコードができます。

アクティベーション・レコードには、以下のフィールドが含まれています。

- *activationId* : アクティベーション ID。
- *start* および *end* : アクティベーションの開始と終了を記録するタイム・スタンプ。 値は、[UNIX の時刻形式](http://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap04.html#tag_04_15)です。
- *namespace* および `name`: エンティティーの名前空間および名前。
- *logs*: アクションによってアクティベーション中に生成されるログのストリング配列。 各配列エレメントは、アクションによる `stdout` または `stderr` への行出力に対応し、時刻およびログ出力ストリームを含みます。 構造は `TIMESTAMP STREAM: LOG_OUTPUT` です。
- *response*: キー `success`、`status`、および `result` を定義するディクショナリー。
  - *status*: アクティベーション結果。「success (成功)」、「application error (アプリケーション・エラー)」、「action developer error (アクション開発者エラー)」、「whisk internal error (whisk 内部エラー)」のいずれかの値です。
  - *success*: 状況が `success` の場合のみ `true` です。
- *result*: アクティベーション結果を含むディクショナリー。 アクティベーションが成功した場合、result にはアクションによって返された値が入ります。 アクティベーションが成功しなかった場合、`result` には `error` キーが含まれます。これには通常、失敗の説明が伴います。

## REST API
{: #openwhisk_ref_restapi}

{{site.data.keyword.openwhisk_short}} REST API に関する情報は [REST API リファレンス](https://cloud.ibm.com/apidocs/functions)にあります。

## システム限度
{: #openwhisk_syslimits}

### アクション
{{site.data.keyword.openwhisk_short}} には、1 つのアクションが使用できるメモリー量や、1 分当たりに許可されるアクション呼び出し回数など、いくつかのシステム限度があります。

次の表に、アクションのデフォルト限度を示します。

| 限度 | 説明 | デフォルト | 最小 | 最大 |
| ----- | ----------- | :-------: | :---: | :---: |
| [codeSize](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_codesize) | アクション・コードの最大サイズ (MB)。 | 48 | 1 | 48 |
| [concurrent](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_concurrent) | 実行中または実行用にキューに入れられる、サブミットできるアクティベーションは名前空間当たり N 個までです。 | 1000 | 1 | 1000* |
| [logs](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_logs) | 1 つのコンテナーが N MB を超えて stdout に書き込むことは許可されません。 | 10 | 0 | 10 |
| [memory](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_memory) | 1 つのコンテナーが N MB を超えるメモリーを割り振ることは許可されません。 | 256 | 128 | 2048 |
| [minuteRate](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_minuterate) | 分当たり、名前空間当たりにサブミットできるアクティベーションは N 個までです。 | 5000 | 1 | 5000* |
| [openulimit](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_openulimit) | アクション当たりのオープン・ファイルの最大数。 | 1024 | 0 | 1024 |
| [parameters](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_parameters) | 付加できるパラメーターの最大サイズ (MB)。 | 5 | 0 | 5 |
| [proculimit](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_proculimit) | 1 つのアクションに使用可能なプロセスの最大数。 | 1024 | 0 | 1024 |
| [result](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_result) | アクション呼び出し結果の最大サイズ (MB)。 | 5 | 0 | 5 |
| [sequenceMaxActions](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_sequencemax) | 1 つのシーケンスを構成するアクションの最大数。 | 50 | 0 | 50* |
| [timeout](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_timeout) | 1 つのコンテナーが N ミリ秒より長く実行することは許可されません。 | 60000 | 100 | 600000 |

### 固定限度の引き上げ
{: #increase_fixed_limit}

末尾に (*) が付いている限度値は固定ですが、値を大きくしても安全であるとビジネス・ケースで判断できる場合は増やすことができます。 限度値を増やしたい場合、IBM [{{site.data.keyword.openwhisk_short}} Web コンソール](https://cloud.ibm.com/openwhisk)からチケットを直接オープンすることによって、IBM サポートに連絡してください。
  1. **「サポート」**を選択します。
  2. ドロップダウン・メニューから**「チケットの追加」**を選択します。
  3. チケット・タイプに**「技術的」**を選択します。
  4. サポートの技術的領域に**「機能」**を選択します。

#### codeSize (MB) (固定: 48 MB)
{: #openwhisk_syslimits_codesize}
* 　アクションの最大コード・サイズは 48 MB です。
* JavaScript アクションでは、ツールを使用して、依存項目を含むすべてのソース・コードを連結して単一のバンドル・ファイルにします。
* この限度は固定されており、変更することはできません。

#### concurrent (固定: 1000*)
{: #openwhisk_syslimits_concurrent}
* 実行中または実行用にキューに入れられるアクティベーションの数は名前空間当たり 1000 個を超えることはできません。
* この限度値は固定ですが、値を大きくしても安全であるとビジネス・ケースで判断できる場合は増やすことができます。 この限度を増やす方法について詳しくは、『[固定限度の引き上げ](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#increase_fixed_limit)』セクションを参照してください。

#### logs (MB) (デフォルト: 10 MB)
{: #openwhisk_syslimits_logs}
* ログ限度 N は [0 MB..10 MB] の範囲内であり、アクション当たりで設定されます。
* ユーザーは、アクションが作成または更新されるときにアクション・ログ限度を変更できます。
* 設定された限度を超えるログは切り捨てられるため、新しいログ項目は無視されます。また、設定されたログ限度をアクティベーションが超えたことを示す警告がアクティベーションの最終出力として追加されます。

#### memory (MB) (デフォルト: 256 MB)
{: #openwhisk_syslimits_memory}
* メモリー限度 M は [128 MB..2048 MB] の範囲内であり、アクション当たりの MB 数で設定されます。
* ユーザーは、アクションが作成されるときにメモリー限度を変更できます。
* コンテナーは、限度によって割り振られた量を超えるメモリーを使用することはできません。

#### minuteRate (固定: 5000*)
{: #openwhisk_syslimits_minuterate}
* レート限度 N は 5000 に設定され、1 分の枠内のアクション呼び出し数を制限します。
* この限度を超える CLI 呼び出しまたは API 呼び出しは、HTTP 状況コード `429: TOO MANY REQUESTS` に対応するエラー・コードを受け取ります。
* この限度値は固定ですが、値を大きくしても安全であるとビジネス・ケースで判断できる場合は増やすことができます。 この限度を増やす方法について詳しくは、『[固定限度の引き上げ](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#increase_fixed_limit)』セクションを参照してください。

#### openulimit (固定: 1024:1024)
{: #openwhisk_syslimits_openulimit}
* アクション当たりのオープン・ファイルの最大数は 1024 です (ハード制限とソフト制限の両方)。
* この限度は固定されており、変更することはできません。
* アクションが呼び出されると、docker run コマンドは、引数 `--ulimit nofile=1024:1024` を使用して `openulimit` 値を設定します。
* 詳しくは、[docker run](https://docs.docker.com/engine/reference/commandline/run) コマンド・ライン解説書を参照してください。

#### parameters (固定: 5 MB)
{: #openwhisk_syslimits_parameters}
* アクション/パッケージ/トリガーの作成または更新の全パラメーターのサイズ限度は 5 MB です。
* パラメーターが大き過ぎるエンティティーを作成または更新しようとすると拒否されます。
* この限度は固定されており、変更することはできません。

#### proculimit (固定: 1024:1024)
{: #openwhisk_syslimits_proculimit}
* アクション・コンテナーに使用可能なプロセスの最大数は 1024 です。
* この限度は固定されており、変更することはできません。
* アクションが呼び出されると、docker run コマンドは、引数 `--pids-limit 1024` を使用して `proculimit` 値を設定します。
* 詳しくは、[docker run](https://docs.docker.com/engine/reference/commandline/run) コマンド・ライン解説書を参照してください。

#### result (固定: 5 MB)
{: #openwhisk_syslimits_result}
* アクション呼び出し結果の最大出力サイズ (MB)。
* この限度は固定されており、変更することはできません。

#### sequenceMaxActions (固定: 50*)
{: #openwhisk_syslimits_sequencemax}
* 1 つのシーケンスを構成するアクションの最大数。
* この限度は固定されており、変更することはできません。

#### timeout (ms) (デフォルト: 60s)
{: #openwhisk_syslimits_timeout}
* タイムアウト限度 N は [100 ms..600000 ms] の範囲内であり、アクション当たりのミリ秒で設定されます。
* ユーザーは、アクションが作成されるときにタイムアウト限度を変更できます。
* N ミリ秒を超えて実行したコンテナーは、終了されます。

### トリガー

次の表に示すように、トリガーには分当たりの起動レートの制限が課されます。

| 限度 | 説明 | デフォルト | 最小 | 最大 |
| ----- | ----------- | :-------: | :---: | :---: |
| [minuteRate](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#openwhisk_syslimits_tminuterate) | 分当たり、名前空間当たりに起動できるトリガーは N 個までです。 | 5000* | 5000* | 5000* |

### 固定限度の引き上げ
{: #increase_fixed_tlimit}

末尾に (*) が付いている限度値は固定ですが、値を大きくしても安全であるとビジネス・ケースで判断できる場合は増やすことができます。 限度値を増やしたい場合、IBM [{{site.data.keyword.openwhisk_short}} Web コンソール](https://cloud.ibm.com/openwhisk)からチケットを直接オープンすることによって、IBM サポートに連絡してください。
  1. **「サポート」**を選択します。
  2. ドロップダウン・メニューから**「チケットの追加」**を選択します。
  3. チケット・タイプに**「技術的」**を選択します。
  4. サポートの技術的領域に**「機能」**を選択します。

#### minuteRate (固定: 5000*)
{: #openwhisk_syslimits_tminuterate}

* レート限度 N は 5000 に設定され、1 分の枠内にユーザーが起動できるトリガーの数を制限します。
* ユーザーはトリガーが作成されるときにトリガー限度を変更することはできません。
* この限度を超える CLI 呼び出しまたは API 呼び出しは、HTTP 状況コード `429: TOO MANY REQUESTS` に対応するエラー・コードを受け取ります。
* この限度値は固定ですが、値を大きくしても安全であるとビジネス・ケースで判断できる場合は増やすことができます。 この限度を増やす方法について詳しくは、『[固定限度の引き上げ](/docs/openwhisk?topic=cloud-functions-openwhisk_reference#increase_fixed_tlimit)』セクションを参照してください。
