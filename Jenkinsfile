
pipeline {
    agent any
     tools{
         jdk 'Java17'
         maven 'Maven'
    }
    stages {
        stage('Checkout Code') {
            steps {
               echo "Pulling from GITHUB repository"
               git branch: 'main', credentialsId: 'mygithubcred', url: 'https://github.com/Bhatnagar09/demo.git'
            }
        }
         stage('Test the Project') {
            steps {
               echo "Test my JAVA project"
               bat 'mvn clean test' 
            }
              post {
                  always {
                         junit '**/target/surefire-reports/*.xml'
                         echo 'Test Run succeeded!'          
					}
				}
		}
        stage('Build Project') {
            steps {
               echo "Building my JAVA project"
               bat 'mvn clean package -DskipTests' 
            }
        }
        stage(' Build the Docker Image') {
            steps {
               echo "Build the Docker Image for mvn project"
               bat 'docker build -t mvnproj:1.0 .'
            }
        }
         stage('Push Docker Image to DockerHub') {
    steps {
        echo 'Pushing Docker image to DockerHub'

        withCredentials([
            usernamePassword(
                credentialsId: 'dockerhubpwd',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )
        ]) {
            bat '''
                docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                docker tag mvnproj:1.0 %DOCKER_USER%/mvnproj:1.0
                docker push %DOCKER_USER%/mvnproj:1.0
            '''
        }
    }
}
       
         stage('Deploy the project using Container') {
            steps {
                echo "Running Java Application"
                bat '''
	               docker rm -f myjavaappcont || exit 0
	               docker run --name myjavaappcont pbhatnagar26/mymvnproj:latest
	           '''
            }
        }
    }

    post {
        success {
            echo 'I succeeded!'
           
        }
        failure {
            echo 'Failed........'
        }
    }
}
