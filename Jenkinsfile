node{
   stage('SCM Checkout'){
     git 'https://github.com/Siddarthan77/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	 }
    }
    stage('Build Docker Imager'){
      sh 'docker build -t siddarthandocker/projectv1.0 .'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f ApacheProjectServer'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
      sh 'docker run -d -p 8090:8080 --name ApacheProjectServer siddarthandocker/projectv1.0' 
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
      sh "docker login -u siddarthandocker -p ${dockerPassword}"
    }
      sh 'docker push siddarthandocker/projectv1.0'
   }
   stage('Nexus Artificatory Image Push'){
   sh "docker login -u admin -p admin123 52.66.126.172:8083"
   sh "docker tag siddarthandocker/projectv1.0 52.66.126.172:8083/privateimage:5.0.0"
   sh 'docker push 52.66.126.172:8083/privateimage:5.0.0'
   }
   }
}
