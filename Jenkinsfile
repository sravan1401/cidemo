    pipeline {
        tools {
            maven 'maven3'
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
                    withSonarQubeEnv('sonar'){
                        sh "mvn sonar:sonar"
                        // some block
                    }
                }
            }
            stage('Package Application') {
                steps {
                    sh "mvn package -DskipTests"
                }
            }
            stage('Publish to Artifactory') {
                steps {
                    rtMavenResolver (
                        id: 'resolver1',
                        serverId: 'artificatory',
                        releaseRepo: 'libs-release-local',
                        snapshotRepo: 'libs-snapshot-local'
                    ) 

                    rtMavenDeployer (
                        id: 'deployer1',
                        serverId: 'artificatory',
                        releaseRepo: 'libs-release-local',
                        snapshotRepo: 'libs-snapshot-local',
                 rtMavenRun (
                        tool:'maven3',
                        pom: 'pom.xml',
                        goals: 'install',
                        resolverId: 'resolver1',
                        deployerId: 'deployer1'
                    )
                }
            }

        }

        post {
            always {
                echo "Looks good"
            }
        }
    }