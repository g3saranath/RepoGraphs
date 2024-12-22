# Top Open Source AI GitHub repositories, Visualized as Code Graphs using FalkorDB Code-graph
I recently had the opportunity to work with the Code-Graph repository from FalkorDB, and I would like to share my experience. The repository offers a powerful tool for generating visual representations of codebases, effectively transforming them into knowledge graphs. This capability is particularly useful for exploring various entities in code, such as functions, classes, and variables, along with their interrelationships.

With increase in expansion of codebases in size and complexity, maintaining, reproducing and understanding becomes challenging. Larger projects often involve hundreds or thousands of interconnected components, limiting: 
- Tracking dependencies across modules.
- Identifying technical bugs, and challenges in reproducibility. 
- Ensuring changes don’t inadvertently break functionality elsewhere in the code. 

## Why use Code Graphs ?

In such scenarios, code graphs become indispensable. Code graphs help visualize the structure and lineage of a complex code base displaying elements such as files, classes, functions in a graph format such that we could understand:
1. Specific components of a large code base
2. Identify the requirements and dependencies
3. Debugging and refactoring made easy
4. Managing large code bases effectively

In this article, we’ll generate code graphs for popular open-source AI repositories to highlight their structure and showcase how FalkorDB could be used to facilitate these visualizations. We will also look into the interesting Graph RAG application provided by the FalkorDB to query through the repository to identify dependencies and required components. 

## Setting Up FalkorDB for Code Graph Generation
Following are the steps to setup FalkorDB, code-graph to analyze open-source repositories as graphs. 

1. Cloning the repositories 
```
git clone --recurse-submodules -j8 https://github.com/FalkorDB/FalkorDB.git
git clone https://github.com/FalkorDB/code-graph.git
```

2. Create Conda Environment
```
conda create -n falkor
conda activate falkor
pip install -r FalkorDB/tests/requirements.txt
```

3. If you don't have Node.js, install using the following commands.
```
# check your current version
node -v

#Update Node.js using Node Version Manager (nvm), install it first if you don't have it. 
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
source ~/.bashrc

#Then, let us install and use the latest Node.js version:
nvm install node
nvm use node
```

4. You would have cloned to the code-graph repository in Step 1, change directory to `code-graph/ `
```
cd code-graph/
# checkout to the active directory, in this version we are checking out to `flask`
git checkout flask
```
Once you check out to the branch, install the npm libraries using:
`npm install`
`npm install next@latest`
`npm install openai`

5. Now, let us run FalkorDB using a Docker in a <em>new terminal</em>
`docker run -p 6379:6379 -it --rm falkordb/falkordb`

6. In case you want to try out the GraphRAG option in FalkorDB to query the graph created based on the open-source repository, you will need to set up an OpenAI API Key. This could be created in [API KEY](https://platform.openai.com/settings/organization/api-keys). Now in your previous command line, 
`export OPENAI_API_KEY=YOUR_OPENAI_API_KEY`

7. Create your `.env.local ` file using the following template. 
```
NEXT_PUBLIC_MODE=UNLIMITED # Optional values LIMITED/UNLIMITED
FALKORDB_URL=redis://localhost:6379 # FALKORDB_URL
OPENAI_API_KEY=YOUR_API_KEY #Paste your API key here
```

8. Now you are ready to launch the FalkorDB code-graph from the code-graph/ directory.
`npm run dev`
![Port obtained from CLI](image.png)
This command will run and host your code graph in browser served at port, you will receive this port information in your command line. Launch this URL - http://localhost/PORT/ and it will prompt for the GitHub URL of the open-source repository you want to analyze and generate using code-graph. 

After you launch the code-graph at the URL, your page should look like the following:
![http://localhost/PORT](image-1.png)

Now you can enter the Github URL of open-source projects you are interested to learn and understand the architecture of it.

## Visualizing code-graphs using FalkorDB
Let us explore and try to understand the structure of some renowned repositories.
### 1. [CrewAI](https://github.com/crewAIInc/crewAI)
To give a bit of a context about CrewAI, it is designed for orchestrating autonomous AI agents, enabling the creatiion teams to accomplish complex tasks. Let us look into the components, structure and capabilities.

![CrewAI Agent Class](<Screenshot 2024-12-21 at 6.21.17 PM.png>)
The graph represents the code structure of the CrewAI repository, visualized using FalkorDB's code graphing tool. It highlights modules, classes, and functions, with a specific focus on the Agent class and its associated functions. The Agent class (yellow node) serves as a central entity with several functions branching out, indicating its pivotal role in the CrewAI codebase. Functions are primarily designed for task management, setup, and tool integration.
- Role-Based Agents: Users can create agents with specific roles tailored to project needs.
- Flexible Tools: Agents can utilize custom tools and APIs to interact with external services.
- Intelligent Collaboration: Agents share insights and coordinate tasks autonomously.
- Task Management: The framework supports defining workflows that manage task dependencies efficiently. 

Functions like `post_init_setup` ensure that the Agent is properly initialized with necessary configurations.`_setup_agent_executoe` - Sets up the execution environment for the Agent. Functions such as `execute_task` and `create_agent_executor` are responsible for task delegation and execution, emphasizing the operational role of the Agent class. `set_knowledge` and `_parse_tools` are used for setting up information and tools needed by the Agent to perform efficiently. `get_delegation_tools` and `get_code_execution_tools` fetch specific tools to support tasks. The class structure and its dependencies reflect modular coding practices. Each function within the Agent class appears to serve a well-defined purpose, ensuring clarity and maintainability. 

We have also leveraged the Graph RAG method provided by the code-graph using the OpenAI API access. In each graph attached in this article you may find some example questions I have tried to find specific components of the graph, and some generic questions about the repository. 

### 2. [LangGraph](https://github.com/langchain-ai/langgraph)
LangGraph is a repository designed to handle tasks related to language processing, text analysis, and structured data representation. Core functionalities might include language learning assistance, translation, sentiment analysis, text analysis. 
![LangGraph Base Module](<Screenshot 2024-12-21 at 8.10.26 PM.png>)
The `base.py` module, as shown in the figure, is a module with a large number of direct connections to classes and functions. 
Functions like `init, put, get_index_params`, and `maybe_add_type_methods` in `base.py` deals with:
- Initialization of components.
- Handling data insertion and retrieval.
- Indexing operations related to structured data or graph-based storage.
Methods such as `row_to_item`, `group_ops`, and `convert` helps the module processes to handle structured data efficiently. The interconnectedness of base module with other classes, such as `PostgresStore` and `ANNIndexConfig`, indicates its role as a modular backbone for extending functionalities: For example, configurations related to indices or external storage systems like Postgres show that LangGraph supports advanced data storage and retrieval techniques.

### 3. [ColossalAI](https://github.com/hpcaitech/ColossalAI)
Now to shift focus a little bit towards the optimized training of such Language and Foundation Model capabilities. There are repositories like ColossalAI which are extensively used in the multi-stage foundation modelling - Supervised fine-tuning (SFT), Reward Model, and Reinforcement Learning with Human Feedback (RLHF) stages run on smaller nodes across OPT, LLAMA and Mistral models which are supported by the ColossalAI. ColossalAI is a very active repository with 4.3K forks dated until 20 December 2024. Now we will analyse this big repository using code-graph. 
![ColossalAI Code-graph](<Screenshot 2024-12-21 at 7.33.28 PM.png>)
The core library `colossalai` provides methods for Data Parallelism, Tensor Parallelism, and Pipeline Parallism. We have the `DistCoordinator` class which helps in distributed training by managing information about the cluster, such as the number of nodes and processes per node. The `LazyInitContext` class which defers model initialization to save memory when working with large models. This is particularly useful when the model's parameter size approaches or exceeds available memory. `materialize`(module) initializes all parameters from LazyTensor, modifying the module in-place to allocate necessary resources.

There are more to this, but I am leaving it upto you for more explorations as it is a very specific repository for parallelised and optimized trainings. 

[GPyTorch](https://github.com/cornellius-gp/gpytorch)
GPyTorch is a powerful Gaussian process library built on PyTorch, designed to facilitate scalable, flexible, and modular modeling of Gaussian processes (GPs). In my AI for Science work, I leverage GPyTorch to harness the capabilities of Gaussian processes for various scientific applications. I have been exploring the GPyTorch codebase using Code-Graph to perform additional experiments and gain insights into its structure.
![GPyTorch - Functionalities](<Screenshot 2024-12-21 at 6.01.36 PM.png>)
In the image we have the `CALBFGS` (Cubic Approximation Limited-memory BFGS) class is an optimization algorithm implemented in GPyTorch that is particularly useful for training Gaussian processes. This method is designed to handle large-scale optimization problems efficiently by approximating the inverse Hessian matrix using limited memory which is well captured by the code-graph. 
The `LogNormalCDF` class provides a way to compute the cumulative distribution function (CDF) of a log-normal distribution. This is particularly important in scenarios where modeling uncertainty or probabilistic outcomes is necessary. 
My exploration of these components through Code-Graph has deepened my understanding of their functionalities and potential applications in AI for Science.


## Using Code Graphs:
During my usage, I successfully generated graphs for several repositories, which provided valuable insights into their structures and dependencies. The ability to visualize code in this manner significantly enhanced my understanding of complex projects and facilitated easier navigation through the codebase.
Some of the benefits of Code-graphs include: 
- Trace Dependencies: Easily identify how different functions and classes are interconnected.
- Debugging Aid: Pinpoint areas of complexity or unused methods.
- Change Tracking: Monitor how updates in the codebase affect overall functionality.

While my experience with the Code-Graph repository has been largely positive, I did notice that the backend has some limitations in supporting some of the repositories like Langfuse. Expanding this support could further enhance the tool's versatility and broaden its applicability, allowing users to take full advantage of its capabilities across a wider range of projects.

## Conclusions
Overall, this project has highlighted the importance of visualizing code relationships in modern software development and has positioned FalkorDB as a strong player in the graph database landscape, particularly for AI-driven applications. I look forward to seeing how this tool evolves and continues to support developers in their efforts to understand and manage complex codebases.