node {
    
    stage('Git Checkout'){
        git 'https://github.com/ramakrishna8254/k8s_helm.git'
    }
    
    stage('Sending docker file to ansible over ssh'){
        sshagent(['ansible_demo']) {
          sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155'
          sh 'scp /var/lib/jenkins/workspace/webserverhttp/* ec2-user@43.204.185.155:/home/ec2-user/'
            }
    } 
    stage('Docker Build Image'){
        sshagent(['ansible_demo']) {
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 cd /home/ec2-user/'
             sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 docker image build -t $JOB_NAME:v1.$BUILD_ID .'
        }
    }
    stage('Docker Image tagging'){
        sshagent(['ansible_demo']) {
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 cd /home/ec2-user/'
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 docker image tag $JOB_NAME:v1.$BUILD_ID  ramakrishna8254/$JOB_NAME:v1.$BUILD_ID '
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 docker image tag $JOB_NAME:v1.$BUILD_ID ramakrishna8254/$JOB_NAME:latest '
            }
    }    
    stage('Push Docker images to Docker Hub'){
        sshagent(['ansible_demo']){
            withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'DOCKER_HUB_PASSWORD')]){
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 docker login -u ramakrishna8254 -p ${DOCKER_HUB_PASSWORD}'
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 docker image push ramakrishna8254/$JOB_NAME:v1.$BUILD_ID '
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 docker image push ramakrishna8254/$JOB_NAME:latest '
           
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 docker image rm ramakrishna8254/$JOB_NAME:v1.$BUILD_ID ramakrishna8254/$JOB_NAME:latest '
            }
        }
    }
    stage('Copy files from ansible to K8s'){
        sshagent(['kubernetes_server']) {
         sh 'ssh -o StrictHostKeyChecking=no ec2-user@13.234.61.205'
         sh 'scp /var/lib/jenkins/workspace/webserverhttp/* ec2-user@13.234.61.205:/home/ec2-user/'
        }
    }
    stage('Kubernetes Deoplyment using ansible'){
        sshagent(['ansible_demo']) {
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 cd /home/ec2-user/'
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 ansible -m ping node'
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.185.155 ansible-playbook ansible.yml'
            }
    }
}
