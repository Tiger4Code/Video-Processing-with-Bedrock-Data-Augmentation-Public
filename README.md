# Video Summarization with Bedrock Data Automation - Notebook

## 1. Environment Setup
First, I spun up a JupyterLab environment in Amazon SageMaker Studio. Make sure to attach the correct IAM permissions — refer to the GitHub repository linked with this video for the required IAM policy (SAM policy) you need to attach. See [`permissions.json`](./permissions.json) in this folder for the exact IAM policy required.

> **Note:**
> The permissions provided in `permissions.json` are generic examples. **Do not use `*` as the resource in production.**
> Always customize the policy to specify the exact AWS resources you intend to use for improved security and least-privilege access.

This folder contains Jupyter notebooks for demonstrating video processing and summarization using Amazon Bedrock Data Automation (BDA).

## 📁 Files Overview

### `MAIN-noor-single-custom-blueprint.ipynb`
This is the main notebook that demonstrates end-to-end video processing using custom blueprints with Amazon Bedrock Data Automation.

## 🎯 What `MAIN-noor-single-custom-blueprint.ipynb` Does

### **Overview**
This notebook demonstrates how to use Amazon Bedrock Data Automation (BDA) to process video files and extract structured insights using custom blueprints. It shows the complete workflow from blueprint creation to video analysis and result extraction.

### **Key Features**

#### 1. **Custom Blueprint Creation**
- Creates a custom video processing blueprint (`video-summarizer-standard-custom`)
- Uses a JSON schema that defines fields for:
  - **Visual Analysis**: Image & diagram extraction with timestamps
  - **Sentiment Analysis**: Rating and explanation based on voice tone and content
  - **Topic Categorization**: Identifying distinct discussions and topics
  - **Transcript Generation**: Full spoken content transcription
  - **Video Summary**: Comprehensive summary with technical concepts

#### 2. **BDA Project Setup**
- Creates a data automation project (`video-summarizer-custom-output-only-blueprints`)
- Configures both standard and custom output configurations
- Enables audio and image extraction capabilities
- Associates the custom blueprint with the project

#### 3. **Video Processing Pipeline**
- Uploads a sample video file (`test.mp4`) to S3
- Invokes BDA async processing with the configured project
- Monitors job status and waits for completion
- Retrieves and analyzes results

#### 4. **Result Analysis**
- Extracts both standard and custom outputs
- Displays structured insights in table and JSON formats
- Shows matched blueprints and processing status
- Visualizes document segments and extracted information

### **Technical Workflow**

1. **Setup & Configuration**
   ```python
   # Initialize BDA clients and S3 locations
   bda_client = boto3.client('bedrock-data-automation')
   bda_runtime_client = boto3.client('bedrock-data-automation-runtime')
   ```

2. **Blueprint Creation**
   ```python
   # Create custom blueprint with video processing schema
   blueprint_arn = helper_functions.create_or_update_blueprint(
       bda_client, blueprint_name, description, 'VIDEO', 'LIVE', schema
   )
   ```

3. **Project Configuration**
   ```python
   # Create BDA project with custom blueprint
   response = bda_client.create_data_automation_project(
       projectName=bda_project_name,
       customOutputConfiguration=custom_output_configuration
   )
   ```

4. **Video Processing**
   ```python
   # Invoke async video processing
   response = bda_runtime_client.invoke_data_automation_async(
       inputConfiguration={'s3Uri': document_s3_uri},
       dataAutomationConfiguration={'dataAutomationProjectArn': project_arn}
   )
   ```

5. **Result Extraction**
   ```python
   # Get custom and standard outputs
   custom_outputs = [json.loads(helper_functions.read_s3_object(
       segment_metadata.get('custom_output_path'))) 
       for segment_metadata in segments_metadata]
   ```

### **Output Structure**

The notebook produces structured outputs including:

- **Custom Outputs**: Blueprint-specific extractions (sentiment, topics, visual elements)
- **Standard Outputs**: General audio/video analysis (transcripts, summaries, content moderation)
- **Metadata**: Job status, matched blueprints, and processing information

### **Key Benefits**

1. **Structured Video Analysis**: Extracts specific insights based on custom requirements
2. **Scalable Processing**: Handles video files asynchronously
3. **Flexible Blueprints**: Customizable schema for different video types
4. **Comprehensive Results**: Both standard and custom outputs for complete analysis

### **Use Cases**

- **Meeting Recordings**: Extract key topics, sentiment, and action items
- **Training Videos**: Identify technical concepts and learning objectives
- **Presentations**: Capture visual elements and presentation flow
- **Interviews**: Analyze sentiment and extract key discussion points

### **Prerequisites**

- AWS account with Bedrock Data Automation access
- SageMaker notebook environment
- Required Python packages: `boto3`, `pandas`, `IPython`
- Sample video file for processing

### **Cleanup**

The notebook includes cleanup procedures to remove uploaded files and generated outputs from S3, ensuring cost optimization.

---

## 📚 Related Files

- `video/video_summarizer.json`: Custom blueprint schema for video processing
- `console-video-blueprint.json`: Reference blueprint schema from AWS console
- `utils/`: Helper functions for BDA operations and result processing

## 🚀 Getting Started

1. Open the notebook in a SageMaker environment
2. Ensure you have the required permissions for Bedrock Data Automation
3. Upload a sample video file to the `video/` directory
4. Run the notebook cells sequentially
5. Review the generated insights and custom outputs

This notebook serves as a comprehensive example of how to leverage Amazon Bedrock Data Automation for advanced video processing and analysis using custom blueprints. 

