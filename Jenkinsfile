pipeline {
  agent any
  tools { 
        maven 'Maven_3_8_4'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=prasantthh -Dsonar.organization=prasantthh -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=8631f57fe0b4ee750a09473ce1a0d0a3099131c1'
			}
    }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://539402213917.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
   

	stage ('kubernetes deployment of asg bugg webapp application') {
		steps {
			withKubeConfig([credentialsId: 'kubelogin']) {
				sh('kubectl delete all --all -n devsecops')
				sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
			}
		}
	}
}

}
	   
	
