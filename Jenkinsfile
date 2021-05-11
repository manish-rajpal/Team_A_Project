pipeline {
    agent any
    stages {

                 stage('APIServer'){
                            steps{
                                sh 'cd spring-petclinic-rest &&  nohup mvn spring-boot:run &'
				                sleep(20)
                            }
                }
                 stage('angular'){
                              steps{
                                    sh 'cd spring-petclinic-angular/static-content && curl https://jcenter.bintray.com/com/athaydes/rawhttp/rawhttp-cli/1.0/rawhttp-cli-1.0-all.jar -o rawhttp.jar &&  nohup java -jar ./rawhttp.jar serve . -p 4200 &'
				                    sleep(3)
                              }
                }
                stage('Newman by Postman') {
                              steps {
                                  	script {
                                            try {
                                           		sh 'newman run PetClinic_02_collection.json --environment PetClinic_02_environment.json --reporters junit'
                                           		}
                                       		catch (Exception e) {
                                                echo "Tests are failing, continue pipeline..."
                                                }
                                            }
                                    }
                                    post {
                                          	always {
                                           		junit '**/*.xml'
                                           	        }
                                         }
                                        }
		        stage('Robot Framework') {
                              steps {
                                    sleep(30)
                                    sh 'robot --variable BROWSER:headlesschrome -d RobotFrameWork/Results RobotFrameWork/Tests'
                              }
                              post {
                                    always {
                                           script {
                                                  step(
                                                       [
                                                             $class              : 'RobotPublisher',
                                                              outputPath          : 'RobotFrameWork/Results',
                                                              outputFileName      : '**/output.xml',
                                                              reportFileName      : '**/report.html',
                                                              logFileName         : '**/log.html',
                                                              disableArchiveOutput: false,
                                                              passThreshold       : 50,
                                                              unstableThreshold   : 40,
                                                              otherFiles          : "**/*.png,**/*.jpg",
                                                       ]
                                                  )
                                           }
                                    }
                              }
                }		

    }
        post {
            always {
                echo 'Hello again! I  am here just to check if you get an email... disregard any contents'
                emailext (
                subject: "new code test",
                body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                to: 'rajpalmanish@gmail.com, jayashree.bondre@iths.se, shubhangi.patil@iths.se, feng.zhu@iths.se, victor.hedstrom@iths.se, maria.shishkina@iths.se, elias.arezomande@iths.se',
                attachmentsPattern: 'RobotFrameWork/Results/report.html, RobotFrameWork/Results/log.html'
                        )
                    }
               }
}
