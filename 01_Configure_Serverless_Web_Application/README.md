## Configuring A Serverless Web Application

This project contains source code and supporting files for a serverless application that you can deploy with the SAM CLI. It includes the following files and folders.

- hello_world - Code for the application's Lambda function.
- events - Invocation events that you can use to invoke the function.
- tests - Unit tests for the application code. 
- template.yaml - A template that defines the application's AWS resources.

The application uses several AWS resources, including Lambda functions and an API Gateway API. These resources are defined in the `template.yaml` file in this project. You can update the template to add AWS resources through the same deployment process that updates your application code.

If you prefer to use an integrated development environment (IDE) to build and test your application, you can use the AWS Toolkit.  
The AWS Toolkit is an open-source plug-in for popular IDEs that uses the SAM CLI to build and deploy serverless applications on AWS. The AWS Toolkit also adds a simplified step-through debugging experience for Lambda function code. See the following links to get started.

* [CLion](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [GoLand](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [IntelliJ](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [WebStorm](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [Rider](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [PhpStorm](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [PyCharm](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [RubyMine](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [DataGrip](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [VS Code](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/welcome.html)
* [Visual Studio](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/welcome.html)

## Getting Started

Before you start the project, consider the following options based on your preference for source code management:

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

3. **Build and Test Lambda Applications:**
- Utilize SAM CLI commands within the Cloud9 environment for building, testing, and deploying Lambda applications.
- navigate to the parent of the cloned repository directory, and input the following:
  
```bash
sam init -r python3.8 -n <"your_repository_name"> --app-template "hello-world"
```
- Make sure to put the name of the repository you made either in GitHub or AWS codecommit 


## Use the SAM CLI to build and test locally

Build your application with the `sam build --use-container` command.

```bash
github-actions-with-aws-sam$ sam build --use-container
```

The SAM CLI installs dependencies defined in `hello_world/requirements.txt`, creates a deployment package, and saves it in the `.aws-sam/build` folder.

Test a single function by invoking it directly with a test event. An event is a JSON document that represents the input that the function receives from the event source. Test events are included in the `events` folder in this project.

Run functions locally and invoke them with the `sam local invoke` command.

```bash
github-actions-with-aws-sam$ sam local invoke HelloWorldFunction --event events/event.json
```

The SAM CLI can also emulate your application's API. Use the `sam local start-api` to run the API locally on port 3000.

```bash
github-actions-with-aws-sam$ sam local start-api
github-actions-with-aws-sam$ curl http://localhost:3000/
```

The SAM CLI reads the application template to determine the API's routes and the functions that they invoke. The `Events` property on each function's definition includes the route and method for each path.

```yaml
      Events:
        HelloWorld:
          Type: Api
          Properties:
            Path: /hello
            Method: get
```

## Add a resource to your application
The application template uses AWS Serverless Application Model (AWS SAM) to define application resources. AWS SAM is an extension of AWS CloudFormation with a simpler syntax for configuring common serverless application resources such as functions, triggers, and APIs. For resources not included in [the SAM specification](https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md), you can use standard [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html) resource types.

## Fetch, tail, and filter Lambda function logs

To simplify troubleshooting, SAM CLI has a command called `sam logs`. `sam logs` lets you fetch logs generated by your deployed Lambda function from the command line. In addition to printing the logs on the terminal, this command has several nifty features to help you quickly find the bug.

`NOTE`: This command works for all AWS Lambda functions; not just the ones you deploy using SAM.

```bash
github-actions-with-aws-sam$ sam logs -n HelloWorldFunction --stack-name "github-actions-with-aws-sam" --tail
```

You can find more information and examples about filtering Lambda function logs in the [SAM CLI Documentation](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-logging.html).

## Tests

Tests are defined in the `tests` folder in this project. Use PIP to install the test dependencies and run tests.

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
