# Bedrock Agentic Infrastructure Automation

This solution demonstrates how to deploy a serverless RAG (Retrieval-Augmented Generation) system using **Amazon Bedrock Agents**, **OpenSearch Serverless**, and **Lambda Action Groups** to dynamically provision and manage AWS infrastructure using natural language.

---

## Solution Overview

The solution consists of two main CloudFormation templates:

1. **`opensearch.yml`**: Provisions the OpenSearch Serverless collection, S3 bucket for documents, and IAM roles required by Amazon Bedrock to use the knowledge base.
2. **`agentic.yml`**: Sets up the Bedrock Agent, associates it with the knowledge base, and deploys action group Lambda functions for infrastructure provisioning (VPCs, subnets, EC2, endpoints).

---

## 📁 Prerequisites

Before deployment, ensure you have:

- AWS CLI installed and configured
- Necessary IAM permissions to deploy CloudFormation stacks, create roles, and resources
- Your knowledge base documents uploaded to an S3 bucket

---

## Deployment Steps

### Step 1: Prepare `opensearch.yml`

Edit the `opensearch.yml` file and update the following lines with your **S3 bucket name** that contains the knowledge base documents:

- **Line 185**: Update `source_bucket = 'change-accordingly'` with your actual bucket name
- **Line 225**: Update `source_bucket = 'change-accordingly'` again

For example:

```python
source_bucket = 'my-kb-documents-bucket'
```

### Step 2: Deploy OpenSearch and Knowledge Base Stack

Run the following command to deploy the `opensearch.yml` stack:

```bash
aws cloudformation deploy \
  --template-file opensearch.yml \
  --stack-name opensearch-serverless-stack \
  --capabilities CAPABILITY_NAMED_IAM
```

This stack will:

- Create an S3 bucket or reference an existing one
- Set up IAM roles for Bedrock
- Provision a vector-based OpenSearch Serverless collection
- Upload sample documentation to the S3 bucket
- Create encryption and network security policies

After successful deployment, note the exported values (you'll need them for `agentic.yml`):

- `S3BucketName`
- `CollectionARN`
- `AOSSVectorIndexName`
- `AmazonBedrockExecutionRoleForKnowledgeBase`

### Step 3: Deploy the Bedrock Agent and Lambda Action Groups

Once the OpenSearch knowledge base is deployed, you can deploy the Bedrock agent stack:

```bash
aws cloudformation deploy \
  --template-file agentic.yml \
  --stack-name bedrock-agentic-stack \
  --capabilities CAPABILITY_NAMED_IAM
```

This will:

- Set up the Bedrock Agent with memory, instructions, and knowledge base attachment
- Define action groups and Lambda functions (for VPC, subnet, endpoint, and EC2 creation)
- Grant the agent permission to invoke the functions

![image](https://github.ibm.com/Reza-Beykzadeh/bedrock-agentic-ai/assets/388449/5e6c65ba-9520-4b19-8ff6-71d08f2e5ca9)


## Testing the Deployment

1. Open the **Bedrock Agent Console**
2. Select your agent (e.g., `rag-sagemaker-agent`)
3. Use the **Test interface** to interact with the agent

**Example input:**
```
please create a VPC named CloudOpsVpc with a CIDR block of 10.10.0.0/16 in us-gov-west-1
```

4. Verify that the resource was created by checking the **VPC Dashboard** in the EC2 console

---

## Cleanup

To delete the resources:

```bash
aws cloudformation delete-stack --stack-name bedrock-agentic-stack
aws cloudformation delete-stack --stack-name opensearch-serverless-stack
```

---

## Resources Used

- Amazon Bedrock Agents
- Amazon OpenSearch Serverless
- AWS Lambda
- Amazon S3
- AWS IAM
- Amazon EC2 / VPC

---

## Author

Created by **Reza Beykzadeh**
