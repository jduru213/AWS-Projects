## Configuring A Serverless Web Application

This project contains source code and supporting files for a serverless application that you can deploy with the SAM CLI. It includes the following files and folders.

- hello_world - Code for the application's Lambda function.
- events - Invocation events that you can use to invoke the function.
- tests - Unit tests for the application code. 
- template.yaml - A template that defines the application's AWS resources.

## Getting Started

- Before you start the project, consider the following options based on your preference for source code management:

### Option 1: Use an Existing GitHub Repository

If you already have a GitHub repository you'd like to use and deploy, follow these steps:

1. Clone your GitHub repository.
   ```bash
   git clone <your_github_repo_url>
   cd <your_repo_directory>
   

### Option 2: Create a Custom Repository using AWS CodeCommit
If you prefer to create a custom repository using AWS CodeCommit, follow these steps:

1. Create a CodeCommit repository with a name of your choice.
   ```bash
   # Replace <repository_name> with your desired repository name
   aws codecommit create-repository --repository-name <repository_name>

- After creating and deciding on your repository approach, you will later paste it into the Serverless Application Model Command Line Interface (SAM CLI) on AWS Cloud9
   * I decided to use the first approach for this LAB
     
   ![image](https://github.com/jduru213/AWS-Projects/assets/112328773/9a029d2e-d50b-4d02-b469-6c20e187999f)


## Step 1: Creating the sample application

The Serverless Application Model Command Line Interface (SAM CLI) is an extension of the AWS CLI that facilitates building and testing Lambda applications. It leverages Docker to execute functions in an Amazon Linux environment, mirroring the Lambda environment. SAM CLI can also emulate your application's build environment and API.

## Using SAM CLI with AWS Cloud9

### Steps:

1. **Create a Cloud9 Environment:**
   - Open the AWS Cloud9 console.
   - Click on "Create Environment" and follow the prompts to create a new environment.
  
![image](https://github.com/jduru213/AWS-Projects/assets/112328773/c75eacd9-39b0-4be6-a707-f9a96ce7022e)

2. **Install SAM CLI:**
   - Open a terminal in the AWS Cloud9 environment.
   - SAM CLI is already preinstalled in AWS Cloud9, so you can directly proceed to use SAM commands for building, testing, and deploying Lambda applications.
   - Since SAM CLI is already preinstalled go ahead and input your repository; Here is my example of my cloning and inputting my GitHub repository
     ![image](https://github.com/jduru213/AWS-Projects/assets/112328773/9a029d2e-d50b-4d02-b469-6c20e187999f)
      

3. **Building Lambda Applications:**
   - Utilize SAM CLI commands within the Cloud9 environment for building, testing, and deploying Lambda applications.
   - Navigate to the parent of the cloned repository directory, and input the following:
  
```bash
sam init -r python3.8 -n your_repository_name --app-template "hello-world"
```
   - Make sure to put the name of the repository you made either in GitHub or AWS codecommit; Here is my example 
![image](https://github.com/jduru213/AWS-Projects/assets/112328773/c537df47-54a9-4f21-9550-a116a25ca2a2)

## Step 2: Testing Locally 
AWS SAM enables local testing of your applications. It comes with a default event located in events/event.json, which contains a message body: {"message": "hello world"}.

### Steps:

1. **Execute the HelloWorldFunction Lambda function locally by providing it with the default event and inputting:**
   -  ```bash
      sam local invoke HelloWorldFunction -e events/event.json
      ```
![image](https://github.com/jduru213/AWS-Projects/assets/112328773/33bc3031-7936-4a17-b7eb-82e5617cf2aa)

   - Should display: {"message": "hello world"}
     
![image](https://github.com/jduru213/AWS-Projects/assets/112328773/03dca73b-c00f-406c-a423-ad08ac7c9793)

2. **Verify the functionality of the API Gateway in front of the Lambda function by initially launching the API on your local environment.**
   - ``` bash
     sam local start-api
      ```
   ![image](https://github.com/jduru213/AWS-Projects/assets/112328773/0e8f1641-fa68-4bd8-a314-860fb3b752f9)
   
3. **Utilize the 'curl' command to request the hello API.**
    - ``` bash
       curl http://127.0.0.1:3000/hello
      ```
      

## Step 3: Generating the sam-pipeline.yml file

In the context of GitHub, CI/CD (Continuous Integration/Continuous Deployment) pipelines are set up using a YAML file named sam-pipeline.yml. This file acts as a blueprint, defining the actions that should trigger a workflow, such as pushing changes to the main branch. Additionally, it specifies the necessary steps or tasks that need to be executed during the workflow.

### Steps 

1. **Create the directory: .github/workflows**
    ``` bash
       mkdir -p .github/workflows 
      ```
   - Here is my example:
     
     ![image](https://github.com/jduru213/AWS-Projects/assets/112328773/c5ce9b73-7e1a-4298-b21c-637e23a5af4e)

        - Utilize the 'cd' command to navigate and change directories
    
2. **Create Amazon S3 Storage Bucket**
   - In your AWS Management console, navigate to the S3 service and click on the "Create bucket" button
   - Enter a unique name for your bucket in the "Bucket name" field.
   - Choose the AWS region where you want to create the bucket
   - You can leave the remaining configuration as the default
      -![image](https://github.com/jduru213/AWS-Projects/assets/112328773/4d36601b-a7a6-405d-af79-984a2b5adace)

     
4.  **Produce a file named sam-pipeline.yml in the .github/workflows directory**
   - ``` bash
       mkdir sam-pipeline.yml
      ```
   - Navigate using:
     ``` bash
       cd sam-pipeline.yml
      ```
     
4. **Modify the sam-pipeline.yml file to include the following:**
   
 - To open the editor on bash use
    ``` bash
       nano sam-pipeline.yml
      ```
    ![image](https://github.com/jduru213/AWS-Projects/assets/112328773/4e661f1d-e646-43ba-91ec-384cef23a819)
   
  - Then modify using the following:
   ``` bash   
   on:
  push:
    branches:
      - main
jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
      - uses: aws-actions/setup-sam@v1
      - uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ##region##
      # sam build 
      - run: sam build --use-container

# Run Unit tests- Specify unit tests here 

# sam deploy
      - run: sam deploy --no-confirm-changeset --no-fail-on-empty-changeset --stack-name sam-hello-world --s3-bucket ##s3-bucket## --capabilities CAPABILITY_IAM --region ##region## 
  ```
![image](https://github.com/jduru213/AWS-Projects/assets/112328773/84fab869-b708-48e1-b49f-88698e504f2a)

   - Make sure to replace ###s3-bucket## with the name you previously created to store the deployment package
   - Make sure to also replace the ##region## with YOUR AWS region
     
![image](https://github.com/jduru213/AWS-Projects/assets/112328773/dbc0a311-5b93-4477-ba2b-b0da2aa61e6c)


## Step 4: Setting up AWS credentials in GitHub

The purpose behind configuring AWS credentials in GitHub is to interact securely and with authorization. GitHub Actions and other tools can easily access AWS resources with the help of these credentials, which act as the authentication method. Such configuration protects the security and integrity of your cloud-based workflows by limiting access and modification of AWS resources linked to your projects. 

### Steps
1. **Navigate to your Repository:**
   Open your GitHub repository where you want to add secrets.

2. **Go to "Settings":**
   Click on the "Settings" tab in the top navigation bar of your repository.
   ![image](https://github.com/jduru213/AWS-Projects/assets/112328773/df549e58-94f1-4aae-a22e-bf2d950460e0)


4. **Access "Secrets":**
   In the left sidebar, find and click on "Secrets" or "Secrets and variables" (depending on your GitHub version).
   
   ![image](https://github.com/jduru213/AWS-Projects/assets/112328773/72d45691-1e55-4476-b3d0-7779e1656403)


6. **Click "New repository secret":**
   Look for the button that says "New repository secret" or a similar option.
   
   ![image](https://github.com/jduru213/AWS-Projects/assets/112328773/6a2e41f9-2997-42a2-8e67-445e6f034154)


7. **Enter Secret Details:**
   For this project create two secrets named AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY and enter the key values.
   
 ![image](https://github.com/jduru213/AWS-Projects/assets/112328773/02c905f7-dc52-4589-97de-23d5f96779b0)   ![image](https://github.com/jduru213/AWS-Projects/assets/112328773/6cfbf3be-4642-4da3-afdc-77e648f42443)


9. **Click "Add secret" or "Create secret":**
   After entering the details, click the button to add the secret to your repository.
   
## Step 5: Deploying your application

Navigate to your local git repository and Add all the files, commit the changes, and push to GitHub.

 ``` bash
git add.
git commit -am "Add AWS SAM files"
git push
 ```
After pushing the files to GitHub's main branch, the GitHub Actions CI/CD pipeline is automatically initiated, following the configuration specified in the sam-pipeline.yml file.

The GitHub actions runner executes the defined pipeline steps in the file. It retrieves the code from your repository, configures Python, and establishes AWS credentials using the secrets stored in GitHub.


## Step 6: Testing the Application 

Testing your AWS SAM application typically involves two main aspects: unit testing and integration testing. Since your GitHub Actions workflow includes the deployment of the SAM application, I'll guide you on how to set up both types of testing:


```bash
github-actions-with-aws-sam$ pip install -r tests/requirements.txt --user
# unit test
github-actions-with-aws-sam$ python -m pytest tests/unit -v
# integration test, requiring deploying the stack first.
# Create the env variable AWS_SAM_STACK_NAME with the name of the stack we are testing
github-actions-with-aws-sam$ AWS_SAM_STACK_NAME="github-actions-with-aws-sam" python -m pytest tests/integration -v
```

## Cleanup

To delete the sample application that you created, use the AWS CLI. Assuming you used your project name for the stack name, you can run the following:

```bash
sam delete --stack-name "github-actions-with-aws-sam"
```

## Resources

See the [AWS SAM developer guide](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) for an introduction to SAM specification, the SAM CLI, and serverless application concepts.

Next, you can use AWS Serverless Application Repository to deploy ready to use Apps that go beyond hello world samples and learn how authors developed their applications: [AWS Serverless Application Repository main page](https://aws.amazon.com/serverless/serverlessrepo/)
