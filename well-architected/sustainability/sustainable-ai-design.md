# Sustainable AI Design

Designing, building, and operationalizing AI workloads **sustainably** is crucial as AI workloads consume significant energy and carbon throughout their lifecycle. Training large AI models, such as GPT-3, can consume up to 700,000 liters of clean freshwater, equivalent to producing 370 BMW cars or 320 Tesla electric vehicles. To ensure the business value derived from AI does not compromise environmental balance, it is essential to adopt sustainable practices.


# AI Workload lifecycle

The AI workload lifecycle consists of three primary steps: Model Design, Data Design, and Operational Design. In this section we will look at the different design aspects that can be considered in each of these steps for factoring in sustainable software design in the lifecycle of AI workload development.

![AI workload lifecycle view](https://drive.google.com/file/d/1NAJtONpz7bz-TB6SmsqIc95AP0ho8eW_/view?usp=sharing)

## Business Goal

Before we dive into the engineering aspects, it is important to understand the business problem if choosing AI is indeed the right step. 
Consider building an AI workload only:
•	If the problem requires learning from data over time
•	If the problem changes over time due to user behavior changes and market trends
•	If the problem requires the system to make decisions autonomously
•	If the problem involves dealing with complex, unstructured data 
•	If the problem involves creating new insights through predictive modeling 

## Key Recommendations for Sustainable AI Workloads:


### Model Design Considerations:

Model design is the first step in the lifecycle of AI workload where the right model is chosen for solving the business problem. A model is a pre-trained package that helps in machine learning problems of the world like classification, prediction, forecasting etc. 
There are multiple decisioning points during the model design phase. In this section we will cover some key points to keep in mind to help make sustainable decisions. 

1. **Size of Model**: Opt for Small Language Models (SLMs) over Large Language Models (LLMs) to reduce energy consumption. By reducing the size of AI models, developers can decrease the computational resources needed to process them, thus cutting down the overall energy expenditure. This approach not only improves model efficiency but also allows AI to be deployed on resource-constrained devices.
2. **Reuse Models**: Utilize pre-trained models like ChatGPT, BERT, and GPT-3 to save energy cycles. Using a pre-trained model saves energy cycles since we don’t have to plan for Data ingestion, Data cleanup, training, pre-training and fine tuning as this is all done and available out of the box for us to use and hence is very much a sustainable alternative.
3.	**Energy-Efficient Frameworks**: Choose frameworks like TensorFlow and PyTorch for their energy efficiency.
4. **Fine Tuning**: Pre-train models on general datasets before fine-tuning on specific domains to reduce computational cycles.
5. **Hosting Provider and Region**: Model training is energy intensive. It is important that the right region is chosen .Select regions powered by renewable energy sources for model training.

### Data Design Considerations:
Data design phase is one which involves Data identification, classification, ingestion, storage and computation. Each of these process steps requires compute, network and storage resources. By making conscious decisions around the amount and type of infrastructure that needs to be stood up, the data processing phase could be made sustainable. Some of the levers that we can use are:
1. **Data Processing**: Reuse publicly available datasets and architect data ingestion to pull only relevant data.
2. **Storage**: Store data in regions powered by renewable energy and consider edge computing.
3.	**Clean Data**: Ensure high-quality data for training to avoid wasted cycles.
Another consideration would be to move the whole data processing, ingestion, computation and storage to the edge.

### Operational Design Considerations:

1. **Hosting Provider and Region**:  Deploy models in regions using renewable energy sources.
2. **Frequency of Retraining**: Retrain orchestration involves automating the retraining of models based on new data. This ensures that the model stays up-to-date and maintains its performance over time. Retraining frequently is not sustainable. Balance model accuracy with energy consumption by setting thresholds for retraining.
3.	**Monitoring**: Track model performance to prevent machine learning ops cycles from being wasted due to model decay.When model decay occurs, machine learning ops cycles are wasted since the AI workload is not predicting correct outcomes, but it continues to run. Monitoring and ensuring corrective actions are taken ensures that the AI workload operates sustainably. 
By implementing these sustainable practices, AI developers and data scientists can significantly reduce the environmental impact of AI workloads while maintaining business value.