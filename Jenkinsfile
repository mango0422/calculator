pipeline{
    environment{
        repository = 'mango0422/calculator'
        DOCKERHUB_CREDENTIALS = credentials('dockerToken')
        dockerImage = ''
    }
    agent any
    stages{
        stage("Compile"){
            steps{
                sh "./gradlew compileJava"
            }
        }
        stage("Build"){
            steps{
                sh "./gradlew build"
            }
        }
        stage("Unit test"){
            steps{
                sh "./gradlew test"
            }
        }
        stage('Docker build'){
            steps {
                // 첫번째 배포시에만 주석 풀고 아래 주석처리 후 실행. 이후에는 처음 두줄 주석처리 후 이후 주석처리 해제.
                // sh '''docker build -t mango0422/homepage:$(docker images | awk '($1 == "mango0422/homepage") {print $2 += .01; exit}') .'''
                // sh '''docker tag mango0422/homepage:$(docker images | awk '($1 == "mango0422/homepage") {print $2; exit}') mango0422/homepage:latest'''
                script {
                    dockerImage = docker.build repository + ":$BUILD_NUMBER"
                }
                // sh '''docker build -t mango0422/homepage:0.1 .'''
                // sh '''docker tag mango0422/homepage:0.1 mango0422/homepage:latest'''
                // sh '''docker tag mango0422/spring:$(docker images | awk '($1 == "mango0422/spring") {print $2; exit}') mango0422/spring:latest'''
                // sh 'docker login -u "tom990422@gmail.com" -p $DOCKERHUB_CREDENTIALS'
                // sh '''docker push mango0422/spring:$(docker images | awk '($1 == "mango0422/spring") {print $2; exit}')'''
            }
        }
        stage('Login'){
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin' // docker hub 로그인
            }
        }
        stage('Deploy our image') { 
            steps { 
                script {
                    sh 'docker push $repository:$BUILD_NUMBER' //docker push
                } 
            }
    } 
        stage('Deploy') {
            steps {
                sh 'docker rm -f calculator'
                sh 'docker run -d -p 8081:8080 --name calculator mango0422/calculator'
            }
        }
    }
}