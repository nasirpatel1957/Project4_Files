@Library('shared_library') _

pipeline{
    agent any 

    parameters{
        choice(name: 'Action', choices: 'create\ndelete', description: 'Choose option for Pipeline')
        string(name: "ECR_Repo_Name", description: "Dockerhub UserName", defaultValue: "nhp1993")
        string(name: "Region_Name", description: "Project Name", defaultValue: "us-east-1")
        string(name: "Account_id", description: "Version Name", defaultValue: "606312200267")
    }

    stages {
        stage("1. Git Checkout") {
        when { expression { params.action == 'create' } }
            steps{
            gitCheckout(
                branch: "main",
                url: "https://github.com/nasirpatel1957/Project4_Files.git"
            )
            }
        }

        stage("2. Maven Unit Test") {
        when { expression { params.action == 'create' } }
            steps{
                script{
                    mvnTest()
            }
        }
    }

        stage("3. Maven Integration Test") {
        when { expression { params.action == 'create' } }
            steps{
                script{
                    mvnIntegration()
            }
        }
    }

        stage('4. Static code analysis: Sonarqube'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   def SonarQubecredentialsId = 'sonar-api'
                   staticCodeAnalysis(SonarQubecredentialsId)
               }
            }
        }

        stage('5. Quality Gate Status Check : Sonarqube'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   def SonarQubecredentialsId = 'sonar-api'
                   QualityGateStatus(SonarQubecredentialsId)
               }
            }
        }

        stage('6. Maven Build'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   mvnBuild()
               }
            }
        }    

        stage('7. Docker Image Build - ECR'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   DockerBuild("${params.ECR_Repo_Name}", "${params.Account_id}", "${params.Region_Name}")
               }
            }
        } 

        stage('8. Docker Image Scan: Trivy'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   DockerScanTrivy("${params.HubUser}", "${params.Project}", "${params.Version}")
               }
            }
        } 

        stage('9. Docker Image Push'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   DockerPush("${params.HubUser}", "${params.Project}", "${params.Version}")
               }
            }
        } 

        stage('10. Docker Image Cleanup'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   DockerClean("${params.HubUser}", "${params.Project}", "${params.Version}")
               }
            }
        } 
    
    
    }
}