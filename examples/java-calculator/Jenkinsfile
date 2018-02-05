pipeline {
    agent any
    parameters {

        choice(choices: 'no\nyes', description: 'Enable o-profile', name: 'enable_oprofile')
        string(defaultValue: "", description: 'Analyzed PID, set enable o-profile to yes', name: 'PID')
        choice(choices: 'no\nyes', description: 'Skip install and configuration- run test only', name: 'run_test_only')
        string(defaultValue: "test.feature", description: 'Feature file', name: 'feature_file')
        string(defaultValue: "@test1", description: 'cucumber test tag', name: 'tag')
        string(defaultValue: "msuarez@redhat.com", description: 'email for notifications', name: 'notification_email')

    }

    environment {
        node= '1.1.1.1'
        oprofile_report_name= 'report.txt'

    }

    stages {
    /*
        stage('Clone') {
            when {
                environment name: 'run_test_only', value: 'no'
            }
            steps{
            git credentialsId: '25ed919a-c9e6-473f-afaf-d6147d1a5c32', url: 'https://github.com/cucumber/cucumber-java-skeleton.git'
            }
        }
        */

        stage('Test'){
            when {
                environment name: 'run_test_only', value: 'no'
            }
            steps{
                sh 'mvn test'
            }
        }

        stage ('ITCM Installation') {
            when {
                environment name: 'run_test_only', value: 'no'
            }
            steps {
            echo "${node}"

            }
        }


        stage ('Run Performance test') {
        steps {
        parallel(
        "Start oprofile":
          {
            script{

                  if (env.enable_oprofile =='yes')
                  {
                 }
                 else
                 echo "oprofile was not enabled"

            }

        },

        "Execute test":{
                echo "Run performance test. Feature file: ${feature_file} Tag: ${tag} "
                //sh "cucumber ${feature_file}' -t '${tag}' -b -x -v --format html --out reports.html --format pretty"
             }

        )

        }
        }
        stage ('Collect Oprofile results'){

          when {
            environment name: 'enable_oprofile', value: 'yes'
              }

          steps{
          echo "${node}"

               }
        }


    }

    post {
        success {
        //node('node1'){

            echo "Test succeeded"
            script {

                mail(bcc: '',
                     body: "Run ${JOB_NAME}-#${BUILD_NUMBER} succeeded. To get more details, visit the build results page: ${BUILD_URL}.",
                     cc: '',
                     from: 'jenkins-admin@gmail.com',
                     replyTo: '',
                     subject: "${JOB_NAME} ${BUILD_NUMBER} succeeded",
                     to: env.notification_email)
                if (env.enable_oprofile =='yes')
                     {
                        //archiveArtifacts artifacts: "${analyzed_component}-${oprofile_report_name}"
                      }

            //publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: '/home/reports', reportFiles: 'reports.html', reportName: 'Performance Test Report', reportTitles: ''])
            }
        //}
        }
        failure {
            echo "Test failed"
            mail(bcc: '',
                body: "Run ${JOB_NAME}-#${BUILD_NUMBER} succeeded. To get more details, visit the build results page: ${BUILD_URL}.",
                 cc: '',
                 from: 'jenkins-admin@gmail.com',
                 replyTo: '',
                 subject: "${JOB_NAME} ${BUILD_NUMBER} failed",
                 to: env.notification_email)
            //publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: true, reportDir: '/home/tester/reports', reportFiles: 'reports.html', reportName: 'Performance Test Report', reportTitles: ''])
        }
    }

}
