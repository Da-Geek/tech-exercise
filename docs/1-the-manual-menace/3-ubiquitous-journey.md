## 🔥🦄 Ubiquitous Journey
blah blah what it is, why we use it
Extensible, traceable, auditable ...

```bash
# create a Group in GitLab for your team

# create a repo in GitLab in that group

# clone repo to your IDE
```

Take a walk in values-tooling.yaml file...
* Boostrap projects 
* Jenkins
Take a walk in values.yaml file... [pb enabled false]

```yaml
# update your values.yaml in the root file accordingly
source: "https://gitlab-ce.apps.cluster.example.com/<YOUR_TEAM_NAME>/tech-exercise.git"
team: <YOUR_TEAM_NAME>
```

update your `ubiquitous-journey/values-tooling.yaml` to change <YOUR_TEAM_NAME> in the bootstrap section
<pre class="language-yaml">
...
        - name: jenkins
          kind: ServiceAccount
          role: admin
          namespace: biscuits-ci-cd
      namespaces:
        - name: biscuits-ci-cd
          bindings: *binds
        - name: biscuits-dev
          bindings: *binds
        - name: biscuits-test
          bindings: *binds
        - name: biscuits-staging
          bindings: *binds
...
</pre>

```bash
# git add, commit, push your changes..
git add .
git commit -m  "🦆 ADD - correct project names 🦆" 
git push 
```

install all the tooling in UJ (only bootstrap, and Jenkins at this stage..)
```bash
helm upgrade install --namespace ${TEAM_NAME}-ci-cd .
```
show namespaces & Jenkins spinning up via ArgoCD 

show resources in the cluster
```bash
oc get projects | grep ${TEAM_NAME}
```
```bash
oc get pods -n ${TEAM_NAME}-ci-cd
```

TODO - fix bootstrap for dummy-sa (sort of did at the time being)