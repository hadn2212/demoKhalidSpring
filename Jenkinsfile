pipeline {

    agent any

    tools { 
        maven 'khalidLabMaven' 
    }
    environment {
        MYSQL_ROOT_LOGIN = credentials('mysql-root-khalidLab')
    }
    stages {

        stage('Build with Maven') {
            steps {
				echo 'Build 1'
                sh 'mvn --version'
				echo 'Build 2'
                sh 'java -version'
				echo 'Build 3'
                sh 'mvn clean package -Dmaven.test.failure.ignore=true'
            }
        }

        stage('Packaging/Pushing imagae') {

            steps {
				echo 'Build 4'
                withDockerRegistry(credentialsId: 'dockerhub-khalidLab', url: 'https://index.docker.io/v1/') {
					echo 'Build 5'
                    sh 'docker build -t ngocha2212/springbootkhalid .'
					echo 'Build 6'
                    sh 'docker push ngocha2212/springbootkhalid'
                }
            }
        }

        stage('Deploy MySQL to DEV') {
            steps {
                echo 'Deploying and cleaning'
                sh 'docker image pull mysql:8.0'
                sh 'docker network create dev || echo "this network exists"'
                sh 'docker container stop khalid-mysql || echo "this container does not exist" '
//                sh 'echo y | docker container prune '
                sh 'docker volume rm khalid-mysql-data || echo "no volume"'

                sh "docker run --name khalid-mysql --network dev -v khalid-mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_LOGIN_PSW} -e MYSQL_DATABASE=db_example  -d mysql:8.0 "
//				--rm \
                sh 'sleep 20'
                sh "docker exec -i khalid-mysql mysql --user=root --password=${MYSQL_ROOT_LOGIN_PSW} < script"
            }
        }

        stage('Deploy Spring Boot to DEV') {
            steps {
                echo 'Deploying and cleaning'
                sh 'docker image pull ngocha2212/springbootkhalid'
                sh 'docker container stop ngocha2212-springbootKhalidCont || echo "this container does not exist" '
                sh 'docker network create dev || echo "this network exists"'
//                sh 'echo y | docker container prune '

                sh 'docker container run -d --name ngocha2212-springbootKhalidCont -p 8081:8080 --network dev ngocha2212/springbootkhalid'
//				--rm \	
            }
        }
 
    }
    post {
        // Clean after build
        always {
            cleanWs()
        }
    }
}
