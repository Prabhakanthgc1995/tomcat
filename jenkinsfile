pipeline {
    agent none  // No agent assigned globally

    stages {
        // Stage to clone the repository
        stage('Clone Repository') {
            agent any  // Use any available agent (including master or a node)
            steps {
                // Clone the Git repository
                git 'https://github.com/Prabhakanthgc1995/tomcat.git'
            }
        }
        
        // Stage to install dependencies
        stage('Install Dependencies') {
            agent any  // This can also run on any node
            steps {
                script {
                    // Ensure Ansible and Git are available on the agent
                    sh 'ansible --version'
                    sh 'git --version'
                }
            }
        }

        // Stage to deploy to Tomcat using Ansible
        stage('Deploy to Tomcat') {
            agent { label 'dev' }  // Run this on the node with label 'dev'
            steps {
                script {
                    // Run the Ansible playbook to deploy to Tomcat
                    // Make sure deploy_tomcat.yml and inventory/hosts exist in the workspace
                    sh 'ansible-playbook -i inventory/hosts deploy_tomcat.yml'
                }
            }
        }

        // Stage for post-deployment testing
        stage('Post Deployment') {
            agent { label 'production' }  // Run this on the node with label 'production'
            steps {
                script {
                    // Validate if Tomcat is running after deployment (check HTTP response)
                    sh 'curl -I http://your-tomcat-server:8080'
                }
            }
        }
    }
}
