def path = "C:/ProgramData/jenkins/.jenkins/workspace/dotnetappscm/aspnet-core-dotnet-core/aspnet-core-dotnet-core.csproj" //stores source path into variable path.

pipeline 
{
    environment //specifies a sequence of key-value pairs which will be defined as environment variables for all steps, or stage-specific steps, depending on where the environment block is located within the Pipeline or within the stage.
    {
        appName = "fayesaWebapp"
        resourceGroup = "Training-rg"
        //path = "C:/ProgramData/jenkins/.jenkins/workspace/dotnetappscm/aspnet-core-dotnet-core/aspnet-core-dotnet-core.csproj"
    }
    agent any	//block is used to tell Jenkins where to execute this Job.	
	stages //block is used to group the set of tasks.	
	{
        stage('Analysis') //code analysis by sonarqube.
        {
            steps //for grouping steps.
            {
                script 
                {
                    
                    def scannerHome = tool 'SonarScanner for MSBuild' //define variable to store the installed tool.
                    withSonarQubeEnv('SonarQubeServer') //block that allows you to select the SonarQube server you want to interact with.
                    {
                        bat "\"${scannerHome}\\SonarScanner.MSBuild.exe\" begin /k:\"demo\"" 
			bat "dotnet build ${path}"    
                        bat "\"${scannerHome}\\SonarScanner.MSBuild.exe\" end"
                    }
                }
            }
        }
        stage("Quality gate") //check the quality gate status
        {
            steps {
                waitForQualityGate abortPipeline: false, credentialsId: 'squser'
            }
        }
        stage('build') // Builds the project and its dependencies into a set of binaries.dotnet build uses MSBuild to build the project, so it supports both parallel and incremental builds
        {
            steps
            {
                bat "dotnet build ${path}"
		    
            }
        }
        stage('test') //The dotnet test command is used to execute unit tests in a given solution. The dotnet test command builds the solution and runs a test host application for each test project in the solution. 
        {
            steps
            {
              bat "dotnet test ${path}"
		    
            }
        }
        stage('publish') //The dotnet publish command's output is ready for deployment to a hosting system.dotnet publish compiles the application, reads through its dependencies specified in the project file, and publishes the resulting set of files to a directory.
        {
            steps
            {
              bat "dotnet publish ${path}"
		    
            }
        }
        
        stage('Package') //package the published files.
        {
            steps 
                {
                echo "Deploying to stage environment for more tests!";
                bat "del *.zip"
                
		            bat "tar.exe -a -c -f WebApp_${BUILD_NUMBER}.zip aspnet-core-dotnet-core/bin/Debug/netcoreapp1.1/publish"
                }
        }
        
       
	stage('Deploy to azure') //Depoly the published files into the azure webapp.
	{
	   steps
		{
		   
			azureWebAppPublish appName: "${env.appName}", azureCredentialsId: 'Azure', resourceGroup: "${env.resourceGroup}" // azureWebAppPublish appName: 'fayesaWebapp', azureCredentialsId: 'Azure', resourceGroup: 'Training-rg'
	    }
	}
	}
}
