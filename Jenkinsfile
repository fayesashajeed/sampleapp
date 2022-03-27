def path = "C:/ProgramData/jenkins/.jenkins/workspace/dotnetappscm/aspnet-core-dotnet-core/aspnet-core-dotnet-core.csproj"

pipeline 
{
    environment
    {
        appName = "fayesaWebapp"
        resourceGroup = "Training-rg"
        //path = "C:/ProgramData/jenkins/.jenkins/workspace/dotnetappscm/aspnet-core-dotnet-core/aspnet-core-dotnet-core.csproj"
    }
    agent any	
	stages 
	{
        stage('Analysis') 
        {
            steps
            {
                script
                {
                    
                    def scannerHome = tool 'SonarScanner for MSBuild'
                    withSonarQubeEnv('SonarQubeServer') 
                    {
                        bat "\"${scannerHome}\\SonarScanner.MSBuild.exe\" begin /k:\"demo\""
                        
			                  bat "dotnet build ${path}"    
                        bat "\"${scannerHome}\\SonarScanner.MSBuild.exe\" end"
                    }
                }
            }
        }
        stage("Quality gate") 
        {
            steps {
                waitForQualityGate abortPipeline: false, credentialsId: 'squser'
            }
        }
        stage('build')
        {
            steps
            {
                bat "dotnet build ${path}"
		    
            }
        }
        stage('test')
        {
            steps
            {
              bat "dotnet test ${path}"
		    
            }
        }
        stage('publish')
        {
            steps
            {
              bat "dotnet publish ${path}"
		    
            }
        }
        
        stage('Package') 
        {
            steps 
                {
                echo "Deploying to stage environment for more tests!";
                bat "del *.zip"
                
		            bat "tar.exe -a -c -f WebApp_${BUILD_NUMBER}.zip aspnet-core-dotnet-core/bin/Debug/netcoreapp1.1/publish"
                }
        }
        
       
	stage('Deploy to azure') 
	{
	   steps
		{
		   
			azureWebAppPublish appName: "${env.appName}", azureCredentialsId: 'Azure', resourceGroup: "${env.resourceGroup}"
	    }
	}
	}
}
