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

# 日志记录和监视活动
{: #openwhisk_logs}

日志记录和监视功能在 {{site.data.keyword.openwhisk}} 中会自动启用，可帮助您对问题进行故障诊断，并提高操作的运行状况和性能。您还可以使用 {{site.data.keyword.cloudaccesstraillong}} 服务来跟踪用户和应用程序如何与 {{site.data.keyword.openwhisk_short}} 服务进行交互。

## 查看日志
{: #view-logs}

可以直接在“{{site.data.keyword.openwhisk_short}} 监视”仪表板中查看激活日志。这些日志还会转发到 [{{site.data.keyword.loganalysisfull}}](/docs/services/CloudLogAnalysis/kibana?topic=cloudloganalysis-analyzing_logs_Kibana#analyzing_logs_Kibana)（在其中对这些日志建立索引），从而支持对生成的所有消息进行全文搜索，并根据特定字段方便地执行查询。
{:shortdesc}

**注**：对于美国东部区域，日志记录不可用。

1. 打开 [{{site.data.keyword.openwhisk_short}} 监视页面 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/openwhisk/dashboard)。

2. 可选：要仅查看特定操作的日志，请将监视摘要限制为该操作。在“过滤选项”部分中，从**限制为**下拉列表中选择操作名称。

3. 在左侧导航中，单击**日志**。这将打开 {{site.data.keyword.loganalysisshort_notm}} Kibana 页面。

4. 可选：要查看较旧的日志，请通过单击右上角的**最近 15 分钟**并选择其他时间范围来更改缺省时间范围值 15 分钟。

### 查询日志
{: #query-logs}

可以使用 Kibana 的查询语法在 [{{site.data.keyword.loganalysislong_notm}}](/docs/services/CloudLogAnalysis/kibana?topic=cloudloganalysis-analyzing_logs_Kibana#analyzing_logs_Kibana) 中查找特定激活日志。

以下示例查询可帮助您调试错误：
  * 查找所有错误日志：
      ```
type: user_logs AND stream_str: stderr
```
      {: codeblock}

  * 查找由“myAction”生成的所有错误日志：
      ```
type: user_logs AND stream_str: stderr AND action_str: "*myAction"
```
      {: codeblock}

### 查询结果
{: #query-results}

除了日志行外，[{{site.data.keyword.loganalysislong_notm}}](/docs/services/CloudLogAnalysis/kibana?topic=cloudloganalysis-analyzing_logs_Kibana#analyzing_logs_Kibana) 还会对 {{site.data.keyword.openwhisk_short}} 生成的结果或激活记录建立索引。结果包含激活元数据，例如激活持续时间或激活结果代码。查询结果字段可帮助您了解 {{site.data.keyword.openwhisk_short}} 操作的行为方式。

可以使用 Kibana 的查询语法查找特定激活日志。以下示例查询可帮助您调试错误：

* 查找所有失败的激活：
    ```
type: activation_record AND NOT status_str: 0
```
    {: codeblock}
    在结果中，`0` 指示已成功退出的操作，其他所有值均指示错误。

* 查找由于特定错误而失败的所有激活：
    ```
type: activation_record AND NOT status_str:0 AND message: "*VerySpecificErrorMessage*"
```
    {: codeblock}


## 监视操作的性能
{: #monitoring_performance}

了解使用 {{site.data.keyword.openwhisk}} 部署的操作的性能。度量值可以帮助您根据操作持续时间或操作激活结果，或者在达到操作激活限制时，查找瓶颈或预测可能的生产问题。
{: shortdesc}

系统将自动收集所有实体的度量值。根据操作是位于基于 IAM 还是位于基于 Cloud Foundry 的名称空间中，度量值会相应位于 IBM Cloud 帐户或空间中。这些度量值会发送到 {{site.data.keyword.monitoringlong}}，并通过 Grafana 提供，在 Grafana 中可以配置仪表板，基于度量值事件值创建警报等。有关度量值的更多信息，请参阅 [{{site.data.keyword.monitoringlong_notm}} 文档](/docs/services/cloud-monitoring?topic=cloud-monitoring-getting-started-with-ibm-cloud-monitoring)。

### 创建仪表板
{: #create_dashboard}

首先创建 Grafana 监视仪表板。

1. 转至下列其中一个 URL。
  <table>
    <thead>
      <tr>
        <th>{{site.data.keyword.openwhisk_short}} 区域</th>
        <th>监视地址</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>欧洲中部</td>
        <td>metrics.eu-de.bluemix.net</td>
      </tr>
      <tr>
        <td>英国南部</td>
        <td>metrics.eu-gb.bluemix.net</td>
      </tr>
      <tr>
        <td>美国南部</td>
        <td>metrics.ng.bluemix.net</td>
      </tr>
      <tr>
        <td>美国东部</td>
        <td>不可用</td>
      </tr>
    </tbody>
  </table>

2. 选择度量值域。
    * 基于 IAM 的名称空间：
        1. 单击您的用户名。
        2. 在**域**下拉列表中，选择**帐户**。
        3. 在**帐户**下拉列表中，选择基于 IAM 的名称空间所在的 IBM Cloud 帐户。
    * 基于 Cloud Foundry 的名称空间：
        1. 单击您的用户名。
        2. 在**域**下拉列表中，选择**空间**。
        3. 使用**组织**和**空间**下拉列表来选择基于 Cloud Foundry 的名称空间。

3. 创建仪表板。
    * 要使用预先创建的 {{site.data.keyword.openwhisk_short}} 仪表板，请执行以下操作：
        1. 导航至**主页 > 导入**。
        3. 在 **Grafana.net 仪表板**字段中，输入预先创建的 {{site.data.keyword.openwhisk_short}} 仪表板的标识 `8124`。
        4. 单击**导入**。
    * 要创建定制仪表板，请导航至**主页 > 创建新项**。

执行操作后，将生成新的度量值并可在 Grafana 中搜索这些度量值。注：已执行的操作可能需要最多 10 分钟时间才会在 Grafana 中显示。


### 度量值格式
{: #metric_format}

度量值反映了从操作激活收集的数据，这些数据每分钟聚集一次。度量值可在操作性能或操作并行级别进行搜索。


**操作性能度量值**

操作性能度量值是针对单个操作计算的值。操作性能度量包含执行的计时特征和激活的状态。注：如果在创建期间未指定包的名称，那么将使用缺省包名。这些度量值将采用以下格式：

```
ibmcloud.public.functions.<region>.action.namespace.<namespace>.<package>.<action>.<metric_name>
```
{: codeblock}

以下字符会转换为短划线 (`-`)：句点 (.)、@ 符号、空格 ( )、& 符号、下划线 (_) 和冒号 (:)
{: tip}

示例：如果在 `us-south` 区域中基于 Cloud Foundry 的名称空间 `user@email.com_dev` 中具有名为 `hello-world` 的操作，那么操作性能度量值将类似于以下内容：

```
ibmcloud.public.functions.us-south.action.namespace.user-ibm-com-dev.action-performance.default.hello-world.duration
```
{: screen}

</br>

**操作并行度量值**

操作并行度量值是根据名称空间中所有活动操作中的数据计算的。操作并行包括并行调用数和超出并行限制时可能发生的系统调速数。这些度量值将采用以下格式：

```
ibmcloud.public.functions.<region>.action.namespace.<namespace>.action-performance.<package>.<action>.<metric_name>
```
{: codeblock}

示例：如果在 `us-south` 区域中具有名为 `myNamespace` 的基于 IAM 的名称空间，那么操作并行度量值将类似于以下内容：

```
ibmcloud.public.functions.us-south.action.namespace.all.concurrent-invocations
```
{: screen}

</br>

**可用度量值**

由于您可能有数千个或数百万个操作激活，因此度量值会表示为由许多激活生成的事件的聚集。这些值通过以下方式进行聚集：
* 和：将所有度量值加在一起。
* 平均值：计算算术平均值。
* 平均值总和：根据组成部分计算算术平均值，并将不同的组成部分加在一起。

请查看下表以了解可供您使用的度量值。

<table>
  <thead>
    <tr>
      <th>度量值名称</th>
      <th>描述</th>
      <th>类型</th>
      <th>类别</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>duration</code></td>
      <td>平均操作持续时间，即记帐操作执行时间。</td>
      <td>平均值</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>init-time</code></td>
      <td>初始化操作容器所用的时间。</td>
      <td>平均值</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>wait-time</code></td>
      <td>在队列中等待安排激活所用的平均时间。</td>
      <td>平均值</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>activation</code></td>
      <td>系统中触发的总体激活数。</td>
      <td>和</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>status.success</code></td>
      <td>操作代码的成功激活数。</td>
      <td>和</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>status.error.application</code></td>
      <td>因应用程序错误而导致的失败激活数。例如，来自操作的正常错误。有关如何派生 action-performance 度量值的更多信息，请查看[了解激活记录](https://github.com/apache/incubator-openwhisk/blob/master/docs/actions.md#understanding-the-activation-record)。</td>
      <td>和</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>status.error.developer</code></td>
      <td>因开发者而导致的失败激活数。例如，操作代码中未处理的异常导致的[操作代理接口](https://github.com/apache/incubator-openwhisk/blob/master/docs/actions-new.md#action-interface)违例。</td>
      <td>和</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>status.error.internal</code></td>
      <td>因 {{site.data.keyword.openwhisk_short}} 内部错误而导致的失败激活数。</td>
      <td>和</td>
      <td><code>action-performance</code></td>
    </tr>
    <tr>
      <td><code>concurrent-rate-limit</code></td>
      <td>由于超过并行速率限制而调速的激活的总和。如果未达到限制，那么不会发出任何度量值。</td>
      <td>和</td>
      <td><code>action-concurrency</code></td>
    </tr>
    <tr>
      <td><code>timed-rate-limit</code></td>
      <td>由于超过每分钟限制而调速的激活的总和。如果未达到限制，那么不会发出任何度量值。</td>
      <td>和</td>
      <td><code>action-concurrency</code></td>
    </tr>
    <tr>
      <td><code>concurrent-invocations</code></td>
      <td>系统中的并行调用数。</td>
      <td>平均值总和</td>
      <td><code>action-concurrency</code></td>
    </tr>
  </tbody>
</table>

作为缺省名称空间一部分的操作的度量值在缺省类别中提供。
{: tip}



## 监视操作的运行状况
{: #openwhisk_monitoring}

[{{site.data.keyword.openwhisk_short}}“仪表板”](https://cloud.ibm.com/openwhisk/dashboard)提供了对您活动的图形化摘要。使用该仪表板可确定 {{site.data.keyword.openwhisk_short}} 操作的性能和运行状况。
{:shortdesc}

可以通过选择要查看的操作日志，然后选择记录的活动的时间范围来过滤日志。这些过滤器会应用于仪表板上的所有视图。
随时单击**重新装入**可使用最新的活动日志数据更新该仪表板。

### 活动摘要
{: #activity_summary}

**活动摘要**视图提供了您的 {{site.data.keyword.openwhisk_short}} 环境的高级别摘要。使用此视图可监视启用了 {{site.data.keyword.openwhisk_short}} 的服务的总体运行状况和性能。通过此视图中的度量值，可以执行以下操作：
* 对于服务的启用了 {{site.data.keyword.openwhisk_short}} 的操作，通过查看调用这些操作的次数，确定这些操作的使用率。
* 确定所有操作中的总体失败率。如果发现错误，可以通过查看**活动直方图**视图来确定哪些服务或操作发生了错误。通过查看**活动日志**来确定错误本身。
* 通过查看与每个操作关联的平均完成时间，确定操作的执行情况。

### 活动时间线
{: #timeline}

**活动时间线**视图显示垂直条形图，用于查看过去和现在操作的活动情况。红色指示特定操作中有错误。将此视图与**活动日志**相关联，可了解有关错误的更多详细信息。



### 活动日志
{: #log}

此**活动日志**视图显示已设置格式的激活日志版本。此视图显示每个激活的详细信息，并且一分钟会轮询一次，以确定是否有新的激活。单击某个操作可显示详细日志。

要使用 CLI 获取“活动日志”中显示的输出，请使用以下命令：
```
ibmcloud fn activation poll
```
{: pre}
