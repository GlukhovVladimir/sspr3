pipeline {
    agent any
    environment {
		DOCKERHUB_CREDENTIALS=credentials('riptized1')
	}
    stages {
        stage('Cloning Git') {
          steps {
              git([url: 'https://github.com/GlukhovVladimir/sspr3.git', branch: 'master'])
          }
        }
        stage('Build') {

			steps {
				sh 'docker build -t riptized/sspr:latest .'
			}
		}
        stage('Test') {
            steps {
				sh 'docker stop $(docker ps -a -q)'
				sh 'docker rm $(docker ps -a -q)'
				sh 'docker run -d --name "test_sspr" riptized/sspr:latest bash'
				sh 'docker exec "test_sspr" sh -c "dotnet vstest TestService.dll"'
				sh 'docker stop "test_sspr"'
            }
        }

        stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push') {

			steps {
				sh 'docker push riptized/sspr:latest'
			}
		}
    }
    post {
		always {
			sh 'docker logout'
		}
	}
}
