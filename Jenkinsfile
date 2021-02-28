    pipeline {
        agent {
            docker {
                image "maven:3.6.3-jdk-8"
            }
        }
        triggers {
            pollSCM("* * * * *")
        }
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
                        releaseRepo: 'pragra-libs-release-local',
                        snapshotRepo: 'pragra-libs-snapshot-local'
                    ) 

                    rtMavenDeployer (
                        id: 'deployer1',
                        serverId: 'artificatory',
                        releaseRepo: 'pragra-libs-release-local',
                        snapshotRepo: 'pragra-libs-snapshot-local',
                        properties: ['key2=value2'] )
                 rtMavenRun (
                        pom: 'pom.xml',
                        goals: 'install',
                        // Maven options.
                        opts: '-Xms1024m -Xmx4096m',
                        resolverId: 'resolver1',
                        deployerId: 'deployer1',
                        buildName: "${JOB_BASE_NAME}",
                        buildNumber: "${BUILD_NUMBER}"
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