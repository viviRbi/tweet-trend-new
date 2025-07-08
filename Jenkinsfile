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
                echo "---------build started-----------"
                sh 'mvn clean deploy -DskipTests'
            }
        }

        // stage("test") {
        //     steps{
        //         echo "---------unit test started-----------"
        //         sh 'mvn surefire-report:report'
        //     }
        // }

        // stage('Sonar Queue analysis ') {
        //     environment {
        //         scannerHome = tool 'sonar-scanner'
        //     }
        //     steps {
        //         withSonarQubeEnv('sonarqube-server'){
        //             sh "${scannerHome}/bin/sonar-scanner"
        //         }
        //     }
        // }

        // stage("Quality Gate"){
        //     steps {
        //         script {
        //             timeout(time:1, unit: 'HOURS') {
        //                 def qg = waitForQualityGate()
        //                 if (qg.status != 'OK'){
        //                     error "Pipeline aborted due to quality gate failure: ${qg.status}"
        //                 }
        //             }
        //         }
        //     }
        // }

        stage("Jar Publish") {
            steps {
                script {
                        echo '<--------------- Jar Publish Started --------------->'
                        def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifact-cred"
                        def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                        def uploadSpec = """{
                            "files": [
                                {
                                "pattern": "jarstaging/(*)",
                                "target": "libs-release-local/{1}",
                                "flat": "false",
                                "props" : "${properties}",
                                "exclusions": [ "*.sha1", "*.md5"]
                                }
                            ]
                        }"""
                        def buildInfo = server.upload(uploadSpec)
                        buildInfo.env.collect()
                        server.publishBuildInfo(buildInfo)
                        echo '<--------------- Jar Publish Ended --------------->'  
                
                }
            }   
        }
    }
}
