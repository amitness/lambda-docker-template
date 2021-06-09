
## Steps
1. Build the docker image with some tag.
```shell
docker build -t ping .
```

2. Run the docker container and ensure it works.
```shell
docker run -p 8080:8080 ping
```

Test the function locally by using requests on the local endpoint.
```python
import requests
import json

data = {"text": "This is a message."}
response = requests.post(
    "http://localhost:8080/2015-03-31/functions/function/invocations",
    json={"body": json.dumps(data)},
)
print(response.json())
```

![image](https://user-images.githubusercontent.com/8587189/121376984-b758e880-c961-11eb-9f98-69dee5842235.png)


3. Set the AWS region, Account ID and other credentials as environment variables. Run the commands.
```shell
export AWS_REGION=us-east-2
export AWS_ACCOUNT_ID=********
export TAG=ping
export ECR_REPO_NAME=ping

# Create ECR repository
aws ecr create-repository --repository-name $ECR_REPO_NAME

# Login to ECR using Docker
aws ecr get-login-password \
    --region "$AWS_REGION" \
| docker login \
    --username AWS \
    --password-stdin "$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com"

# Tag local image to ECR endpoint
docker tag $TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO_NAME

# Push the docker image to ECR
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
