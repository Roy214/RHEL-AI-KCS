Red Hat Enterprise Linux AI is a platform that allows you to develop enterprise applications on open source Large Language Models (LLMs). RHEL AI is built from the Red Hat InstructLab open source project. For more detailed information about InstructLab, see the "InstructLab and RHEL AI" section.

Common Terms for RHEL AI:

Red Hat Enterprise Linux AI allows you to do the following:

Host an LLM and interact with the open source Granite family of Large Language Models (LLMs).
Using the LAB method, create and add your own knowledge data in a Git repository and fine-tune a model with that data with minimal machine learning background.
Interact with the model that has been fine-tuned with your data.
Red Hat Enterprise Linux AI empowers you to contribute directly to LLMs. This allows you to easily and efficiently build AI-based applications, including chatbots.

This glossary defines common terms for Red Hat Enterprise Linux AI:

InstructLab
InstructLab is an open source project that provides a platform for easy engagement with AI Large Language Models (LLM) by using the ilab command-line interface (CLI) tool.
Large Language Models
Known as LLMs, is a type of artificial intelligence that is capable of language generation or other processing tasks.
Synthetic Data Generation (SDG)
A process where large LLMs (Large Language Models) are used to generate artificial data that then can be used to train other LLMs.
Fine-tuning
A technique where an LLM is trained to meet a specific objective: to know particular information or be able to do a particular thing.
LAB
An acronym for "Large-Scale Alignment for ChatBots." Invented by IBM Research, LAB is a novel synthetic data-based alignment tuning and multi-phase training method for LLMs. InstructLab implements the LAB method during synthetic generation and training.
Multi-phase training
A fine-tuning strategy that the LAB method implements. During this process, a model is fine-tuned on multiple datasets in separate phases. The model trains in multiple phases called epochs, which gets saved as a checkpoint. The best performing checkpoint is then used for training in the following phase. The fully fine-tuned model is the best performing checkpoint from the final phase.
Serving
Often referred to as "serving a model", is the deployment of an LLM or trained model to a server. This process gives you the ability to interact with models as a chatbot.
Inference
When serving and chatting with a model, inferencing is when a model can process and produce outputs from input data.
Taxonomy
The LAB method is driven by taxonomies, an information classification method. On RHEL AI, you can customize a taxonomy tree that enables you to create models fine-tuned with your own data.
Granite
An open source (Apache 2.0) Large Language Model trained by IBM. On RHEL AI you can download the granite-7b-starter model as a base LLM for customizing.
PyTorch
An optimized tensor library for deep learning on GPUs and CPUs.
vLLM
A memory-efficient inference and serving engine library for LLMs.
FSDP
An acronym for Fully Shared Data Parallels. The Pytorch tool FSDP can distribute computing power across multiple devices on your hardware. This optimizes the training process and makes fine-tuning faster and more memory efficient. This tool shares the functionalities of DeepSpeed.
DeepSpeed
A Python library for optimizes LLM training and fine-tuning by distributing computing resources on multiple devices. This tool shares the functionalities of FSDP. Deepspeed is currently the recommended hardware off loader for NVIDIA machines.

InstructLab is an open source AI project that facilitates contributions to Large Language Models (LLMs). RHEL AI takes the foundation of the InstructLab project and builds an enterprise platform for LLM integration on applications. Red Hat Enterprise Linux AI targets high performing server platforms with dedicated Graphic Processing Units (GPUs). InstructLab is intended for small scale platforms, including laptops and personal computers.

Instruct Lab and RHEL AI:

InstructLab implements the LAB (Large-scale Alignment for chatBots) technique, a novel synthetic data-based fine-tuning method for LLMs. The LAB process consists of several components:

A taxonomy-guided synthetic data generation process
A multi-phase training process
A fine-tuning framework
RHEL AI and InstructLab allow you to customize an LLM with domain-specific knowledge for your distinct use cases.

Introduction to skills and knowledge 

Skill and knowledge are the types of data that you can add to the taxonomy tree. You can then use these types to create a custom LLM model fine-tuned with your own data.

Knowledge: 
Knowledge for an AI model consists of data and facts. When creating knowledge sets for a model, you are providing it with additional data and information so the model can answer questions more accurately. Where skills are the information that trains an AI model on how to do something, knowledge is based on the model’s ability to answer questions that involve facts, data, or references. For example, you can create a data set that includes a product’s documentation and the model can learn the information provided in that documentation.

Skill:
A skill is a capability domain that intends to train the AI model on submitted information. When you make a skill, you are teaching the model how to do a task. Skills on RHEL AI are split into categories:

Composition skill: Compositional skills allow AI models to perform specific tasks or functions. There are two types of compositional skills:

Freeform compositional skills: These are performative skills that do not require additional context or information to function.
Grounded compositional skills: These are performative skills that require additional context. For example, you can teach the model to read a table, where the additional context is an example of the table layout.
Foundation skills: Foundational skills are skills that involve math, reasoning, and coding.
