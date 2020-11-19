* section 2：the importance of budget and deadline for e-Science in the cloud  

* section 3：an overview of existing approaches to scheduling workflows

* section 4：the basic elements of the scheduling problem  

* section 5：the BDAS algorithm  

* section 6：the basis for the evaluation and defines the metrics used in this work.  

* section 7：the experimental results for BDAS and three other scheduling algorithms over five representative scientific workflows.  

  

#### 问题定义

Workflows are the most widely used models for representing and managing complex distributed scientific computations . A Directed Acyclic Graph (DAG) is the most common abstraction of a workflow.   

Using a DAG abstraction, a workflow is defined as a graph **G = (T, E)** where **T = {$t_{0}$, $t_{1}$,..., $t_{n}$,}** is a set of tasks represented by verticesand **E = {$t_{i, j}$, | $t_{i}$, $t_{j}$ $\in$ T}** is a set of directed edges denoting data or control dependencies between tasks.  

Workflows are executed on different instance types, and each instance type is associated with a set of resources.  

We assume that cloud vendors provide access to unlimited number of instances and the instances are heterogeneous (denoted by **P = {$p_{0}$, $p_{1}$,..., $p_{h}$,}** , where h is thei ndex of the instance type). We also assume that all instances and storage services are located in the same region and also assume that the average bandwidth between the instances is essentially identical.  



Each level can therefore be thought of as a bag of tasks (BoT) containing a set of independent tasks.  



Each level gets its own level deadline and budget and all tasks in the same level have the same level-deadline and level-budget.  



#### THE BDAS ALGORITHM

* Workflow partitioning： The workflow is partitioned into dependency free bags of tasks, called levels.
* Budget Distribution： The user-defined budget (B) is then allocated to each defined level using ”All in” strategy.
* Deadline Distribution： The user-defined deadline (D) is divided and distributed between levels. Each level gets its own level deadline. All tasks in the same level, have the same level-deadline.
* Task Selection： A task is selected based on its priority in the ready list for execution.
* Instance Selection： In this phase, we introduce new trade-off between Cost and Time. We find the best combination of cost and time to select the most suitable instance.  























