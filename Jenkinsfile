pipeline {
    agent {
        label 'slave'  // Ensure this label matches the label of your Jenkins agent node (EC2 B)
    }
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Checkout your code from version control (if applicable)
                    checkout scm
                    
                    // Clone the GitHub repository
                    sh 'git clone https://github.com/S-Bhagath/fuse.git'
                    
                    // Navigate into the cloned repository directory
                    dir('fuse') {
                        // Build the Docker image on Docker Host (EC2 C)
                        // Using the Docker Host IP (54.211.35.246)
                        sh 'ssh -o StrictHostKeyChecking=no ec2-user@54.211.35.246 "docker build -t my-web-server /path/to/fuse"'
                    }
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    // Run the Docker container on Docker Host (EC2 C)
                    // Using the Docker Host IP (54.211.35.246)
                    sh 'ssh -o StrictHostKeyChecking=no ec2-user@54.211.35.246 "docker run -d -p 8080:80 --name web-server my-web-server"'
                }
            }
        }
        stage('Verify') {
            steps {
                script {
                    // Use the Docker Host IP (54.211.35.246) to access the web server
                    def dockerHostIp = '54.211.35.246'  // IP address of Docker Host
                    def response = sh(script: "curl -s http://${dockerHostIp}:8080", returnStdout: true).trim()
                    echo "Response from web server:\n${response}"
                }
            }
        }
    }
    post {
        always {
            // Clean up Docker container on Docker Host (EC2 C)
            // Using the Docker Host IP (54.211.35.246)
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@54.211.35.246 "docker rm -f web-server || true"'
        }
    }
}

