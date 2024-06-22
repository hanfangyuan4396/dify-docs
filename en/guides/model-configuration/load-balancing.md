# Load Balancing

Model rate limits are restrictions imposed by model providers on the number of times a user or customer can access API services within a specified period. These limits help prevent abuse or misuse of the API, ensure fair access for all users, and control the overall load on the infrastructure.

In enterprise-level large-scale model API calls, high concurrent requests can exceed rate limits and affect user access. Load balancing can distribute API requests across multiple endpoints to ensure all users receive the fastest response and the highest model call throughput, thereby ensuring stable business operations.

You can enable this feature in **Model Provider -- Model List -- Configure Model Load Balancing** and add multiple credentials (API keys) for the same model.

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt="" width="563"><figcaption><p>Model Load Balancing</p></figcaption></figure>

{% hint style="info" %}
Model load balancing is a paid feature. You can enable this feature by [subscribing to SaaS paid services](../../getting-started/cloud.md#subscription-plans) or purchasing the enterprise edition.
{% endhint %}

The default API Key in the configuration is the credential added when the model provider was initially configured. You need to click **Add Configuration** to add different API keys for the same model to properly use the load balancing feature.

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt="" width="563"><figcaption><p>Configure Load Balancing</p></figcaption></figure>

**You need to add at least one additional model credential** to save and enable load balancing.

You can also **temporarily disable** or **delete** configured credentials.

<figure><img src="../../.gitbook/assets/image (7).png" alt="" width="563"><figcaption></figcaption></figure>

After configuration, all models with load balancing enabled will be displayed in the model list.

<figure><img src="../../.gitbook/assets/image (6).png" alt="" width="563"><figcaption><p>Enable Load Balancing</p></figcaption></figure>

{% hint style="info" %}
By default, load balancing uses the Round-robin strategy. If a rate limit is triggered, a 1-minute cooldown period will be applied.
{% endhint %}

You can also configure load balancing from **Add Model**, and the configuration process is the same as described above.

<figure><img src="../../.gitbook/assets/image (4).png" alt="" width="563"><figcaption><p>Configure Load Balancing from Add Model</p></figcaption></figure>