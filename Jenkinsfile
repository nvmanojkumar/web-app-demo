pipeline{
  agent any
  tools{
    maven 'Maven'
  }
  
  stages{
    stage ("Initialize"){
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            '''       
      }
    }
    
    stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
      }
    }
    
    stage ("Build"){
      steps {
      sh 'mvn clean package'
      }    
    }
    
     stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.223.255.228:/prod/apache-tomcat-8.5.68/webapps/webapp.war'
              }      
           }       
    }
    
    stage ('DAST') {
      steps {
        sshagent(['zap']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@18.191.232.136 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://18.223.255.228:8080/webapp/" || true'
        }
      }
    }    
       
    
  }
}
