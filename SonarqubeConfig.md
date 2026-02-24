# SonarQube Configuration in Azure DevOps

This guide shows how to connect SonarQube to Azure DevOps pipelines and configure projects for .NET, Maven, Gradle, and Node.js.

---

## 1. Prerequisites

- SonarQube server accessible via URL (`http://sonar.company.com`)  
- Admin credentials or token in SonarQube  
- Azure DevOps project and repository  
- SonarScanner installed (CLI or MSBuild for .NET)  
- Secure pipeline variable for SonarQube token (e.g., `SonarQubeToken`)  

---

## 2. Create a SonarQube Service Connection in Azure DevOps

1. Go to **Project Settings → Service Connections → New Service Connection → SonarQube**  
2. Fill in:

- **Server URL:** `http://<sonarqube-server>:9000` or domain like `https://sonar.company.com`  
- **Token:** Generate in SonarQube under **My Account → Security → Generate Tokens**  
- **Name:** e.g., `SonarQubeConnection`  

3. Verify and save the connection.

---

## 3. .NET / C# Projects

### Azure DevOps Pipeline YAML

```yaml
trigger:
- main

pool:
  vmImage: 'windows-latest'

variables:
  buildConfiguration: 'Release'

steps:
- task: SonarQubePrepare@5
  inputs:
    SonarQube: 'SonarQubeConnection'
    scannerMode: 'MSBuild'
    projectKey: 'MyDotNetApp'
    projectName: 'MyDotNetApp'

- task: VSBuild@1
  inputs:
    solution: '**/*.sln'
    msbuildArgs: '/p:Configuration=$(buildConfiguration)'

- task: SonarQubeAnalyze@5

- task: SonarQubePublish@5
  inputs:
    pollingTimeoutSec: '300'

Notes:

projectKey is a unique identifier (not a token).

Authentication comes from the Azure DevOps service connection.

4. Maven Java Projects
pom.xml Snippet
<properties>
    <sonar.projectKey>MyMavenApp</sonar.projectKey>
    <sonar.host.url>http://sonar.company.com</sonar.host.url>
</properties>

<build>
    <plugins>
        <plugin>
            <groupId>org.sonarsource.scanner.maven</groupId>
            <artifactId>sonar-maven-plugin</artifactId>
            <version>4.4.1.4047</version>
        </plugin>
    </plugins>
</build>
Azure DevOps Pipeline YAML
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

variables:
  SONAR_TOKEN: $(SonarQubeToken)

steps:
- task: SonarQubePrepare@5
  inputs:
    SonarQube: 'SonarQubeConnection'
    scannerMode: 'Other'
    extraProperties: |
      sonar.projectKey=MyMavenApp
      sonar.host.url=http://sonar.company.com
      sonar.login=$(SONAR_TOKEN)

- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
    goals: 'clean install sonar:sonar'

- task: SonarQubePublish@5
  inputs:
    pollingTimeoutSec: '300'

Notes:

Token is passed because Maven plugin runs outside Azure DevOps tasks.

5. Gradle Projects
build.gradle Snippet
plugins {
    id "org.sonarqube" version "4.0.0.2929"
}

sonarqube {
    properties {
        property "sonar.projectKey", "MyGradleApp"
        property "sonar.host.url", "http://sonar.company.com"
        property "sonar.login", "${System.getenv('SONAR_TOKEN')}"
    }
}
Azure DevOps Pipeline YAML
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

variables:
  SONAR_TOKEN: $(SonarQubeToken)

steps:
- task: SonarQubePrepare@5
  inputs:
    SonarQube: 'SonarQubeConnection'
    scannerMode: 'Other'
    extraProperties: |
      sonar.projectKey=MyGradleApp
      sonar.host.url=http://sonar.company.com
      sonar.login=$(SONAR_TOKEN)

- task: Gradle@2
  inputs:
    gradleWrapperFile: 'gradlew'
    tasks: 'clean build sonarqube'

- task: SonarQubePublish@5
  inputs:
    pollingTimeoutSec: '300'
6. Node.js / JavaScript / TypeScript Projects
sonar-project.properties
sonar.projectKey=MyNodeApp
sonar.projectName=MyNodeApp
sonar.projectVersion=1.0
sonar.sources=src
sonar.tests=test
sonar.javascript.lcov.reportPaths=coverage/lcov.info
sonar.host.url=http://sonar.company.com
sonar.login=${SONAR_TOKEN}
Azure DevOps Pipeline YAML
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

variables:
  SONAR_TOKEN: $(SonarQubeToken)

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '18.x'
  displayName: 'Install Node.js'

- script: |
    npm install
    npm run build
    npm test -- --coverage
  displayName: 'Build and Test'

- task: SonarQubePrepare@5
  inputs:
    SonarQube: 'SonarQubeConnection'
    scannerMode: 'CLI'
    configMode: 'manual'
    cliProjectKey: 'MyNodeApp'
    cliProjectName: 'MyNodeApp'
    cliSources: 'src'

- script: |
    sonar-scanner \
      -Dsonar.projectKey=MyNodeApp \
      -Dsonar.sources=src \
      -Dsonar.host.url=http://sonar.company.com \
      -Dsonar.login=$(SONAR_TOKEN)
  displayName: 'Run SonarScanner'

- task: SonarQubePublish@5
  inputs:
    pollingTimeoutSec: '300'

Notes:

Token is required because Node.js scanner runs outside Azure DevOps tasks.

Include LCOV coverage reports for full analysis.

7. Key Points

Project Key → unique identifier for all projects, never a token.

.NET/MSBuild → uses Azure DevOps service connection for authentication automatically.

Maven / Gradle / Node.js → scanner runs outside the tasks, token must be provided.

Quality Gates and coverage reports should be configured per project.

Store tokens as secure pipeline variables to avoid exposing credentials.
