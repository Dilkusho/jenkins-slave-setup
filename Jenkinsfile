pipeline {
    agent {
        docker {
            image 'ansible/ansible:default'
            label 'slave-builder'
            args '-u 0:0 -v /home/jenkins/.ssh:/root/.ssh'
        } //docker
    } //agent
    stages {
        stage("Set up") {
            steps {
                sh """
                    pip install ansible
                    apt-get install zip -y
                """
            } //steps
        } //stage
        stage("Build") {
            steps {
                sh """
                    ansible-playbook -i inventory jenkins-slave-setup.yml
                """
            } //steps
        } //stage
        stage("Test") {
            steps {
                sh """
                    ansible jenkins-slaves -m shell -a "ls /home/jenkins/" -i inventory
                    ansible jenkins-slaves -m shell -a "id jenkins" -i inventory
                    ansible jenkins-slaves -m shell -a "cat /home/jenkins/.ssh/authorized_keys" -i inventory
                """
            } //steps
        } //stage
        stage("Deploy") {
            steps {
                sh """
                    zip -r jenkins-slave-setup.zip /home/jenkins/
                    ssh root@10.0.0.37 "mkdir -p /var/www/html/myrepo/"
                    scp jenkins-slave-setup.zip root@10.0.0.37:/var/www/html/myrepo/jenkins-slave-setup.zip
                """
            }
        }
    } //stages
}
