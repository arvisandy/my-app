node
{
   stage('SCM Checkout')
   {
     git 'https://github.com/arvisandy/my-app'
   }
   stage('Compile-Package')
   {
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
   stage('Build Docker Imager')
   {
   sh 'docker build -t aravind06a/myweb:0.0.2 .'
   }
   stage('Docker Image Push')
   {
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')])
   {
   sh "docker login -u dockerPass -p ${dockerPassword}"
   }
   sh 'docker push dockerPass/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u dockerPass -p dockerPassword 18.218.157.181:8083"
   sh "docker tag dockerPass/myweb:0.0.2 18.218.157.181:8083/arvi:1.0.0"
   sh 'docker push 18.218.157.181:8083/arvi:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest dockerPass/myweb:0.0.2' 
   }
}
}
