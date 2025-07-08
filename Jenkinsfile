pipeline {
    agent {
        node {
            label 'maven-slave'
        }
    }
	environment {
		PATH = "/opt/apache-maven-3.9.10/bin:$PATH"
	}

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean deploy -DskipTests'
            }
        }

        stage('Sonar Queue analysis ') {
            environment {
                scannerHome = tool 'sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonarqube-server'){
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
    }
}
