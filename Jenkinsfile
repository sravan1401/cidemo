    pipeline {
        tools {
            maven 'm3'
            jdk 'jdk8'
        }
        agent any
    
        stages {
            stage('Declarative CheckOut') {
                steps {
                    sh "echo Checking Out code"
                    checkout scm
                }
            }
            stage('Compile') {
                steps {
                    sh "mvn clean compile"
                }
            }
            stage('Unit Test') {
                steps {
                    sh "mvn test"
                }
            }
            stage('Publish Test Result') {
                steps {
                    junit '**/surefire-reports/*.xml'
                }
            }
            stage('Code Quality Check') {
                steps {
                    withSonarQubeEnv('sonarqube'){
                        sh 'mvn verify sonar:sonar -Dsonar.projectName=PragraDemo -Dsonar.projectKey=PRAGRA1 -Dsonar.projectVersion=$BUILD_NUMBER'
                       // some block
                    }
                }
            }
            stage('Package Application') {
                steps {
                    sh "mvn package -DskipTests"
                }
            }
            stage('Generic Artificatory') {
                steps {
                rtUpload (
                        serverId: 'art1',
                        spec: '''{
                            "files": [
                                {
                                "pattern": "target/ci-pipeline-pragra-0.0.1.jar",
                                "target": "pragra-ci-demo/${BUILD_NUMBER}"
                                },
                                {
                                "pattern": "pom.xml",
                                "target": "pragra-ci-demo/${BUILD_NUMBER}"
                                }
                            ]
                        }''',
                    
                        // Optional - Associate the uploaded files with the following custom build name and build number,
                        // as build artifacts.
                        // If not set, the files will be associated with the default build name and build number (i.e the
                        // the Jenkins job name and number).
                        buildName: "${JOB_NAME}",
                        buildNumber: "${BUILD_NUMBER}"
                 )
                }
            }
            stage('Promote Build') {
                steps {
                    withCredentials([usernameColonPassword(credentialsId: 'artificatory', variable: 'logindata')]) {
                     sh 'curl -u${logindata}' -X PUT "http://192.168.50.101:8081/artifactory/api/storage/pragra-ci-demo/${BUILD_NUMBER}/ci-pipeline-pragra-0.0.1.jar?properties=Promoted=Yes"'
                }
            }
            // stage('Publish to Artifactory') {
            //     steps {
            //         rtMavenResolver (
            //             id: 'resolver1',
            //             serverId: 'art1',
            //             releaseRepo: 'libs-release-local',
            //             snapshotRepo: 'libs-snapshot-local'
            //         ) 

            //         rtMavenDeployer (
            //             id: 'deployer1',
            //             serverId: 'art1',
            //             releaseRepo: 'libs-release-local',
            //             snapshotRepo: 'libs-snapshot-local'
            //         )
            //      rtMavenRun (
            //             tool:'m3',
            //             pom: 'pom.xml',
            //             goals: 'install',
            //             resolverId: 'resolver1',
            //             deployerId: 'deployer1'
            //         )
            //     }
            // }

        }

        post {
            always {
                echo "Looks good"
            }
        }
    }