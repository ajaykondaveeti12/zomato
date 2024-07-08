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
	}
}