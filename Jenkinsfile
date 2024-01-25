node {
   def registryProjet='registry.gitlab.com/plaga1/presentations-jenkins'
   def IMAGE="${registryProjet}:compil-${env.BUILD_ID}"

    stage('Build - Clone') {
          git 'https://github.com/hubabdou/war-build-docker.git'
    }
    stage('Build - Maven package'){
            sh 'mvn package'
    }
     
   
def img = stage('Build') {
          docker.build("$IMAGE",  '.')
    }

  
   stage('Build - Test') {
            img.withRun("--name run-$BUILD_ID -p 8081:8080") { c ->
            sh 'docker ps'
            sh 'netstat -ntaup'
            sh 'sleep 15s'
            sh 'curl 127.0.0.1:8081'
            sh 'docker ps'
          }
    }

   stage('Build - Push') {
          docker.withRegistry('https://registry.gitlab.com', 'reg1') {
              img.push 'latest'
              img.push()
          }
    }


    stage('Deploy - Clone') {
          git 'https://github.com/hubabdou/jenkins-ansible-docker.git'
    }
     
    stage('Deploy - End') {
      ansiblePlaybook (
          colorized: true,
          become: true,
          playbook: 'playbook.yml',
          inventory: '${HOST}',
          extras: "--user=jenkins --extra-vars 'ansible_sudo_pass=1234 image=$IMAGE'"
       )
    }

}

