---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

keywords: logging, monitoring, viewing, logs, query, performance, dashboard, metrics, health

subcollection: cloud-functions

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 記載和監視活動
{: #openwhisk_logs}

{{site.data.keyword.openwhisk}} 內會自動啟用記載和監視，以協助您對問題進行疑難排解，並改進動作的性能和效能。您也可以使用 {{site.data.keyword.cloudaccesstraillong}} 服務來追蹤使用者及應用程式如何與 {{site.data.keyword.openwhisk_short}} 服務互動。

## 視圖日誌
{: #view-logs}

您可以直接從「{{site.data.keyword.openwhisk_short}} 監視」儀表板來檢視啟動日誌。日誌也會轉遞至 [{{site.data.keyword.loganalysisfull}}](/docs/services/CloudLogAnalysis/kibana?topic=cloudloganalysis-analyzing_logs_Kibana#analyzing_logs_Kibana) 來進行檢索，將對所有產生的訊息啟用全文搜尋，並根據特定欄位進行方便的查詢。
{:shortdesc}

**附註**：美國東部地區未提供記載功能。

1. 開啟「[{{site.data.keyword.openwhisk_short}} 監視」頁面 ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://cloud.ibm.com/openwhisk/dashboard)。

2. 選用項目：若要只檢視特定動作的日誌，請將監視摘要限制為該動作。在「過濾選項」區段中，從**限制至**下拉清單中選取動作名稱。

3. 在左側導覽中，按一下**日誌**。即會開啟 {{site.data.keyword.loganalysisshort_notm}} Kibana 頁面。

4. 選用項目：若要查看較舊的日誌，請按一下右上角的**最後 15 分鐘**，並選取不同的時間範圍，來變更預設時間範圍值 15 分鐘。

### 查詢日誌
{: #query-logs}

您可以使用 Kibana 的查詢語法，在 [{{site.data.keyword.loganalysislong_notm}}](/docs/services/CloudLogAnalysis/kibana?topic=cloudloganalysis-analyzing_logs_Kibana#analyzing_logs_Kibana) 中尋找特定啟動日誌。

下列範例查詢可協助您對錯誤進行除錯：
  * 尋找所有錯誤日誌：
      ```
type: user_logs AND stream_str: stderr
```
      {: codeblock}

  * 尋找 "myAction" 產生的所有錯誤日誌：
      ```
type: user_logs AND stream_str: stderr AND action_str: "*myAction"
```
      {: codeblock}

### 查詢結果
{: #query-results}

除了日誌行之外，[{{site.data.keyword.loganalysislong_notm}}](/docs/services/CloudLogAnalysis/kibana?topic=cloudloganalysis-analyzing_logs_Kibana#analyzing_logs_Kibana) 還會檢索由 {{site.data.keyword.openwhisk_short}} 產生的結果或啟動記錄。結果包含啟動 meta 資料，例如啟動期間或啟動結果碼。查詢結果欄位有助於您瞭解 {{site.data.keyword.openwhisk_short}} 動作的行為。

您可以使用 Kibana 的查詢語法來尋找特定的啟動日誌。下列範例查詢可協助您對錯誤進行除錯：

* 尋找所有失敗活動：
    ```
type: activation_record AND NOT status_str: 0
```
    {: codeblock}
    在結果中，`0` 表示順利結束動作，所有其他值則表示錯誤。

* 尋找失敗且發生特定錯誤的所有啟動：
    ```
type: activation_record AND NOT status_str:0 AND message: "*VerySpecificErrorMessage*"
```
    {: codeblock}


## 監視動作效能
{: #monitoring_performance}

瞭解使用 {{site.data.keyword.openwhisk}} 部署之動作的效能。度量值可協助您根據動作持續時間、動作啟動結果或在達到動作啟動限制時，來找到瓶頸或預測可能的正式作業問題。
{: shortdesc}

自動收集所有實體的度量值。根據動作是在 IAM 型還是 Cloud Foundry 型名稱空間中，度量值可能位於 IBM Cloud 帳戶或空間中。這些度量值會傳送至 {{site.data.keyword.monitoringlong}} 並透過 Grafana 提供，而您可以在其中配置儀表板、根據度量值事件值來建立警示，以及其他作業。如需度量值的相關資訊，請參閱 [{{site.data.keyword.monitoringlong_notm}} 文件](/docs/services/cloud-monitoring?topic=cloud-monitoring-getting-started-with-ibm-cloud-monitoring)。

### 建立儀表板
{: #create_dashboard}

透過建立 Grafana 監視儀表板來開始使用。

1. 移至下列其中一個 URL。
  <table>
    <thead>
      <tr>
        <th>{{site.data.keyword.openwhisk_short}} 地區</th>
        <th>監視位址</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>歐盟中部</td>
        <td>metrics.eu-de.bluemix.net</td>
      </tr>
      <tr>
        <td>英國南部</td>
        <td>metrics.eu-gb.bluemix.net</td>
      </tr>
      <tr>
        <td>美國南部</td>
        <td>metrics.ng.bluemix.net</td>
      </tr>
      <tr>
        <td>美國東部</td>
        <td>無法使用</td>
      </tr>
    </tbody>
  </table>

2. 選取度量值網域。
    * IAM 型名稱空間：
        1. 按一下您的使用者名稱。
        2. 在**網域**下拉清單中，選取**帳戶**。
        3. 在**帳戶**下拉清單中，選取 IAM 型名稱空間所在的 IBM Cloud 帳戶。
    * Cloud Foundry 型名稱空間：
        1. 按一下您的使用者名稱。
        2. 在**網域**下拉清單中，選取**空間**。
        3. 使用**組織**及**空間**下拉清單，來選取 Cloud Foundry 型名稱空間。

3. 建立儀表板。
    * 若要使用預先建立的 {{site.data.keyword.openwhisk_short}} 儀表板，請執行下列動作：
        1. 導覽至**首頁 > 匯入**。
        3. 在 **Grafana.net 儀表板**欄位中，輸入預先建立 {{site.data.keyword.openwhisk_short}} 儀表板的 ID：`8124`。
        4. 按一下**匯入**。
    * 若要建立自訂儀表板，請導覽至**首頁 > 建立新的項目**。

執行動作之後，會產生新的度量值，而且可以在 Grafana 中進行搜尋。附註：最多可能需要 10 分鐘，才能在 Grafana 中顯示已執行的動作。


### 度量值格式
{: #metric_format}

這些度量值會反映從每分鐘聚集的動作啟動所收集的資料。可以在動作效能或動作並行層次搜尋度量值。


**動作效能度量值**

動作效能度量值是針對單一動作所計算的值。動作效能度量值同時封裝執行的計時特徵以及啟動的狀態。附註：如果您未在建立期間指定套件名稱，則會使用預設套件名稱。這些度量值採用下列格式：

```
ibmcloud.public.functions.<region>.action.namespace.<namespace>.<package>.<action>.<metric_name>
```
{: codeblock}

下列字元會轉換為橫線 (`-`)：句點 (.)、at 符號 (@)、空格 ( )、'&' 符號 (&)、底線 (_)、冒號 (:)
{: tip}

範例：如果您有名為 `hello-world` 的動作位於 `us-south` 地區的 Cloud Foundry 型名稱空間 `user@email.com_dev` 中，則動作效能度量值會與下列內容類似：

```
ibmcloud.public.functions.us-south.action.namespace.user-ibm-com-dev.action-performance.default.hello-world.duration
```
{: screen}

</br>

**動作並行度量值**

動作並行度量值是根據名稱空間中所有作用中動作的資料計算而來。動作並行包括並行呼叫數目，以及可能會在超出並行限制時發生的系統節流控制。這些度量值採用下列格式：

```
ibmcloud.public.functions.<region>.action.namespace.<namespace>.action-performance.<package>.<action>.<metric_name>
```
{: codeblock}

範例：如果您有名為 `myNamespace` 的 IAM 型名稱空間位於 `us-south` 地區，則動作並行度量值會與下列內容類似：

```
ibmcloud.public.functions.us-south.action.namespace.all.concurrent-invocations
```
{: screen}

</br>

**可用的度量值**

因為您可能會有數千次或數百萬次的動作啟動，所以會依多次啟動所產生的事件聚集來呈現度量值。這些值是以下列方式聚集：
* 總和：所有度量值都會加在一起。
* 平均值：計算算術平均值。
* 加總平均值：根據元件計算算術平均值，並將不同的元件加在一起。

請參閱下表，以查看您可以使用的度量值。

<table>
  <thead>
    <tr>
      <th>度量值名稱</th>
      <th>說明</th>
      <th>類型</th>
      <th>種類</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>duration</code></td>
      <td>平均動作持續時間、已計費的動作執行時間。</td>
      <td>平均值</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>init-time</code></td>
      <td>起始設定動作容器所花費的時間。</td>
      <td>平均值</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>wait-time</code></td>
      <td>佇列中等待排定啟動所花費的平均時間。</td>
      <td>平均值</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>activation</code></td>
      <td>在系統中觸發的啟動總數。</td>
      <td>總和</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>status.success</code></td>
      <td>動作碼的成功啟動次數。</td>
      <td>總和</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>status.error.application</code></td>
      <td>應用程式錯誤所導致的不成功啟動次數。例如，來自動作的正常錯誤。如需如何衍生動作效能度量值的相關資訊，請參閱[瞭解啟動記錄](https://github.com/apache/incubator-openwhisk/blob/master/docs/actions.md#understanding-the-activation-record)。</td>
      <td>總和</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>status.error.developer</code></td>
      <td>開發人員所導致的不成功啟動次數。例如，因動作碼中的無法處理異常狀況而違反[動作 Proxy 介面](https://github.com/apache/incubator-openwhisk/blob/master/docs/actions-new.md#action-interface)。</td>
      <td>總和</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>status.error.internal</code></td>
      <td>{{site.data.keyword.openwhisk_short}} 內部錯誤所導致的不成功啟動次數。</td>
      <td>總和</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>concurrent-rate-limit</code></td>
      <td>因超出並行速率限制而節流控制的啟動總和。如果未達到限制，則不會產生任何度量值。</td>
      <td>總和</td>
      <td><code>action-concurrency</code></td>
    </tr>
    <tr>
      <td><code>timed-rate-limit</code></td>
      <td>因超出每分鐘限制而節流控制的啟動總和。如果未達到限制，則不會產生任何度量值。</td>
      <td>總和</td>
      <td><code>action-concurrency</code></td>
    </tr>
    <tr>
      <td><code>concurrent-invocations</code></td>
      <td>系統中的並行呼叫次數。</td>
      <td>加總平均值</td>
      <td><code>action-concurrency</code></td>
    </tr>
  </tbody>
</table>

預設種類中提供作為預設名稱空間一部分之動作的度量值。
{: tip}



## 監視動作性能
{: #openwhisk_monitoring}

[{{site.data.keyword.openwhisk_short}} 儀表板](https://cloud.ibm.com/openwhisk/dashboard)提供活動的圖形摘要。使用儀表板，以判定 {{site.data.keyword.openwhisk_short}} 動作的效能及性能。
{:shortdesc}

您可以選取要檢視的動作日誌來過濾日誌，並選取所記載活動的時間範圍。這些過濾器會套用至儀表板上的所有視圖。
隨時按一下**重新載入**，以使用最新啟動日誌資料來更新儀表板。

### 活動摘要
{: #activity_summary}

**活動摘要**視圖提供 {{site.data.keyword.openwhisk_short}} 環境的高階摘要。使用此視圖，以監視已啟用 {{site.data.keyword.openwhisk_short}} 之服務的整體性能及效能。從此視圖中的度量值，您可以執行下列動作：
* 檢視呼叫動作的次數，以判定您的服務已啟用 {{site.data.keyword.openwhisk_short}} 的動作的使用率。
* 判定所有動作的整體失敗率。如果您發現錯誤，則可以檢視**活動直方圖**視圖，來找出發生錯誤的服務或動作。檢視**活動日誌**，以找出錯誤本身。
* 檢視與每一個動作相關聯的平均完成時間，以判定動作的效能。

### 活動時間表
{: #timeline}

**活動時間表**視圖會顯示垂直線圖形，以檢視過去及現在動作的活動。紅色指出特定動作內發生錯誤。請產生此視圖與**活動日誌**的關聯，以尋找錯誤的其他詳細資料。



### 活動日誌
{: #log}

這個**活動日誌**視圖顯示啟動日誌的格式化版本。此視圖顯示每次啟動的詳細資料，但會每分鐘輪詢一次來尋找新的啟動。按一下動作來顯示詳細日誌。


若要使用 CLI 來取得顯示在「活動日誌」中的輸出，請使用下列指令：
```
ibmcloud fn activation poll
```
{: pre}
