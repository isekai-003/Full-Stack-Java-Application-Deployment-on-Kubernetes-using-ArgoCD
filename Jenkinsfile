def registry = 'https://spy003.jfrog.io'
def imageName = 'spy003.jfrog.io/spy-docker-local/mymissions'
def version   = '2.1.2'
pipeline {
    agent any
    // agent {
    //     node {
    //         label 'maven'
    //     }
    // }
environment {
    // PATH = "/var/lib/jenkins/workspace/spring-boot/target"
    // API_KEY = credentials('NVD_cred')
    // ARTIFACTORY_REPO = 'my-repo'
    ARTIFACTORY_CRED = 'artifact-cred'

}
tools {
    // jdk 'java'
    maven 'maven12'
    // jfrog 'jfrog-cli'

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
        
//     stage('SonarQube analysis') {
//     environment {
//       scannerHome = tool 'sonar-scanner'
//     }
//     steps{
//     withSonarQubeEnv('sonar-server') {
//       sh "${scannerHome}/bin/sonar-scanner"
//     }
//     }
//   }
//   stage("Quality Gate"){
//     steps {
//         script {
//         timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
//     def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
//     if (qg.status != 'OK') {
//       error "Pipeline aborted due to quality gate failure: ${qg.status}"
//     }
//   }
// }
//     }
//   }
//   stage(' OWASP-Dependency-Check') {
//       steps {
//                    dependencyCheck additionalArguments: '--scan ./   ', odcInstallation: 'DP'
//                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
//       }
// //     }
  stage("build"){
            steps {
                // jf 'mvn-config --repo-resolve-releases=spy-libs-release  --repo-resolve-snapshots=spy-libs-snapshot --repo-deploy-releases=spy-libs-release-local --repo-deploy-snapshots=spy-libs-snapshot-local'

                 echo "----------- build started ----------"
                 sh "mvn clean package -DskipTests=true"
                
                 echo "----------- build complted ----------"
            }
        }
        //  stage('push artifact') {
        //     steps {

        //       jf 'rt u /var/lib/jenkins/workspace/spring-boot/target/spyMission-1.0.0.jar spy-libs-release-local/myMissions/spyMission-1.0.0.jar'


        //     }
        // }
       
    //      stage("Jar Publish") {
    //     steps {
    //         script {
    //                 echo '<--------------- Jar Publish Started --------------->'
    //                  def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"ARTIFACTORY_CRED"
    //                  def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
    //                  def uploadSpec = """{
    //                       "files": [
    //                         {
    //                           "pattern": "target/(*)",
    //                           "target": "${ARTIFACTORY_REPO}/",
    //                           "props" : "${properties}"
                              
    //                         }
    //                      ]
    //                  }"""
    //                  def buildInfo = server.upload(uploadSpec)
    //                  buildInfo.env.collect()
    //                  server.publishBuildInfo(buildInfo)
    //                  echo '<--------------- Jar Publish Ended --------------->'  
            
    //         }
    //     }   
    // }


    stage(" Docker Build ") {
      steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }

            stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, "ARTIFACTORY_CRED"){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }

// stage(" Deploy ") {
//        steps {
//          script {
//             echo '<--------------- Helm Deploy Started --------------->'
//             sh 'helm install ttrend ttrend-1.0.1.tgz'
//             echo '<--------------- Helm deploy Ends --------------->'
//          }
//        }
//      }  
}
}