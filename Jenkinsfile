pipeline {
    agent any

    stages {
        stage('Sonar Analysis') {
            steps {
                echo 'Testing..'
                sh 'cd webapp && sudo docker run  --rm -e SONAR_HOST_URL="http://13.53.76.240:9000" -e SONAR_LOGIN="sqp_8ac5574d91d726c1218cbd684df95164453a60c7"  -v ".:/usr/src" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms'

            }
        }

        stage('Build LMS') {
            steps {
                echo 'Building..'
                sh 'cd webapp && npm install && npm run build'
            }
        }

        stage('Release LMS') {
            steps {
                script {
                    echo "Releasing.."       
                    def packageJSON = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJSON.version
                    echo "${packageJSONVersion}"  
                    sh "zip webapp/dist-${packageJSONVersion}.zip -r webapp/dist"
                    sh "curl -v -u admin:Hari@79934 --upload-file webapp/dist-${packageJSONVersion}.zip http://13.53.76.240:8081/repository/lms/"     
            }
            }
        }

        stage('Deploy LMS') {
            steps {
                script {
                    echo "Deploying.."       
                    def packageJSON = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJSON.version
                    echo "${packageJSONVersion}"  
                    sh "curl -u admin:Hari@79934 -X GET \'http://13.53.76.240:8081/repository/lms/dist-${packageJSONVersion}.zip\' --output dist-'${packageJSONVersion}'.zip"
                    sh 'sudo rm -rf /var/www/html/*'
                    sh "sudo unzip -o dist-'${packageJSONVersion}'.zip"
                    sh "sudo cp -r webapp/dist/* /var/www/html"
            }
            }
        }
    }
}




