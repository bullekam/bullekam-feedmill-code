# bullekam-feedmill-code
Code for feedmill application

# 1) Terraform up (creates the ECR repo and everything else)
cd infra
terraform init
terraform apply

# 2) Get ECR URL from output
ECR_URL=$(terraform output -raw ecr_repository_url)
cd ..

# 3) Login to ECR
aws ecr get-login-password --region us-east-1 \
  | docker login --username AWS --password-stdin $ECR_URL

# 4) Build + tag + push
docker build -t hello-world-next:v1 .
docker tag hello-world-next:v1 $ECR_URL:v1
docker push $ECR_URL:v1

cd infra
terraform apply -var="image_tag=v1"


Open the app:
terraform output -raw alb_dns_name
Visit: http://<that-dns-name>