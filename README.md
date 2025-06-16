# 🐶 Project: Operationalizing an AWS ML Project

This project is part of the AWS Machine Learning Engineer Nanodegree from Udacity. The goal was to take a pre-trained machine learning model — in this case, a dog breed classifier — and make it ready for production using key AWS tools like SageMaker, EC2, Lambda, S3, and IAM.

---

## ✅ Training and Deployment

### Setting up SageMaker

I started by creating a SageMaker notebook instance using `ml.g4dn.xlarge`. This instance has a GPU (NVIDIA T4), which makes it a great option for running deep learning tasks inside the notebook. It gave me enough power to do quick tests, run training jobs, and interact with the model without needing to spin up separate training jobs every time.

### Why I Chose `ml.g4dn.xlarge`

The `ml.g4dn.xlarge` instance type strikes a nice balance between performance and cost. It’s not as expensive as the `p3` series but still has enough GPU memory to handle CNN-based training like the ResNet50 model used in this project. It was the perfect choice for both development and training.

### S3 Bucket for Data

I created and used an S3 bucket to store my training data and model artifacts:
```
s3://operation-udacity/
```

After training, SageMaker saved the final model artifact to:
```
s3://sagemaker-us-east-1-381491983227/dog-pytorch-2025-06-16-10-09-06-343/output/model.tar.gz
```

### Deploying the Model

I deployed the trained model using this line:
```python
predictor = pytorch_model.deploy(
    initial_instance_count=1,
    instance_type='ml.m5.large',
    endpoint_name='pytorch-dog-classifier-multi-instance'
)
```

The endpoint was successfully created and is ready to serve predictions.

### Multi-Instance Training

To speed things up, I configured SageMaker to use multiple training instances. I set:
```python
instance_count=2,
instance_type='ml.g4dn.xlarge'
```
This helped distribute the workload and reduce overall training time.

---

## ✅ EC2 Training

### What I Used

I launched a `g4dn.xlarge` EC2 instance using the AWS Deep Learning AMI. This gave me access to a GPU-powered virtual machine, which I used for local training and experimentation outside of SageMaker.

### Why This Instance

Same reason as above — the T4 GPU is cost-effective and gets the job done. It’s ideal for projects where you want more control and flexibility, like running training scripts directly.

### Training from EC2

I trained the model using a regular Python script (`solution.py`) without using SageMaker SDKs. It was all done using PyTorch directly from the command line with:
```bash
python3 solution.py
```

---

## ✅ Lambda Function

### Deploying the Endpoint via Lambda

Once the endpoint was live, I set up a Lambda function to act as the API layer. It uses `boto3` to send requests to the SageMaker endpoint and returns the prediction result. The Lambda function reads a JSON input, calls the endpoint, and formats the output.

### Proper Response Format

The return response from the Lambda function includes:
- Status code (`200`)
- Headers with content type
- A JSON body with the prediction result

This structure is useful if we want to connect it to an API Gateway later on.

---

## ✅ Security

### IAM Role Setup

The Lambda function uses an IAM role that includes the `AmazonSageMakerFullAccess` policy. This lets it invoke the SageMaker endpoint but nothing else — keeping things secure and scoped down.

### IAM Audit

I went through all the roles in the project and made sure none were over-permissioned. I removed or ignored any roles that were unused or had old full-access policies. This ensures the environment is clean and secure.

---

## ✅ Concurrency & Auto-Scaling

### Lambda Concurrency

I set **provisioned concurrency** to `3` so the Lambda function can handle multiple requests without cold starts. This helps when traffic spikes.

### SageMaker Auto-Scaling

For the endpoint, I set:
- Min instance count: 1
- Max instance count: 3
- Target metric: 25
- Cooldown: 30 seconds

This helps keep costs under control but still allows us to scale when traffic increases.

---

## 🧠 Keywords

AWS, SageMaker, Lambda, EC2, IAM, Auto-scaling, Concurrency, PyTorch, Deep Learning, Dog Breed Classification
