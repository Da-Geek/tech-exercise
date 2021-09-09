### Extend Jenkins Pipeline with Sonar Scanning

1. Create a sonar file in the root of the project. This file contains the information of Sonarqube instance.

```bash
cd /projects/pet-battle
cat << EOF > sonar-project.js
const scanner = require('sonarqube-scanner');

scanner(
  {
    serverUrl: 'http://sonarqube-sonarqube:9000',
    options: {
      'sonar.login': process.env.SONARQUBE_CREDS_USR,
      'sonar.password': process.env.SONARQUBE_CREDS_PSW,
      'sonar.projectName': 'Pet Battle',
      'sonar.projectDescription': 'Pet Battle UI',
      'sonar.sources': 'src',
      'sonar.tests': 'src',
      'sonar.inclusions': '**', // Entry point of your code
      'sonar.test.inclusions': 'src/**/*.spec.js,src/**/*.spec.ts,src/**/*.spec.jsx,src/**/*.test.js,src/**/*.test.jsx',
      'sonar.exclusions': '**/node_modules/**',
      //'sonar.test.exclusions': 'src/app/core/*.spec.ts',
      'sonar.javascript.lcov.reportPaths': 'reports/lcov.info',
      'sonar.testExecutionReportPaths': 'coverage/test-reporter.xml'
    }
  },
  () => process.exit()
);
EOF
```

2. Then we need to introduce SonarQube credentials to Jenkinsfile. Add the followings to the beginning of the file in `environment` block.

```groovy
		SONARQUBE_CREDS = credentials("${OPENSHIFT_BUILD_NAMESPACE}-sonarqube-auth")
```
You'll have:

<pre>
		<span style="color:green;" >// Credentials bound in OpenShift</span>
		GIT_CREDS = credentials(<span style="color:orange;" >"</span>${OPENSHIFT_BUILD_NAMESPACE}<span style="color:orange;" >-git-auth"</span>)
		NEXUS_CREDS = credentials(<span style="color:orange;" >"</span>${OPENSHIFT_BUILD_NAMESPACE}<span style="color:orange;" >-nexus-password"</span>)
		SONAR_CREDS = credentials(<span style="color:orange;" >"</span>${OPENSHIFT_BUILD_NAMESPACE}<span style="color:orange;" >-sonar-auth"</span>)
</pre>

And add a shell step in to `build` stage of the pipeline where <span style="color:green;" >// SONARQUBE SCANNING</span> placeholder is. This needs to be happen before the build.

```bash
        // 🌞 SONARQUBE SCANNING EXERCISE GOES HERE 
        echo '### Running SonarQube ###'
        sh 'npm run sonar'
```

3. Push the changes to the git repository, which also will trigger a new build.

```bash
cd /projects/pet-battle
git add Jenkinsfile sonar-project.js
git commit -m "🧦 test code-analysis step 🧦"
git push
```

4. Observe the pipeline and when scanning is completed, browse the Sonarqube UI to see the details.

** Update the screenshots **
![images/sonar-pb-api.png](images/sonar-pb-api.png)