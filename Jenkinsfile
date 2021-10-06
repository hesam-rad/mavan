pipeline {
    agent any
    stages {
        stage("Req-http") {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'tm4j', passwordVariable: 'PWD', usernameVariable: 'USER')]) {
                        sh """
                            curl -u '$USER:$PWD' -X GET http://jira.gamelectronics.com/rest/atm/1.0/automation/testcases?tql=testCase.projectKey=%27RC%27 --output /tmp/test/test-hesam.zip
                            curl -u '$USER:$PWD' -X GET http://jira.gamelectronics.com/rest/atm/1.0/testrun/RC-C2 --output /tmp/test/test-hesam.txt
                        """
                        sh """
                            cd /tmp/test/zip
                            rm -rf  ./*
                            cd /tmp/test
                            unzip "test-hesam.zip" -d "/tmp/test/zip/"
                            exit 0
                        """
                    }
                }
            }
        }
    }
    post {
        always {
            publishTestResults serverAddress: 'http://jira.gamelectronics.com',
                    projectKey: 'RC',
                    filePath:'target/test-report/*.json',
                    format: 'Cucumber',
                    autoCreateTestCases: false
        }
  	    failure {
  	        echo "Test failed"
                    cucumber buildStatus: 'FAIL',
                                   failedFeaturesNumber: 1,
                                   failedScenariosNumber: 1,
                                   skippedStepsNumber: 1,
                                   failedStepsNumber: 1,
                                   fileIncludePattern: '**/*.json',
                                   sortingMethod: 'ALPHABETICAL'
            slackSend color: 'red', message: "${params.reportname} Tests failed. >> Click to view <$reportUrl|report>"
  	    }
  	    success {
            echo "Test succeeded"
                    cucumber buildStatus: 'SUCCESS',
                                            failedFeaturesNumber: 0,
                                            failedScenariosNumber: 0,
                                            skippedStepsNumber: 0,
                                            failedStepsNumber: 0,
                                            fileIncludePattern: '**/*.json',
                                            sortingMethod: 'ALPHABETICAL'
            slackSend color: 'green', message: "${params.reportname} Tests passed. >> Click to view <$reportUrl|report>"
        }
    }
}
