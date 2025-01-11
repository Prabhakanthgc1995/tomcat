pipeline {
    agent none  // No agent assigned globally

    stages {
        // Stage to clone the repository
        stage('Clone Repository') {
            agent any  // Use any available agent
            steps {
                // Clone the Git repository and specify the branch
                git branch: 'main', url: 'https://github.com/Prabhakanthgc1995/tomcat.git'
            }
        }

        // Stage to deploy to Tomcat using Ansible
        stage('Deploy to Tomcat') {
            agent { label 'dev' }  // Run this on the node with label 'dev'
            steps {
                script {
                    // Ensure Ansible is installed on the node (optional, but good practice)
                    sh 'ansible --version'

                    // Run the Ansible playbook to deploy to Tomcat
                    // Make sure deploy_tomcat.yml and inventory/hosts exist in the workspace
                    sh 'ansible-playbook deploy_tomcat.yml'
                }
            }
        }

        // Post-deployment check (optional)
        stage('Post Deployment Check') {
            agent { label 'dev' }  // Run this on the node with label 'production'
            steps {
                script {
                    // Check if Tomcat is up and running by sending a simple HTTP request
                    def result = sh(script: 'curl -I http://your-tomcat-server:8080', returnStatus: true)
                    if (result != 0) {
                        error "Tomcat server is not reachable!"
                    } else {
                        echo "Tomcat server is up and running!"
                    }
                }
            }
        }
    }
}
