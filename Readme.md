# AZURE Kubernetes Automation



# Architecture

![Watch the image](/Sree-usecase-1.png)

# Steps

- Step 1:  Create SNS and SQS then Subscribe to Amazon SNS topic from SQS ( SNS --> SQS)

#

# Step 1: Create SNS and SQS then Subscribe to Amazon SNS topic from SQS ( SNS --> SQS)

- Go to Amazon SNS --> Create topic --> Select standard --> Topic Name --> Create Topic
- Go to Amazon SQS --> Create queue --> Select Standard --> Queue Name --> Create Queue
- Open the Queue --> Go to SNS Subscriptions  --> Click Subscribe to Amazon SNS topic --> Select Amazon topic name --> Save
- Goto SNS --> Select Topic name --> Publish message --> Give subject and also Message like below
```
{
    "customer": 100,
    "phone": 5516999999999,
    "items": [
        {
            "name": "produto 1", "amount": 10},
        {
            "name": "produto 2", "amount": 14.4}
    ]
}
```

-  Go to SQS --> Select queue name --> Send and Recive Message --> Poll for message --> click message --> select Body 

- Configure the azure CLI Repository

```
sudo sh -c 'echo -e "[azure-cli]
name=Azure CLI
baseurl=https://packages.microsoft.com/yumrepos/azure-cli
enabled=1
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/azure-cli.repo'
```
