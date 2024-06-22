### Literal Translation
# Knowledge Base

The training data for large language models is generally based on publicly available data, and each training session requires a significant amount of computational power. This means that the knowledge of the models generally does not include private domain knowledge, and there is a certain delay in the public knowledge domain. To solve this problem, the current common solution is to use RAG (Retrieval-Augmented Generation) technology, which uses users' questions to match the most relevant external data, and after retrieving the relevant content, reorganize and insert the response back as the context of the model prompt.

{% hint style="info" %}
To learn more, please check the extended reading on [Retrieval-Augmented Generation (RAG)](../../learn-more/extended-reading/retrieval-augment/)
{% endhint %} 
<!-- TODO: check link -->

Dify's knowledge base feature visualizes each step in the RAG pipeline, providing a simple and easy-to-use user interface to help application builders in managing personal or team knowledge bases, and quickly integrating them into AI applications. You only need to prepare text content, such as:

* Long text content (TXT, Markdown, DOCX, HTML, JSONL, or even PDF files)
* Structured data (CSV, Excel, etc.)

Additionally, we are gradually supporting synchronizing data from various data sources to datasets, including:

* Web pages
* Notion
* Github
* Databases
* ……

{% hint style="info" %}
**Scenario**: If your company wants to establish an AI customer service assistant based on the existing knowledge base and product documentation, you can upload the documents to the dataset in Dify and build a chatbot. In the past, this might have taken you weeks and been difficult to maintain continuously.
{% endhint %}

### Knowledge Base and Documents

In Dify, Knowledge is a collection of documents. A knowledge base can be integrated into an application as a retrieval context. Documents can be uploaded by developers or a member of operation team, or synchronized from other data sources (usually corresponding to one unit file in the data source).