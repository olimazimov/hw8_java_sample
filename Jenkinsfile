pipeline {

    agent any

    triggers {
        cron('0 5 * * *')

    }

    // decalre parameters
    parameters {
        string(name: 'TAG', defaultValue: "latest")
        booleanParam(name: 'SKIP_TEST', defaultValue: false)
        booleanParam(name: 'SKIP_PUBLISH_IMAGE', defaultValue: false)
    }

    // setup tools
    tools {
        // go 'Go'
        maven 'Maven_3.8.6'
        git 'Default'
    }


    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            when {
                expression {
                    return params.SKIP_TEST == false;
                }
            }
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Build image') {
            steps {
              sh ('docker build -t java-sample:${TAG} .')
            }
        }

        stage('Publish image') {
            when {
                expression {
                    return params.SKIP_PUBLISH_IMAGE == false;
                }
            }

            steps{
               withCredentials([usernamePassword(credentialsId: 'userTestID', passwordVariable: 'userpass', usernameVariable: 'userkey')]) {
                    //sh ('echo ${dockerHubUser}')
                    // sh ('echo ${dockerHubPassword}')
                    sh('docker login -u ${dockerHubUser} -p ${dockerHubPassword}')
                    sh('docker push olimazimov/hw8_java_sample:${TAG}')
                }
            }


        }

        // stage("Push tag to git") {
        //     when {
        //         expression {
        //             return params.SKIP_PUBLISH_IMAGE == false;
        //         }
        //     }

        //     steps {
        //         sh("git config user.name 'olimazimov'")
        //         sh("git config user.email 'olim.azimov@gmail.com'")

        //         withCredentials([gitUsernamePassword(credentialsId: 'github-token',gitToolName: 'git-tool')]) {
        //                 // remove old tag
        //                 sh('git push origin :refs/tags/${TAG}')
        //                 // update tag
        //                 sh('git tag -f ${TAG}')
        //                 // push
        //                 sh('git push origin --tags')
        //         }
        //     }

        // }
    }
}