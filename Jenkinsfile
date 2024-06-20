def registry = 'https://spy003.jfrog.io'
def imageName = 'spy003.jfrog.io/spy-docker-local/mymissions'

pipeline {
    agent {
        node {
            label 'build-node'
        }
    }
environment {
    
    
    // // ARTIFACTORY_REPO = 'my-repo'
    // ARTIFACTORY_CRED = 'artifact-cred'
      scannerHome = tool 'sonar-scanner'
      GIT_REPO_NAME = "FS-Java"
      GIT_USER_NAME = "isekai-003"
      version = "2.1.2"
    //   MY_URL_ID = "my-url"

}
tools {
    jdk 'java'
    maven 'maven12'
    jfrog 'jfrog-cli'
}
    stages {
        stage('COMPILE'){
            steps{
                sh 'mvn compile'
            }
        }
        stage("test"){
            steps{
                echo "----------- unit test started ----------"
                sh 'mvn surefire-report:report'
                 echo "----------- unit test Complted ----------"
            }
        }
        
    stage('SonarQube analysis') {
          steps{
                 withSonarQubeEnv('sonar-server') {
                 sh "${scannerHome}/bin/sonar-scanner"
    }
    }
  }
     stage("Quality Gate"){
          steps {
               script {
                    timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
                    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                    if (qg.status != 'OK') {
                    error "Pipeline aborted due to quality gate failure: ${qg.status}"
                            }
                    }
                }
            }
    }
    stage(' Vulnerability-Scan') {
        steps {
            parallel(
                "Dependency-Check": {
                   dependencyCheck additionalArguments: '--scan ./   ', odcInstallation: 'DP'
                   dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                },
                "TrivyFS-Scan": {
                  sh  " trivy fs --format table -o trivy-fs-report.html . "
                }
            )
        }
    }
  stage("build"){
            steps {
                jf 'mvn-config --repo-resolve-releases=spy-libs-release  --repo-resolve-snapshots=spy-libs-snapshot --repo-deploy-releases=spy-libs-release-local --repo-deploy-snapshots=spy-libs-snapshot-local'

                 echo "----------- build started ----------"

                 sh "mvn clean package -DskipTests=true"
                
                 echo "----------- build complted ----------"
            }
        }
         stage('push artifact') {
            steps {

              jf 'rt u /var/lib/workspace/spring-boot/target/spyMission-2.1.2.jar spy-libs-release-local/myMissions/spyMission-2.1.2.jar'


            }
        }
    
    stage(" Docker Build ") {
      steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }
     stage ("Docker Image Scan") {
        steps{
            sh " trivy image --format table -o trivy-image-report.html shamshuddin03/spymission:2.1.2 "
        }
     }

            stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'artifact-cred'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }
     stage('Update Deployment File') {
           
        
        steps {
            withCredentials([string(credentialsId: 'github-cred', variable: 'GITHUB_TOKEN')]) {
             checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'git', url: 'https://github.com/isekai-003/FS-Java.git']])

                sh '''
                    git config user.email "shamshuddin0003@gmail.com"
                    git config user.name "isekai-003"
                    sed -i "s/replaceImageTag/${version}/g" k8smanifests/ds.yml
                    git add .
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
            }
        }
    }
    
 
    }
post {
always {
script {
def jobName = env.JOB_NAME
def buildNumber = env.BUILD_NUMBER
def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'
def body = """
<html>
<body>
<div style="border: 4px solid ${bannerColor}; padding: 10px;">
<h2>${jobName} - Build ${buildNumber}</h2>
<div style="background-color: ${bannerColor}; padding: 10px;">
<h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
</div>
<p>Check the <a href="${BUILD_URL}">console output</a>.</p>
</div>
</body>
</html>
"""
emailext (
subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
body: body,
to: 'shamshuddin0003@gmail.com',
from: 'jenkins@example.com',
replyTo: 'jenkins@example.com',
mimeType: 'text/html',
attachmentsPattern: 'trivy-image-report.html'
)
}
}
}
}