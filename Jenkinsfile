pipeline{
	agent any 
	tools{
		jdk 'jdk17'
		nodejs 'node16'
	}
	environment{
		SCANNER_HOME=tool 'sonar-scanner'
	}
	stages{
		stage('clean workspace'){
			steps{
				cleanWs()
			}
		}
		stage('Code Checkout From Git'){
			steps{
				git branch: 'main', url: 'https://github.com/ajaykondaveeti12/zomato.git'
			}
		}
		stage("SonarQube Code Analysis"){
			steps{
				withSonarQubeEnv('sonar-scanner'){
					sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=zomato-project \
                    -Dsonar.projectKey=zomato-project '''
				}
			}
		}
		stage("Code Quality Gates"){
			steps{
				script{
					 timeout(time: 2, unit: 'MINUTES'){
					waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
				}
			}
		}
	}
	stage("Install Dependencies"){
			steps{
				sh "npm install"
			}
		}
		stage("OWASP FS SCAN"){
			steps{
				dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'dp-check'
				dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
			}
		}
		stage("TRIVY FS SCAN"){
			steps{
				sh "trivy fs . > trivy.txt"
			}
		}
		stage("Docker Image Build and Push"){
			steps{
				script{
				withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
					sh "docker build -t cloudzomato . "
					sh "docker tag cloudzomato ajaykondaveeti/cloudzomato:latest"
					sh "docker push ajaykondaveeti/cloudzomato:latest"
						}
					}
				}
			}
		stage("TRIVY is Image Scanning"){
			steps{
				sh "trivy image ajaykondaveeti/cloudzomato:latest >trivy.txt"
			}
		}
		stage("Creating Docker Container "){
			steps{
				sh 'docker run -d --name zomato-app -h zomato -p 3000:3000 ajaykondaveeti/cloudzomato:latest'
			}
		}
	}
}