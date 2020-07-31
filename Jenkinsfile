pipeline {
    agent any

    stages {

        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: "http://161.202.190.34:8082/artifactory/",
                    credentialsId: "jfrog-credentials"
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
            }
        }

        stage('SonarQube Code Analysis') {

          steps {
          script{

            // withSonarQubeEnv('sonarscanner') {
            // def mvnHome = tool 'maven'
            // sh "'${mvnHome}/bin/mvn' org.sonarsource.scanner.maven:sonar-maven-plugin:3.3.0.603:sonar -Dsonar.host.url=$SONAR_HOST_URL  -Dsonar.projectKey=lung-example -Dsonar.projectName=lung-example -Dsonar.language=java -Dsonar.sources=. -Dsonar.java.binaries=target -Dsonar.tests=. -Dsonar.test.inclusions=**/*Test*/* -Dsonar.exclusions=target/**/*.class"
            withMaven(maven : 'maven') {
                sh "org.sonarsource.scanner.maven:sonar-maven-plugin:3.3.0.603:sonar -Dsonar.host.url=$SONAR_HOST_URL  -Dsonar.projectKey=lung-example -Dsonar.projectName=lung-example -Dsonar.language=java -Dsonar.sources=. -Dsonar.java.binaries=target -Dsonar.tests=. -Dsonar.test.inclusions=**/*Test*/* -Dsonar.exclusions=target/**/*.class'
            }
         }
         }

          }
        }        
        
/*
        stage ('Compile Stage') {

            steps {
                withMaven(maven : 'maven') {
                    sh 'mvn clean compile'
                }
            }
        }
*/
        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: 'maven', // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }

        stage ('Testing Stage') {

            steps {
                withMaven(maven : 'maven') {
                    sh 'mvn test'
                }
            }
        }


        stage ('Deployment Stage') {
            steps {
                withMaven(maven : 'maven') {
                    sh 'mvn deploy'
                }
            }
        }
    }
}
