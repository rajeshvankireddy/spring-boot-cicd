pipeline {
  agent {
    docker {
      image 'abhishekf5/maven-abhishek-docker-agent:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    }
  }
  stages {
    stage('Checkout') {
      steps {
        // sh 'echo passed'
        git branch: 'main', url: 'https://github.com/rajeshvankireddy/spring-boot-cicd'
      }
    }
    stage('Build and Test') {
      steps {
        sh 'ls -ltr'
        // build the project and create a JAR file
        sh 'cd spring-boot-cicd && mvn clean package'
      }
    }
    stage('Static Code Analysis') {
      environment {
        SONAR_URL = "http://43.205.142.144:9000/"
      }
      steps {
        withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
          sh 'cd spring-boot-cicd && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
        }
      }
    }
    stage('Build and Push Docker Image') {
      environment {
<<<<<<< HEAD:Jenkinsfile
        DOCKER_IMAGE = "rajvrk/springboot-ci-cd:${BUILD_NUMBER}"
=======
        DOCKER_IMAGE = "rajvrk/springbootcicd:${BUILD_NUMBER}"
>>>>>>> origin/main:JenkinsFile.txt
        // DOCKERFILE_LOCATION = "spring-boot-cicd/Dockerfile"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
      }
      steps {
        script {
            sh 'cd spring-boot-cicd && docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                dockerImage.push()
            }
        }
      }
    }
    stage('Update Deployment File') {
        environment {
            GIT_REPO_NAME = "spring-boot-cicd"
            GIT_USER_NAME = "rajeshvankireddy"
        }
        steps {
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                    git config user.email "rajeshvankireddy45@gmail.com"
                    git config user.name "rajeshvankireddy"
                    BUILD_NUMBER=${BUILD_NUMBER}
<<<<<<< HEAD:Jenkinsfile
                    sed -i 's/replaceImageTag/'"$BUILD_NUMBER"'/g' spring-boot-cicd/deployment.yml
                    apt-get update && apt-get install -y wget
                    wget https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O /usr/bin/yq
                    chmod +x /usr/bin/yq
                    yq eval '.spec.template.spec.containers[] |= select(.name == "spring-boot-app").image = "'"$IMAGE_NAME:$BUILD_NUMBER"'"' -i java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
                    git add spring-boot-cicd/deployment.yml
=======
                    sed -i 's/replaceImageTag/'"$BUILD_NUMBER"'/g' spring-boot-ci-cd/deployment.yml
                    apt-get update && apt-get install -y wget
                    wget https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O /usr/bin/yq
                    chmod +x /usr/bin/yq
                    yq eval '.spec.template.spec.containers[] |= select(.name == "spring-boot-app").image = "'"$IMAGE_NAME:$BUILD_NUMBER"'"' -i spring-boot-ci-cd/deployment.yml
                    git add spring-boot-app-ci-cd/deployment.yml
>>>>>>> origin/main:JenkinsFile.txt
                    git commit -m "Update deployment image to version $BUILD_NUMBER" || echo "No changes to commit"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main

                '''
            }
        }
    }
  }
}
