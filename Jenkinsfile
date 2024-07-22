pipeline {
    agent any
environment {
  SCANNER_HOME = tool 'sonar-scanner'
  GIT_TAG = sh(script: "git describe --tags --abbrev=0 dev", returnStdout: true).trim()
        DOCKER_IMAGE_NAME = 'steve-dev'
        DOCKER_REGISTRY = 'stevedev.azurecr.io'
        DOCKER_REPO = 'stevedev'
        DOCKER_IMAGE_TAG = "${DOCKER_REGISTRY}/${DOCKER_REPO}:${GIT_TAG}"
        ACR_URL = 'stevedev.azurecr.io'
}
    stages {
        stage('Fectch Code') {
            steps {
                git branch: 'dev', credentialsId: 'bbat', url: 'https://ijazbro@bitbucket.org/ijazbro/steve-2024.git'
            }
        }
    /*    stage('Code Compilation') {
            steps {
               sh 'mvn install -Dlicense.skip=true -P dev' 
            }    
        } 
        /*stage('Code Quality checks') {
            steps {
               sh 'mvn checkstyle:checkstyle pmd:pmd findbugs:findbugs -Dlicense.skip=true -P dev'
               sh 'mvn -B org.owasp:dependency-check-maven:aggregate -DnvdApiKey="b58968ac-4bac-49c7-a259-e237b2df3ef0" -Dformats=XML,JSON,HTML -Dlicense.skip=true -P dev'

               sh 'mvn -B sonar:sonar -Dsonar.projectKey=AKS-Steve  -Dsonar.host.url=http://10.0.0.35:9090 -Dsonar.login=squ_205603b4df5adcdb2be6bbfc9fdd97cf0e0b999d'
            }    
        }
             stage('OWASP Dependency Check'){
            steps{
                dependencyCheckPublisher pattern: './dependency-check-report.xml'
                
            }
        } */
            
        stage('Dokcer build and Tag') {
            steps {
               sh 'docker image prune -a -f'   
              // Build and tag the Docker image with Git tag
                    sh "docker build -t ${DOCKER_IMAGE_TAG} --build-arg DB_HOST=siriusdb.mysql.database.azure.com --build-arg DB_PORT=3306 --build-arg DB_USERNAME=steve --build-arg DB_PASSWORD=KoalaBear@5pm --build-arg DB_DATABASE=siriusdb --build-arg ADMIN_USERNAME=admin --build-arg ADMIN_PASSWORD='OliveTree4m&u' --build-arg GIT_TAG=${GIT_TAG}  -f my-dockerfile2 ."
                
               }
        }
      /*  stage('Docker run and push') {
            steps {
            sh "chmod a+x docker.sh"
            sh "sh docker.sh"
            echo "TAG is ${GIT_TAG}"
             sh "sudo docker run -d --name=steve-${GIT_TAG} -p 8188:8888 ${DOCKER_IMAGE_TAG} "
            
            }
            }
            
          /*  stage('Check Container Status') {
            steps {
                script {
                    try {
                        // Check if the Docker container is running
                        def containerStatus = sh(script: 'docker inspect -f "{{.State.Running}}" steve-${GIT_TAG}', returnStdout: true).trim()

                        if (containerStatus != 'true') {
                            error 'Docker container failed to start.'
                        }

                        echo 'Docker container started successfully.'
                        echo 'We can push the image'
                    } catch (Exception e) {
                        echo 'Error: Docker container failed to start.'
                        error 'Failing the pipeline as the Docker container is not running.'
                    }
                }
            }
            }*/
            
            stage('Push Docker Image') {
            steps {
                script {
                withDockerRegistry(credentialsId: 'ACR', url: "https://${ACR_URL}") {
                        docker.image("${DOCKER_IMAGE_TAG}").push()
            }
            }
            }
            }
            
           /*  stage('Trivy Docker Image Scan for Vulnerability') {
            steps {
              sh " trivy image ijazu/steve:dev-$BUILD_NUMBER --format template --template ''@/usr/bin/html.tpl'' -o docker-report.html "
            }
        } */ 
        
        stage('Kube Deploy') {
            steps {
        
             script {
                 withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'AKS', namespace: 'steve-dev', restrictKubeConfigAccess: false, serverUrl: '')
                 {
              //sh "helm upgrade steve-dev --install steve-dev --namespace steve-dev --set-string app.container.tag=${GIT_TAG}"
                 sh "kubectl apply -f /var/lib/jenkins/workspace/Steve-in-AKS/k8s/yaml"
 }
            }
        }
        }
        
       /* stage('Pod Status Checking') {
            steps {
                script {
                    try

                        // Wait for the deployment to be rolled out successfully
                        sh 'kubectl rollout status deployment/your-deployment-name --timeout=30s'
                        
                        echo 'Deployment was successful.'
                    } catch (Exception e) {
                        echo 'Deployment failed. Rolling back...'
                        
                        // Rollback the deployment
                        sh 'helm rollback <RELEASE_NAME>'
                        
                        error 'Deployment failed and has been rolled back.'
                    }
                }
            }
        
    }*/
    
    }
}

