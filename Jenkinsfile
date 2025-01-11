pipeline {
    agent none  // No agent assigned globally

    stages {
        // Stage to clone the repository
        stage('Clone Repository') {
            agent any
            steps {
                git 'https://github.com/Prabhakanthgc1995/tomcat.git'
            }
        }

        // Stage to deploy to Tomcat using Ansible
        stage('Deploy to Tomcat') {
            agent { label 'dev' }  // Run this on the node with label 'dev'
            steps {
                script {
                    // Export the ANSIBLE_ROLES_PATH environment variable
                    env.ANSIBLE_ROLES_PATH = '/home/ubuntu/workspace/tomcat1/'

                    // Ensure Ansible is installed on the node
                    sh 'ansible --version'

                    // Run the Ansible playbook to deploy to Tomcat
                    sh 'ansible-playbook -i inventory/hosts deploy_tomcat.yml'
                }
            }
        }

        // Post-deployment check
        stage('Post Deployment Check') {
            agent { label 'production' }
            steps {
                script {
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
