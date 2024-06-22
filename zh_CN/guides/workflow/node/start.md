# 开始

### 定义

定义一个工作流程启动的初始参数。

你可以在开始节点中自定义启动工作流的输入变量。每一个工作流都需要一个开始节点。

<figure><img src="../../../.gitbook/assets/image (236).png" alt="" width="375"><figcaption><p>工作流开始节点</p></figcaption></figure>

在开始节点内定义输入变量支持四种类型：

* 文本
* 段落
* 下拉选项
* 数字
* 文件（即将支持）

<figure><img src="../../../.gitbook/assets/output (2) (1).png" alt=""><figcaption><p>配置开始节点变量</p></figcaption></figure>

配置完成后，在工作流执行时会要求输入开始节点中定义的变量值。

<figure><img src="../../../.gitbook/assets/output (3) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
提示：在 Chatflow 中，开始节点会提供系统内置变量：`sys.query` 和 `sys.files`

`sys.query` 用于对话型应用中的用户问题输入。

`sys.files` 用于对话中上传文件，如上传一张图片，需要配合图像理解模型使用。
{% endhint %}
