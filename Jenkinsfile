
pipeline {
  agent { label 'docker' }

  parameters {
      string(name: 'name_container', defaultValue: 'proyecto-qa', description: 'docker name')
      string(name: 'image_name', defaultValue: 'iproyecto-qa', description: 'image name')
      string(name: 'image_tag', defaultValue: 'latest', description: 'image tag')
      string(name: 'image_port', defaultValue: '81', description: 'port to publish')
  }
  environment {
    name_final = "${name_container}${image_tag}${image_port}"
  }
  stages {
    stage('stop/rm') {
      when {
        expression {
          /* groovylint-disable-next-line GStringExpressionWithinString */
          DOCKER_EXIST = sh(returnStdout: true, script: 'echo "$(docker ps -q --filter name=${name_final})"').trim()
          return  DOCKER_EXIST != ''
        }
      }
      steps {
        script {
          /* groovylint-disable-next-line GStringExpressionWithinString */
          sh '''
            docker stop ${name_final}
            '''
        }
      }
    }

    stage('build') {
      steps {
        script {
          sh '''
            docker build -t ${image_name}:${image_tag} .
          '''
        }
      }
    }
    stage('run') {
      steps {
        script {
          /* groovylint-disable-next-line GStringExpressionWithinString */
          sh '''
            docker run -dp ${image_port}:80 --name ${name_final} ${image_name}:${image_tag}
          '''
        }
      }
    }
  }
  post {
        always {
            junit 'build/reports/**/*.xml'
        }
    }
}
