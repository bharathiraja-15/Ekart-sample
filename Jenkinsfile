pipeline {
    agent any

    tools {
        jdk 'jdk11'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME = tool 'SonarQubeScanner'
    }

    stages {

        stage('git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/bharathiraja-15/Ekart-sample.git'
            }
        }

        stage('compile') {
            steps {
                sh "mvn clean install -DskipTests"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh """
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=shopping-cart \
                    -Dsonar.projectName="Shopping Cart" \
                    -Dsonar.sources=. \
                    -Dsonar.java.binaries=target/classes \
                    -Dsonar.host.url=http://13.61.5.83:9000/ \
                    -Dsonar.login=squ_8f4da96d680a434a5a94b7bc5ce690b8ca0c2f7c
                    """
                }
            }
        }

       

        stage('Build') {
    steps {
        sh "mvn clean install -DskipTests"
    }
}

        stage('Build and Push Docker') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker']) {
                        sh '''
                            docker build -t shopping:latest -f docker/Dockerfile .
                            docker tag shopping:latest bharathiraja3234/shopping:latest
                            docker push bharathiraja3234/shopping:latest
                        '''
                    }
                }
            }
        }

        stage('Trigger CD Pipeline') {
    steps {
        script {
            def result = build job: 'cd_pipeline', wait: true
            echo "CD Pipeline Result: ${result.result}"
        }
    }
}
    }
}
