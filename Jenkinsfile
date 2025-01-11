pipeline {
    agent any

    environment {
        // Define the role path where the role is stored
        ANSIBLE_ROLES_PATH = "${WORKSPACE}/roles"
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clone the repository containing your Ansible files
                git 'https://github.com/Prabhakanthgc1995/tomcat.git'
            }
        }

        stage('Create Ansible Role (if necessary)') {
            steps {
                script {
                    // Check if the tomcat role exists, and if not, create it
                    def tomcatRolePath = "${env.WORKSPACE}/roles/tomcat"
                    if (!fileExists(tomcatRolePath)) {
                        echo "Creating Tomcat Ansible Role"
                        sh """
                            mkdir -p ${tomcatRolePath}/tasks
                            mkdir -p ${tomcatRolePath}/handlers
                            mkdir -p ${tomcatRolePath}/defaults
                            echo '---
- name: Install Tomcat
  yum:
    name: tomcat
    state: present

- name: Start Tomcat service
  service:
    name: tomcat
    state: started
    enabled: yes' > ${tomcatRolePath}/tasks/main.yml

                            echo '---
- name: Restart Tomcat
  service:
    name: tomcat
    state: restarted' > ${tomcatRolePath}/handlers/main.yml

                            echo '---' > ${tomcatRolePath}/defaults/main.yml
                        """
                    } else {
                        echo "Tomcat Ansible Role already exists"
                    }
                }
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                script {
                    // Run the Ansible playbook to install Tomcat
                    sh """
                        ansible-playbook -i inventory/hosts deploy_tomcat.yml
                    """
                }
            }
        }

        stage('Post Deployment Check') {
            steps {
                script {
                    // Check if Tomcat is running by sending a simple HTTP request
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

    post {
        always {
            // Clean up or perform any necessary post-actions
            echo 'Cleaning up'
        }
        success {
            echo 'Tomcat installed and running successfully!'
        }
        failure {
            echo 'There was a failure in the pipeline.'
        }
    }
}
