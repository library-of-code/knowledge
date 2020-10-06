### Retrieve a password to authenticate to a registry 
```
aws ecr get-login-password --region <region>
```

### Login to Docker CLI with AWS
```
aws ecr get-login-password \
    --region <region> \
| docker login \
    --username AWS \
    --password-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com
```