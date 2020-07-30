pipeline {
    agent any
    tools {
        maven "m3.6"
        jdk "jdk8"
    }
    triggers {
            pollSCM("* * * * *")
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh "mvn clean compile"
            }
        }
        stage('Test') {
            steps {
                echo 'Testing'
                sh "mvn test"
            }
        }
        stage('Test Report') {
          steps {
              script {
                junit '**/surefire-reports/*.xml'
              }
          }
        }
        stage('Sonar Analysis') {
            steps {
                echo 'Sonar Scanner'
               	withSonarQubeEnv('sonar67') {
			    	sh "mvn sonar:sonar"
			    }
            }
        }
        stage('Package') {
            steps {
                echo 'Packaging'
                sh "mvn package -DskipTests"
            }
        }
        stage('Deploy') {
            steps {
                echo '## TODO DEPLOYMENT ##'
                echo 'Add a deployment enviroment'
            }
        }
        stage('Archive Artifact') {
            steps {

                rtMavenDeployer (
                    id: "mvn_deployer1",
                    serverId: "ART",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )


                rtMavenResolver (
                    id: "mvn_resolver1",
                    serverId: "ART",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-release-local"
                )

                rtMavenRun (
                    tool: 'm3',
                    pom: 'pom.xml',
                    goals: 'install',
                    resolverId: 'mvn_resolver1',
                    deployerId: 'mvn_deployer1'
                )
                // script {
                //     archive '**/*.jar'
                // }


            }
        }
        stage('Publish Build Info') {
            steps {
                 rtPublishBuildInfo (
                 serverId: "ART"
                )
            }
        }
        stage('Promote Build') {
            steps {
            rtPromote (
            // Mandatory parameter
 
            buildName: '$JOB_NAME',
            buildNumber: '$BUILD_ID',
                // Artifactory server ID from Jenkins configuration, or from configuration in the pipeline script
            serverId: 'ART',
            // Name of target repository in Artifactory
            targetRepo: 'libs-release-local',
 
            // Optional parameters
 
            // Comment and Status to be displayed in the Build History tab in Artifactory
            comment: 'this is the promotion comment',
            status: 'Released',
            // Specifies the source repository for build artifacts.
            sourceRepo: 'libs-snapshot-local',
            // Indicates whether to promote the build dependencies, in addition to the artifacts. False by default.
            includeDependencies: true,
            // Indicates whether to fail the promotion process in case of failing to move or copy one of the files. False by default
            failFast: true,
            // Indicates whether to copy the files. Move is the default.
            copy: true
            )
          }
        }
        stage('Branch Stage') {
          steps {
            echo 'branch'
          }
        }

    }

    post {
        always {
            echo 'JENKINS PIPELINE'
        }
        success {
            echo 'JENKINS PIPELINE SUCCESSFUL'
        }
        failure {
            echo 'JENKINS PIPELINE FAILED'
        }
        unstable {
            echo 'JENKINS PIPELINE WAS MARKED AS UNSTABLE'
        }
        changed {
            echo 'JENKINS PIPELINE STATUS HAS CHANGED SINCE LAST EXECUTION'
        }
    }
}
