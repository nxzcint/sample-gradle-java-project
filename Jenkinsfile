pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_S3_BUCKET = "grdele-bucket"
        ARTIFACT_NAME = "gradle-wrapper.jar"
        AWS_EB_APP_NAME = "ahmed-gradle1"
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "Ahmedgradle1-env"

    }
    stages {
        stage('Validate') {
            steps {
                sh "chmod +x gradlew"
                sh "./gradlew clean"
            }
        }
        stage('Build') {
            steps {
                sh "./gradlew build"
                sh "./gradlew assemble"

            }
        }
        stage('Test') {
            steps {
                sh "./gradlew test"
            }
      
        }

        stage('Package') {
            steps {
                sh "./gradlew jar"
            }
            post{
                success{
                    archiveArtifacts artifacts: 'gradle/wrapper/*.jar', followSymlinks: false
                }
            }
        }
        stage('Publish artifacts to S3 Bucket') {
            steps {
                sh "aws configure set region us-east-1"
                sh "aws s3 cp ./build/libs/Gradle-java-spring-boot-app-0.0.1-SNAPSHOT.jar s3://$AWS_S3_BUCKET/$ARTIFACT_NAME"
            }
         }
        stage('Deploy') {
            steps {
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
            }
         }
        

    }
}
