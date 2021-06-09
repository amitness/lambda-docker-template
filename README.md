
## Steps
1. Build the docker image with a tag.
```shell
docker build -t ping .
```

2. Run and ensure it works
```shell
docker run -p 8080:8080 ping
```

Test using requests
```python
import requests
import json

data = {"text": "This is a message."}
response = requests.post('http://localhost:8080/2015-03-31/functions/function/invocations', json={'body': json.dumps(data)})
print(response.json())
```

3. Create an ECR repo and set the AWS Region and Account ID and push the docker image to ECR.
```shell
export AWS_REGION=us-east-2
export AWS_ACCOUNT_ID=********
export TAG=ping
export ECR_REPO_NAME=ping

aws ecr create-repository --repository-name $ECR_REPO_NAME

aws ecr get-login-password \
    --region "$AWS_REGION" \
| docker login \
    --username AWS \
    --password-stdin "$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com"

docker tag $TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO_NAME

docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO_NAME
```

4. Install [serverless](https://www.serverless.com/framework/docs/getting-started/) and deploy lambda function to AWS.
```
serverless deploy
```

5. Try the API by calling endpoint given by serverless framework.
```python
import requests
data = {"text": "This is a message."}

response = requests.post('https://******.execute-api.us-east-2.amazonaws.com/dev/ping', json=data)
print(response.json())
```