pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo "Running on Branch: ${env.BRANCH_NAME}"
                checkout scm
            }
        }


        stage('Unit Tests') {
            steps {
                    echo "Running unit tests in ${env.BRANCH_NAME} branch"
                    sh 'mvn clean test'
            }
        }

        // stage('Trivy Scan') {
        //     steps {
        //             echo "Running Trivy scan in ${env.BRANCH_NAME} branch"
        //             sh '''
        //                 wget -q https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl -O html.tpl
        //                 trivy fs --format template --template "@html.tpl" -o report.html .
        //             '''
        //     }
        // }

        // stage('Sonar Qube Analysis') {
        //     steps {
        //             withSonarQubeEnv('sonar') {
        //                 echo "Running SonarQube analysis in ${env.BRANCH_NAME}"
        //                 sh """
        //                     mvn verify sonar:sonar \
        //                     -Dsonar.projectKey=java-app-${env.BRANCH_NAME} \
        //                     -Dsonar.projectName=java-app-${env.BRANCH_NAME}
        //                 """
        //         }
        //     }
        // }


        stage('Build & Archive') {
            when {
                branch 'main'
            }
            steps {
                echo "Building in main branch"
                sh 'mvn clean package'
               // archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }

        
        
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo "Deploying app from main branch"
                dir('target') {
                    sh '''
                        if pgrep -f "java -jar java-sample-21-1.0.0.jar" > /dev/null; then
                            pkill -f "java -jar java-sample-21-1.0.0.jar"
                            echo "App was running and has been killed."
                        else
                            echo "App is not running."
                        fi
                        JENKINS_NODE_COOKIE=dontKillMe nohup java -jar java-sample-21-1.0.0.jar > app.log 2>&1 &
                    '''
                }
            }
        }
    }
}
