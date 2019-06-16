pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm 
            }
        }
        stage('Build') {
            steps {
                echo 'Clean Build'
                sh "${tool 'm3'}/bin/mvn clean compile"
            }
        }
        stage('Test') {
            steps {
                echo 'Testing'
                sh "${tool 'm3'}/bin/mvn test"
            }
        }
        stage('Sonar Analysis') {
            steps {
                echo 'Sonar Scanner'
               	//def scannerHome = tool 'SonarQube Scanner 3.0'
			    withSonarQubeEnv('sonar67') {
			    	sh "${tool 'm3'}/bin/mvn sonar:sonar"
			    }
            }
        }
        stage('Package') {
            steps {
                echo 'Packaging'
                sh "${tool 'm3'}/bin/mvn -DskipTests"
            }
        }
        stage('Deploy') {
            steps {
                echo '## TODO DEPLOYMENT ##'
            }
        }
        stage('Archive Artifact') {
            steps{
              archiveArtifacts artifacts: 'build/libs/**/*.jar',
              fingerprint: true
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