pipeline {
    agent any

    environment {
        // Correct way to bind AWS credentials in a declarative pipeline
        AWS_ACCESS_KEY_ID     = credentials('aws-s3-backup-creds')
        AWS_SECRET_ACCESS_KEY = credentials('aws-s3-backup-creds')
        S3_BUCKET             = 's3://my-jenkins-backup-bucket-unique-id'
    }

    stages {
        // We removed the manual 'Checkout' stage because Jenkins does it automatically
        
        stage('Archive & Compress') {
            steps {
                echo "Packaging files from workspace..."
                // This archives the code Jenkins just pulled from Git
                sh 'tar -czf jenkins-project.tar.gz .'
            }
        }

        stage('Upload to AWS S3') {
            steps {
                echo "Pushing artifact to AWS..."
                // AWS CLI automatically uses the AWS_ACCESS_KEY_ID env variables
                sh "aws s3 cp jenkins-project.tar.gz ${S3_BUCKET}/backups2/"
            }
        }
    }

    post {
        always {
            echo "Cleaning up workspace..."
            sh 'rm -f *.tar.gz'
        }
        success {
            echo "Deployment to S3 successful!"
        }
        failure {
            echo "Build failed. Please check the 'hhttps' typo in your SCM settings or Jenkinsfile."
        }
    }
}
