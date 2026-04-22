pipeline {
    agent any

    environment {
        // Use the ID you created in the Credentials section earlier
        AWS_CREDS = credentials('aws-s3-backup-creds')
        S3_BUCKET = 's3://my-jenkins-backup-bucket-unique-id'
    }

    stages {
        stage('Checkout') {
            steps {
                // Pulls code from your Git repo
                git branch: 'main', url: 'https://github.com/your-username/your-repo.git'
                echo "Code successfully retrieved from Git."
            }
        }

        stage('Archive & Compress') {
            steps {
                echo "Packaging files..."
                sh 'tar -czf jenkins-project.tar.gz .'
            }
        }

        stage('Upload to AWS S3') {
            steps {
                echo "Pushing artifact to AWS Infrastructure..."
                // Using AWS CLI with environment variables
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
            echo "Something went wrong. Check the logs above."
        }
    }
}
