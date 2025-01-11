java cAssignment 1: CI/CD Pipeline
Introduction
During the up-coming assignments in this course you will build a pipeline that executes different
tasks, such as unit tests, integration tests, end-to-end tests, deployments etc. To achieve this you will
use the pipeline funcationallity in GitHub called GitHub Actions / Workflows, as presented in the
workshop session 1. In this assignment your task is to create a new respository in GitHub and
create a new pipeline (Workflow) that will package a small application into a Docker image and
then push it to a so called Container Registry. The goal is that everytime changes are made to any
code in the repository a new Docker image should be built and pushed to the Docker registry
automatically. Hence, as soon as the application is updated a new version is being published and is
ready to be used. You should also add some basic logging to the application.
What should be used?
• GitHub
• Docker 
• Python
• Calculator application (provided by the teachers)
Assignment tasks
1. Add logging statements to the application
First, you will have to download the Calculator application which is published on the course site in
Canvas. Create a new empty directory and unzip the file there. Make sure you have all the needed
Python libraries (dependencies) for running the application. Install them by running:
pip install -r BE/requirements.txt
Then you should be able to run the application from a command shell by executing:
python BE/calculator.py --add 1 1
The application should print the result, that is: 1.0+1.0=2.0.
If this works, you’re ready to add some logging to the application. There is a logger.py file in the
calculator application that should be used for this purpose. In creates a logger instance with two
handlers, one that prints the logs to the terminal and one that sends the logs to a service in Azure
called Application Insights. You don’t have access to this service yourself, but it will be
demonstrated during a lecture, so you’ll see some of capabilities of that service.
Your task is to add logging-statements to important methods in calculator_helper.py file, that is,
subtract, multiply, divide, register_user, login  logout. The log messages should be informative
about what the method is doing, for example, what numbers are calculated, what user is created
1
and so forth.
At the top of the file there is a log_properties definition, replace the userId with your name in the
format firstname_lastname. This is used when storing the logs to Application Insights. An example
of how to use the logger instance would be:
self.logger.debug("some message", extra=self.log_properties)
2. Create a Dockerfile
Now it’s time to create a Dockerfile that builds a Docker image that contains the application. When a
container is started, based on the created image, it should calculate an answer depening on your
input. For example docker run your_image_name --add 1 1 should print the result 1.0+1.0=2.0.
Create your Dockerfile in the BE-subdirectory that contains the Python implemention.
To achieve this task you will have to use the folloing instructions in your Dockerfile: FROM, COPY, RUN 
ENTRYPOINT. Your Docker image should be based on the Docker Python image: python:3.12-slim. The
RUN instruction is to be used for installing the Python requirements on the image that are needed by
the calculator application. The needed packages are defined in the requirements.txt-file. Use the
Python package manager pip to install the packages.
Information about Dockerfile and the four commands you should use can be found here:
https://docs.docker.com/engine/reference/builder/
You don’t need to read through and understand all the details! Just find the information needed to
solve the task…
When you have created a Dockerfile you can build an image by using the Docker build command.
Run the following command while standing in the directory containing the Dockerfile: docker build
-t your_name-calculator .
You should then be able to see your newly created image by executing the command: docker images.
Figure out how to start a container from your created image that calculates and prints the sum of
1+1. Use the docker run command.
NOTE
Use your real name as a prefix when giving names to your images, for example:
jens_johansson-calculator.代 写program、Python
代做程序编程语言 Then the teachers can see who has created what image.
The images will in later steps be pushed to a container registry. Images who cannot
be 'connected' to a student by name will be removed from the registry…
3. Publishing an image to a Docker container registry
Now you should push the Docker image to a container registry in the cloud, which means the image
can be accessed from anywhere, not just your own computer. For this course we have created a
container registry in System Verification’s Azure (Microsoft cloud) account. The name of the
container registry is judevops.azurecr.io. Login to the registry using the docker login command.
The username and password are available in Canvas.
2
To which registry Docker pushes (uploads) an image is controlled by the name of the image. If you
want to push a Docker image called your_name-calculator to the judevops.azurecr.io registry it
should be named as judevops.azurecr.io/your_name-calculator. After you have logged in to the
registery, name your image accordingly using the docker tag command and then push it to the
registry using the docker push command.
You should now be able to pull (download) the image from the Docker registry using the docker
pull command, i.e. docker pull judevops.azurecr.io/your_name-calculator and then run it using the
command: docker run judevops.azurecr.io/your_name-calculator.
4. Create a new GitHub repository and a pipeline
Sign in to your GitHub account and create a new repository. GitHub Classroom link is available in
Canvas. Add the files from previous steps, i.e. the Dockerfile and calculator-directory with Python
files to the repository.
Navigate to GitHub Actions and select configure for a new Simple workflow. Change the filename
suggestion from blank.yml to calculator_pipeline.yml and choose Start commit  Commit new file.
GitHub have now created a pipeline defintion for you in your-repository name/.github/workflows/calculator_pipeline.yml.
In GitHub pipelines are refered to as Workflows. The created pipeline template includes a job called
build and that job contains two steps that prints some text in the console. As soon as the
calculator_pipeline.yml-file was commited to the repository it was also executed.
Navigate in GitHub and find the pipeline (workflow) and find the results of the executed job and
steps. You should be able to find the output of the steps executed, i.e. the Hello, world! message.
5. Build and push your Docker image in the pipeline
The pipeline should be executed on all changes on the main branch in the repository, this setting
should be there by default when you created the workflow template. You should update the
pipeline to contain 1 job with 3 different steps that performs the following:
• Checkout the code from the repository.
• Login to container registry (judevops.azurecr.io) with the credentials.
• Create a new Docker image based in the files in the repository and push it to the registry.
(This can be done using the same shell commands used in previous steps, that is, docker build 
docker push with correct arguments.)
Make sure to give your different steps descriptive names.
Information and examples of how Workflows are implemented can be found here:
There is a special GitHub Action that should be used to signin to the Azure Container Registry. 3
NOTE
You should avoid adding the credentials to the container registry directly in the
yaml/workflow-file - even if it’s possible! You should use GitHub Secrets to store the
username and password and refer to them within the pipeline according to the
example.
6. Verify your solution
Do a small change to the files in the code repository that changes the output when you run the
container. For example you can update the statements that prints the results in the calculator.py
-file (look at line 46 and onwards…), so that it prints something slightly different.
When the change is done, push the change to your GitHub repository. Verify that the workflow was
executed automatically and succeeded. Then verify that if you execute a docker pull, to download
the image from the Docker registry to your local computer, and then execute docker run, you see
the updated output from the container.  
Now you have your first version of a Continuous Delivery pipeline!
4

         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
