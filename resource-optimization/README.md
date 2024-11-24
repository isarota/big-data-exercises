# Resource Optimization

In this problem, an architectural design is expected to be created, which can be presented either verbally or with drawings. The solution design should be supported with hardware or software suggestions. For software suggestions, verbal explanations or pseudocode are preferred.

## Case:

In a hypothetical analytical model builder application, machine learning and deep learning models are built. The application is capable of performing data preparation, data cleaning, and data transformation steps before the model-building process. It also supports building models for selected datasets with various types of models.

- All variables to be used in the model can be selected from environments like DWH, OLTP, Hive, etc.
- All pre-model data preparation and model-building operations run on a Spark infrastructure.
- Optionally, pre-model transformation steps and model-building steps can be combined into a single Spark pipeline.

Classification: Open

Given the constraints of limited hardware and fixed resource usage, how should the architectural design be planned to determine the priorities between these pipelines and manage Spark resources for each pipeline? The design should also consider disaster recovery.

## Example pipelines:

Example 1:

- **Start**
- **Data Preprocessing**
  - Data Selection
  - Data Cleaning
  - Data Transformation
- **Model Process**
  - Model Building
  - Model Validation
- **End**

Example 2:

- **Start**
- **Data Preprocessing**
  - Data Selection
  - Data Transformation
- **Model Process**
  - Model 1 Building
  - Model 2 Building
  - Model 3 Building
  - Model Scoring
- **End**

The application is accessed through an interface, where N different users can submit M different pipelines simultaneously. Pipelines run with fixed resource usage in a FIFO manner. Some pipelines are memory-intensive, while others are CPU-intensive.

## Example FIFO Queue to Running Jobs Flow

### FIFO Queue
- Person3-pipeline1
- Person3-pipeline2
- Person3-pipeline3
- Person3-pipeline4
- Person3-pipeline5
- Person3-pipeline6
- Person2-pipeline2
- Person2-pipeline3
- Person1-pipeline5

### Running Jobs
- Person1-pipeline1
- Person1-pipeline2
- Person1-pipeline3
- Person1-pipeline4
- Person2-pipeline1

### Process
FIFO Queue --> [Get] --> Running Jobs


