# Integrating Knowledge Base within the Application

### 1. Creating a Knowledge Base Application

A knowledge base can be used as external knowledge to provide precise answers to user questions through a large language model. You can associate an existing knowledge base with any application type in Dify.

Taking a chat assistant as an example, the process is as follows:

1. Go to **Studio -- Create Application -- Create Chat Assistant**
2. Enter **Context Settings**, click **Add**, and select the already created knowledge base
3. In **Context Settings -- Parameter Settings**, configure the **Recall Strategy**
4. Enable **Citation and Attribution** in **Add Features**
5. In **Debug and Preview**, input user questions related to the knowledge base for debugging
6. After debugging, **Save and Publish** as an AI knowledge base Q&A application

<figure><img src="../../.gitbook/assets/image (187).png" alt=""><figcaption><p>Associating a knowledge base within the application</p></figcaption></figure>

***

### 2. Recall Modes

Enter **Context -- Parameter Settings -- Recall Settings** to choose the recall mode for the knowledge base.

**N-Choose-1 Recall**: Based on user intent and knowledge base description, the LLM autonomously selects the most relevant single knowledge base to query for related text.

**Multi-Path Recall**: Matches all knowledge bases simultaneously based on user intent, querying related text fragments from multiple knowledge bases. After a re-ranking step, the best result matching the user question is selected from the multi-path query results. Requires configuration of the Rerank model API.

<figure><img src="../../.gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>

**How to Choose a Recall Mode**

N-Choose-1 Recall is driven by Function Call/ReAct, where each associated knowledge base acts as a tool function. The LLM autonomously selects the most relevant knowledge base to query based on the semantic match between the user question and the knowledge base description.

The effectiveness of the N-Choose-1 mode is influenced by three main factors:

* **The reasoning capability of the system model:** Some models have unstable adherence to Function Call/ReAct instructions.
* **Clarity of the knowledge base description:** The description content affects the LLM's reasoning about the user question and the related knowledge base.
* **Number of knowledge bases:** Too many knowledge bases can affect the LLM's reasoning accuracy and may exceed the context window length of the reasoning model.

**Recommended Configuration for N-Choose-1 Mode:** Choose a system reasoning model with better performance, associate as few knowledge bases as possible, and provide precise knowledge base descriptions.

When users upload a knowledge base, the system reasoning model automatically generates a summary description for the knowledge base. To achieve the best recall effect in this mode, you can check the system-generated summary description in "Knowledge Base -> Settings -> Knowledge Base Description" to ensure it clearly summarizes the content of the knowledge base.

Below is the technical flowchart for the N-Choose-1 Recall mode:

<figure><img src="../../.gitbook/assets/image (190).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
N-Choose-1 Recall relies on the reasoning capability of the model and has many usage restrictions. The recall strategy for this mode is planned to be adjusted in Q3 2024.
{% endhint %}

#### Multi-Path Recall Mode (Recommended)

In Multi-Path Recall mode, the retriever searches for text content related to the user question across all knowledge bases associated with the application. The results of the multi-path recall are merged, and a subsequent re-ranking (Rerank) step reorders the retrieved documents semantically.

Below is the technical flowchart for the Multi-Path Recall mode:

<figure><img src="https://docs.dify.ai/~gitbook/image?url=https%3A%2F%2F1288284732-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCdDIVDY6AtAz028MFT4d%252Fuploads%252Fgit-blob-9bb237ea9a2b4cc09637e951e696d5b52eb31033%252Fimage.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=0790e257848b5e6c45ce226109aa1c2f5d54bae1c04d1e14dec9fa6a46bdee17" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Multi-Path Recall mode requires the configuration of a Rerank model.
{% endhint %}

The Multi-Path Recall mode does not depend on the reasoning capability of the model or the knowledge base description. This mode can achieve higher quality recall effects when retrieving from multiple knowledge bases. Therefore, it is **recommended to set the recall mode to Multi-Path Recall**.

***

### 3. Re-Ranking (Rerank)

The re-ranking model improves the results of semantic sorting by reordering the candidate document list based on the semantic match between the user question and the documents. It calculates the relevance score between the user question and each candidate document and returns a list of documents sorted by relevance from high to low.

<figure><img src="../../.gitbook/assets/image (128).png" alt=""><figcaption><p>Hybrid Retrieval + Re-Ranking</p></figcaption></figure>

{% hint style="info" %}
For more information about Rerank, please refer to the extended reading [Re-Ranking](integrate\_knowledge\_within\_application.md#zhong-pai-xu-rerank).
{% endhint %}

#### How to Configure the Rerank Model?

Dify currently supports the Cohere Rerank model. Enter the "Model Provider -> Cohere" page and fill in the Rerank model's API key:

<figure><img src="../../.gitbook/assets/image (112).png" alt=""><figcaption><p>Configuring the Cohere Rerank model in the model provider</p></figcaption></figure>

How to obtain the Cohere Rerank model?

Log in to [https://cohere.com/rerank](https://cohere.com/rerank), register on the page, apply for the usage qualification of the Rerank model, and obtain the API key.

{% hint style="info" %}
Besides supporting the Cohere Rerank API, you can also use local inference frameworks like Ollama and Xinference to deploy local Rerank models such as bge-reranker.
{% endhint %}

#### Setting the Rerank Model

Go to the "Dataset -> Create Dataset -> Retrieval Settings" page and add the Rerank settings. In addition to setting Rerank when creating a dataset, you can also change the Rerank configuration in the settings of an already created dataset. Change the Rerank configuration in the recall mode settings of the application orchestration dataset.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption><p>Setting the Rerank model in the dataset retrieval mode</p></figcaption></figure>

**TopK**: Used to set the number of relevant documents returned after Rerank.

**Score Threshold**: Used to set the minimum score of relevant documents returned after Rerank. After setting the Rerank model, the TopK and Score Threshold settings only take effect in the Rerank step.

When setting the recall mode to Multi-Path Recall in the "Prompt Orchestration -> Context -> Settings" page, you need to enable the Rerank model.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Setting the Rerank model in the dataset multi-path recall mode</p></figcaption></figure>