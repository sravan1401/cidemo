pipeline {
    agent any
    triggers {
            pollSCM(*/10 * * * *)
        }
    tools {
        mvn "m3"
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
            }
        }
        stage('Archive Artifact') {
            steps {
                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "ART",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
                
                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ART",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )
                rtMavenRun (
                    tool: 'm3',
                    pom: 'pom.xml',
                    goals: 'clean install',
                    opts: '-Xms1024m -Xmx4096m',
                    resolverId: 'MAVEN_RESOLVER'
                    deployerId: 'MAVEN_DEPLOYER',
                )

                
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