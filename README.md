# 🐶 Operationalizing an AWS ML Project: Dog Breed Classification

This project is part of the **AWS Machine Learning Engineer Nanodegree** by **Udacity**.

We begin with a completed machine learning model designed to classify dog breeds based on image data. The goal is to **operationalize** this model using various AWS services to make it production-ready, scalable, and secure. The project demonstrates how an ML engineer takes a raw model and transforms it into a robust, deployable service.

---

## 📌 Summary

- **Scenario**: Use the trained solution from the previous dog breed classification project  
- **Task**: Build a production-grade deployment pipeline for a CNN model classifying dog breeds  
- **Dataset**: Dog Breed Image Dataset

---

## 🛠️ Services Used

| AWS Service | Purpose |
|-------------|---------|
| **SageMaker** | Model training, hyperparameter tuning, and endpoint deployment |
| **EC2** | Custom training and debugging using PyTorch with GPU |
| **IAM** | Secure access control across services (SageMaker, Lambda, S3) |
| **S3** | Store training data, model artifacts, and logs |
| **Lambda** | Host lightweight inference service via `invoke_endpoint()` |

---

## 🧪 Technical Features

- **✅ Hyperparameter Tuning**: Using SageMaker's automatic model tuning jobs  
- **✅ Multi-instance Training**: Training ResNet50 on multiple GPU instances for scalability  
- **✅ Model Deployment**: SageMaker endpoint deployment and integration with Lambda  
- **✅ Lambda Concurrency & Auto-scaling**: Set provisioned concurrency and auto-scaling for high-load inference  
- **✅ IAM Role Security**: Followed least-privilege principle to restrict roles and access rights  

---

## 🔒 Security

Security is a top priority in this deployment. IAM roles are tightly scoped to allow only:
- SageMaker training and endpoint invocation
- Lambda execution with SageMaker invocation rights
- S3 read/write only to relevant buckets

The workspace is kept secure by avoiding full-access roles and using AWS-provided trust boundaries.

---

## 🚀 How to Run This Project

1. **Train the model using EC2 or SageMaker**
   ```bash
   python3 solution.py  # from EC2 with g4dn.xlarge
   ```

2. **Upload the model to S3**
   ```bash
   aws s3 cp model.pth s3://your-bucket-name/
   ```

3. **Deploy the model on SageMaker**
   - Use a PyTorch Estimator and deploy via `estimator.deploy(...)`

4. **Configure a Lambda function**
   - Attach a role with `sagemaker:InvokeEndpoint` permission
   - Use the endpoint name in the Lambda handler

5. **Enable Concurrency and Auto-scaling**
   - Provisioned concurrency: `3`
   - Auto-scaling max instance count: `3`
   - Scale in/out cooldown: `30s`
   - Target metric: `25`

---

## 🧠 Keywords

`AWS`, `SageMaker`, `Lambda`, `EC2`, `IAM`, `Auto-scaling`, `Concurrency`, `Image Classification`, `Deep Learning`, `PyTorch`, `Dog Breed Classification`
