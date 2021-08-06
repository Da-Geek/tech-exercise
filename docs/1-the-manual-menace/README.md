# Exercise 1 - The Manual Menace
> A GitOps approach to perform and automate deployments.
## 👨‍🍳 Exercise Intro
[TODO] In this exercise, we will use GitOps to set up our working environment. We will set up Git projects, create `dev`, `test` and `stage` namespaces, and deploy tools like Jenkins and Nexus. In order to do that, we'll utilize a very popular approach: _GitOps_ 

## 🔮 Learning Outcomes
* Understand the benefits gained from GitOps approach
* Deploy helm charts manually
* Drive tools installations through GitOps
## 🔨 Tools used in this exercise
* [Helm](https://helm.sh/) - Helps us to define, install, and upgrade Kubernetes application.
* [ArgoCD](https://argoproj.github.io/argo-cd/) - A controller which continuously monitors application and compare the current state against the desired.
* [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) - Helps us to save our credentials safely in public repositories.


## 🦆 Conventions
When running through the exercise, we're tried to call out where things need replacing. The key ones are anything inside an `<>` should be replaced. For example, if your team is called `biscuits` then in the instructions if you see `<TEAM_NAME>` this should be replaced with `biscuits` like so:
```yaml
name: <TEAM_NAME>

# this becomes
name: biscuits
```

## 🖼️ Big Picture
[TODO]