pipeline {
    agent any
    environment {
            DOCKERHUB_CREDENTIALS = credentials('mihaivalentingeorgescu-dockerhub')
    }
    stages {
        stage('Checkstyle') {
            steps {
                sh 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64'
                sh 'echo $JAVA_HOME'
                sh 'mvn checkstyle:checkstyle'
                archiveArtifacts artifacts: 'checkstyle-result.html', onlyIfSuccessful: true
            }
        }
        stage('Tests') {
            steps {
                echo "now we will begin the tests"
                sh 'mvn test'
            }
        }
        stage('Build') {
            steps {
                echo "now we will begin the build"
                script {
                    def appBuildoutPut = sh (script: 'mvn clean package -Dskiptests', returnStatus: true)
                    if (appBuildoutPut == 0) {
                        echo "build SUCCESSFUL"
                    } else {
                        error "Build failed"
                    }
                }
            }
        }
        stage('Create docker image') {
            steps {
                echo "now we will begin the creation of the docker image"
                script {
                    def dockerBuildOutput = sh(script: 'docker build -t imagine_spring_petclinic:0.1 .', returnStatus: true)
                    if (dockerBuildOutput == 0) {
                        echo "Build successful"
                    } else {
                        error "Build failed" // Optional: Terminate the pipeline with an error message if the build fails
                    }
                }
            }
        }
        stage('Tag the docker image') {
            steps {
                script {
                    echo "now we will tag the docker image"
                    sh "docker tag imagine_spring_petclinic:0.1 mihaivalentingeorgescu/mr:0.1"
                }
            }
        }
        stage('Login to Dockerhub') {
            steps {
                echo "now we will login to dockerhub"
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Push to DockerHub') {
            steps {
                echo "now we will push to the docker file"
                sh 'docker push mihaivalentingeorgescu/mr:0.1'
            }
        }
        stage('Change stages') {
            steps {
                echo "this is the start of the new stage with the main branch"
            }
        }
        stage('Create docker image for main branch') {
            steps {
                echo "now we will create the docker image for the main branch"
            }
        }
        stage('Push docker image to main repository') {
            steps {
                echo "now we will push the image to the docker main repository"
            }
        }
    }
}
