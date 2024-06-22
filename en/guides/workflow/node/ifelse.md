# IF/ELSE

### Definition

Allows you to split the workflow into two branches based on if/else conditions.

A conditional branching node has three parts:

* IF Condition: Select a variable, set the condition, and specify the value that satisfies the condition.
* IF condition evaluates to `True`, execute the IF path.
* IF condition evaluates to `False`, execute the ELSE path.

**Condition Types**

* Contains
* Not contains
* Starts with
* Ends with
* Is
* Is not
* Is empty
* Is not empty

***

### Scenario

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Taking the above **Text Summary Workflow** as an example:

* IF Condition: Select the `summarystyle` variable from the start node, with the condition **Contains** `technical`.
* IF condition evaluates to `True`, execute the IF path, querying technical-related knowledge through the Knowledge Retrieval node and then responding via the LLM node (upper part of the diagram).
* IF condition evaluates to `False`, i.e., the `summarystyle` variable input **does not contain** `technical`, execute the ELSE path, responding via the LLM2 node (lower part of the diagram).

**Multiple Condition Judgments**

For complex condition judgments, you can set multiple condition judgments and configure **AND** or **OR** between conditions to take the **intersection** or **union** of the conditions, respectively.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="369"><figcaption><p>Multiple Condition Judgments</p></figcaption></figure>