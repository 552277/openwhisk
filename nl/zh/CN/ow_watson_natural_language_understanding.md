---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-15"

keywords: natural language, understanding, watson knowledge studio

subcollection: cloud-functions

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# {{site.data.keyword.nlushort}} 包

{{site.data.keyword.nlufull}} 服务可帮助您大规模地分析文本内容的各种特征。
{: shortdesc}

除了提供文本、原始 HTML 或公共 URL 外，{{site.data.keyword.nlushort}} 还为您提供所请求功能的结果。缺省情况下，此服务会在分析之前清除 HTML 内容，以便结果可以忽略大多数广告和其他不需要的内容。您可以使用 Watson Knowledge Studio 来创建可用于检测 Natural Language Understanding 中定制实体和关系的<a target="_blank" href="https://www.ibm.com/watson/developercloud/doc/natural-language-understanding/customizing.html">定制模型</a>。

{{site.data.keyword.nlushort}} 包中包含以下实体。您可以通过单击实体名称在 {{site.data.keyword.nlushort}} API 参考中找到其他详细信息。

|实体|类型|参数|描述
|
| --- | --- | --- | --- |
|[`natural-language-understanding-v1`](https://www.ibm.com/watson/developercloud/natural-language-understanding/api/v1/?curl.html)|包|`username`、`password`、`iam_access_token`、`iam_apikey`、`iam_url`、`headers`、`headers[X-Watson-Learning-Opt-Out]`、`url`|使用 {{site.data.keyword.nlushort}} 服务。|
|[analyze](https://www.ibm.com/watson/developercloud/natural-language-understanding/api/v1/?curl#analyze)|操作|`username`、`password`、`iam_access_token`、`iam_apikey`、`iam_url`、`headers`、`headers[X-Watson-Learning-Opt-Out]`、`url`、`features`、`text`、`html`、`url`、`clean`、`xpath`、`fallback_to_raw`、`return_analyzed_text`、`language`、`limit_text_characters`|分析文本、HTML 或公共 Web 页面|
|[delete-model](https://www.ibm.com/watson/developercloud/natural-language-understanding/api/v1/?curl#delete-model)|操作|`username`、`password`、`iam_access_token`、`iam_apikey`、`iam_url`、`headers`、`headers[X-Watson-Learning-Opt-Out]`、`url`、`model_id`|删除模型|
|[list-models](https://www.ibm.com/watson/developercloud/natural-language-understanding/api/v1/?curl#list-models)|操作|`username`、`password`、`iam_access_token`、`iam_apikey`、`iam_url`、`headers`、`headers[X-Watson-Learning-Opt-Out]`、`url`|列出模型|

## 创建 {{site.data.keyword.nlushort}} 服务实例
{: #service_instance_understanding}

安装包之前，必须创建 {{site.data.keyword.nlushort}} 服务实例和服务凭证。
{: shortdesc}

1. [创建 {{site.data.keyword.nlushort}} 服务实例 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/catalog/services/natural-language-understanding)。
2. 创建服务实例时，还会为您创建自动生成的服务凭证。

## 安装 {{site.data.keyword.nlushort}} 包
{: #install_understanding}

具有 {{site.data.keyword.nlushort}} 服务实例后，请使用 {{site.data.keyword.openwhisk}} CLI 将 {{site.data.keyword.nlushort}} 包安装到名称空间中。
{: shortdesc}

### 通过 {{site.data.keyword.openwhisk_short}} CLI 进行安装
{: #nlus_cli}

开始之前：
  1. [安装 {{site.data.keyword.Bluemix_notm}} CLI 的 {{site.data.keyword.openwhisk_short}} 插件](/docs/openwhisk?topic=cloud-functions-cloudfunctions_cli#cloudfunctions_cli)。

要安装 {{site.data.keyword.nlushort}} 包，请执行以下操作：

1. 克隆 {{site.data.keyword.nlushort}} 包存储库。
    ```
    git clone https://github.com/watson-developer-cloud/openwhisk-sdk
    ```
    {: pre}

2. 部署包。
    ```
    ibmcloud fn deploy -m openwhisk-sdk/packages/natural-language-understanding-v1/manifest.yaml
    ```
    {: pre}

3. 验证包是否已添加到包列表中。
    ```
    ibmcloud fn package list
    ```
    {: pre}

    输出：
    ```
    packages
    /myOrg_mySpace/natural-language-understanding-v1                        private
    ```
    {: screen}

4. 将所创建的 {{site.data.keyword.nlushort}} 实例中的凭证绑定到包。
    ```
    ibmcloud fn service bind natural-language-understanding natural-language-understanding-v1
    ```
    {: pre}

    示例输出：
    ```
    Credentials 'Credentials-1' from 'natural-language-understanding' service instance 'Watson Natural Language Understanding' bound to 'natural-language-understanding-v1'.
    ```
    {: screen}

5. 验证该包是否已配置为使用 {{site.data.keyword.nlushort}} 服务实例凭证。
    ```
    ibmcloud fn package get natural-language-understanding-v1 parameters
    ```
    {: pre}

    示例输出：
    ```
    ok: got package natural-language-understanding-v1, displaying field parameters
    [
      {
        "key": "__bx_creds",
            "value": {
                "natural-language-understanding": {
            "credentials": "Credentials-1",
            "instance": "Watson Natural Language Understanding",
            "password": "AAAA0AAAAAAA",
            "url": "https://gateway.watsonplatform.net/natural-language-understanding/api",
            "username": "00a0aa00-0a0a-12aa-1234-a1a2a3a456a7"
          }
        }
      }
    ]
    ```
    {: screen}

### 通过 {{site.data.keyword.openwhisk_short}} UI 进行安装
{: #nlus_ui}

1. 在 {{site.data.keyword.openwhisk_short}} 控制台中，转至[“创建”页面 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/openwhisk/create)。

2. 使用 **Cloud Foundry 组织**和 **Cloud Foundry 空间**列表，选择要将包安装到其中的名称空间。名称空间由组合的组织和空间名称构成。

3. 单击**安装包**。

4. 单击 **Watson** 包组。

5. 单击 **Natural Language Understanding** 包。

5. 单击**安装**。

6. 安装包后，会将您重定向到“操作”页面，您可以在其中搜索名为 **natural-language-understanding-v1** 的新包。

7. 要使用 **natural-language-understanding-v1** 包中的操作，必须将服务凭证绑定到这些操作。
  * 要将服务凭证绑定到包中的所有操作，请遵循上面列出的 CLI 指示信息中的步骤 5 和 6。
  * 要将服务凭证绑定到单个操作，请在 UI 中完成以下步骤。**注**：对于要使用的每个操作，必须完成以下步骤。
    1. 单击 **natural-language-understanding-v1** 包中要使用的操作。这将打开该操作的详细信息页面。
    2. 在左侧导航中，单击**参数**部分。
    3. 输入新的**参数**。对于键，输入 `__bx_creds`。对于值，请从先前创建的服务实例中粘贴服务凭证 JSON 对象。

## 使用 {{site.data.keyword.nlushort}} 包
{: #usage_understanding}

要使用此包中的操作，请运行以下格式的命令：

```
ibmcloud fn action invoke natural-language-understanding-v1/<action_name> -b -p <param name> <param>
```
{: pre}

所有操作都需要格式为 YYYY-MM-DD 的版本参数。以向后不兼容方式更改 API 后，会发布一个新版本日期。请在 [API 参考](https://www.ibm.com/watson/developercloud/natural-language-understanding/api/v1/#versioning)中查看更多详细信息。

此包的函数使用当前版本的 Natural Language Understanding (2018-03-16)。请试用 `list-models` 操作。
```
ibmcloud fn action invoke natural-language-understanding-v1/list-models -b -p version 2018-03-16
```
{: pre}
