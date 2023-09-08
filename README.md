PROJECT 3: Jenkins Multibranch Pipelines and Folders Task 
STEPS TO CREATE MULTI BRANCH PIPELINE WITH FOLDER:
1.	Create an EC2 instance in AWS console, I create an instance with name “Jenkins_project”.
     ![image](https://github.com/sainakka5/MULTIBRANCH_PIPELINE_WITH_BUILD_AUTOMATION_CODE/assets/136338958/eba3e614-5d39-4538-9b80-2380c0142331)

 
2.	Install the Jenkins and its requirements in that instance using Jenkins installation code in Jenkins documentary for centos 7.

        sudo yum install wget -y

        sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
        sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

        yum install fontconfig java-11-openjdk -y
        yum install jenkins -y
        service jenkins start
        systemctl enable jenkins
        service jenkins status

3.	After execution of the above script, enable the http (8080) port in your Jenkins server security group then login into Jenkins with your ip address like, give your <public_ip:8080> on your browser and give the authentication password, which you can find it in path 
 < /var/jenkins_home/secrets/initialAdminPassword > of Jenkins server give that password and login to the Jenkins webserver.
4.	Install the required plugins for our project and create a new folder by clicking on new item and give a name to that folder 
click on the folder option as shown in the below fig, then it will create a folder to create multibranch pipeline.
 ![image](https://github.com/sainakka5/MULTIBRANCH_PIPELINE_WITH_BUILD_AUTOMATION_CODE/assets/136338958/a35036b3-695d-48b2-8ac2-0fb1b2c981ea)


5.	Click on the folder you created and, in that folder, create a multibranch pipeline, again click on new item and give the name as “Mymultibranchpipeline” click on multibranch pipeline and click ok then it will create a pipeline.
 ![image](https://github.com/sainakka5/MULTIBRANCH_PIPELINE_WITH_BUILD_AUTOMATION_CODE/assets/136338958/226ed0d3-20c5-4284-9c53-a5aece2ae7fe)

6.	In that multibranch pipeline go to configuration> branch source > add the git and give the URL of your git repository which includes a code with different branches, to build different pipelines for these branches.
7.	The code and repository are mentioned in the below figures if it was a private repo give credentials.
8.	Here I had created 3 branches but 2 branches named main, new-branch are allowed for build pipeline for this task I had add Discover branches, Filter by name (with wildcards) and include your branches there.
9.	Enable scan webhook option in Scan Multibranch Pipeline Triggers, give the URL of webhook in GitHub repository setting. This option helps to build the pipeline when ever we changed and commit the repository file. Instead of clicking scan option every time just give this option for automatic build of multibranch pipeline. These are done as shown in below figures.
            ![image](https://github.com/sainakka5/MULTIBRANCH_PIPELINE_WITH_BUILD_AUTOMATION_CODE/assets/136338958/37bd1b1f-57c2-47fa-a080-9da1ee0b29a0)

       ![image](https://github.com/sainakka5/MULTIBRANCH_PIPELINE_WITH_BUILD_AUTOMATION_CODE/assets/136338958/009de06a-8f41-4024-9296-228ac93f176d)

     
10.	In the below fig there is a code to replace the webhook tool called poll scm it takes time intervals to build the change in branch.

    
       ![image](https://github.com/sainakka5/MULTIBRANCH_PIPELINE_WITH_BUILD_AUTOMATION_CODE/assets/136338958/35f2d7a5-bb5a-4b8a-98fd-7b3d92443ee4)

     ![image](https://github.com/sainakka5/MULTIBRANCH_PIPELINE_WITH_BUILD_AUTOMATION_CODE/assets/136338958/eb8ba322-0659-4db1-a45f-1b3a7e9e633f)
      ![image](https://github.com/sainakka5/MULTIBRANCH_PIPELINE_WITH_BUILD_AUTOMATION_CODE/assets/136338958/b67997f0-3b58-48b4-974e-c2ecb110eb7a)
     ![image](https://github.com/sainakka5/MULTIBRANCH_PIPELINE_WITH_BUILD_AUTOMATION_CODE/assets/136338958/d53d3d53-c553-4bab-a697-a82f9b182657)

 
 
11.	After saving all the options in configuration of multibranch pipeline it will scan and build the different pipelines for allowed branches of repository
if we change the content and commit the changes and come to the Jenkins server it will build a pipeline for the changed branch. Like the below figure.
 
     ![image](https://github.com/sainakka5/MULTIBRANCH_PIPELINE_WITH_BUILD_AUTOMATION_CODE/assets/136338958/c678a998-3105-46c9-ab97-17c917ddb8c7)

	Here I had changed the code in main branch so, the main branch pipeline only get build. It will not touch the remaining branches and pipelines

12.	This is the code which builds the pipelines whenever we change the changes in branches, we have to paste the pollSCM code in your Jenkins file.

                                        pipeline {
                                            agent any
                                            tools {
                                                maven "mvn"
                                            }
                                            
                                            triggers {
                                                pollSCM('* * * * *') // Schedule SCM polling at a specified interval (every minute in this example)
                                            }
                                            stages {
                                                stage('Checkout') {
                                                    steps {
                                                        git branch: 'main', url: 'https://github.com/iamkishore0/maven_project.git'
                                                        // Check out your source code from your SCM repository here
                                                        // For example, you can use 'git' for Git repositories
                                                        // You may need to configure your SCM credentials
                                                        // For Git, it would be something like 'checkout([$class: 'GitSCM', branches: [[name: '*/master']], userRemoteConfigs: [[url: 'https://github.com/your/repo.git']]])'
                                                    }
                                                }
                                                stage('Build ') {
                                                    steps {
                                                        sh "mvn compile"
                                                        // Add your build steps here
                                                    }
                                                }
                                                stage('Test') {
                                                    steps {
                                                        sh 'mvn test'
                                                        // Add your testing steps here
                                                    }
                                                }
                                                stage('Deploy') {
                                                    steps {
                                                        sh 'mvn install'
                                                        // Add your deployment steps here
                                                    }
                                                }
                                            }
                                        }
14.	Multibranch pipelines in Jenkins are an efficient way to handle multiple branches within a single pipeline project. They automatically discover branches in a repository and create corresponding Jenkins pipelines. This feature simplifies the management of continuous integration and delivery for various branches.

Advantages of Multibranch Pipelines:
1.	Automated Branch Detection: Multibranch pipelines automatically discover branches, reducing the need for manual job creation and maintenance.
2.	Isolation of Builds: Each branch has its own pipeline, ensuring that changes in one branch do not interfere with other branches.
3.	Improved Code Quality: By running builds and tests on each branch, developers can identify and fix issues early in the development process.
4.	Streamlined Pipeline Configuration: Multibranch pipelines use a Jenkinsfile in the repository, allowing version-controlled pipeline definitions and making it easier to modify the pipeline behavior.
5.	Regarding folders, they offer a way to organize jobs in Jenkins, reducing clutter and making it easier to manage multiple jobs or projects. Folders help maintain a more structured and logical view of your Jenkins jobs, especially when dealing with a large number of projects.
6.	To complete this assignment, students should demonstrate their ability to set up Jenkins, create multibranch pipelines, use Jenkinsfiles to define build stages, and understand the benefits of organizing jobs into folders.
