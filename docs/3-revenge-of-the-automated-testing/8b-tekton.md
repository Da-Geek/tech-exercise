### Extend Tekton Pipeline with Image Signing


1. Add a task into our codebase to sign our built images.

```bash
cd /projects/tech-exercise
cat <<'EOF' > tekton/templates/tasks/image-signing.yaml
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: image-signing
spec:
  workspaces:
    - name: output
  params:
    - name: APPLICATION_NAME
      description: Name of the application
      type: string
    - name: TEAM_NAME
      description: Name of the team that doing this exercise :)
      type: string
    - name: VERSION
      description: Version of the application
      type: string
    - name: COSIGN_VERSION
      type: string
      description: Version of cosign CLI
      default: 1.0.0
    - name: WORK_DIRECTORY
      description: Directory to start build in (handle multiple branches)
      type: string
  steps:
    - name: image-signing
      image: quay.io/openshift/origin-cli:4.8
      workingDir: $(workspaces.output.path)/$(params.WORK_DIRECTORY)
      script: |
        #!/usr/bin/env bash
        curl -skL -o /tmp/cosign https://github.com/sigstore/cosign/releases/download/v$(params.COSIGN_VERSION)/cosign-linux-amd64
        chmod -R 775 /tmp/cosign

        oc registry login
        /tmp/cosign sign -key k8s://$(params.TEAM_NAME)-ci-cd/$(params.TEAM_NAME)-cosign `oc registry info`/$(params.TEAM_NAME)-test/$(params.APPLICATION_NAME):$(params.VERSION)
EOF
```


2. Let's add this task into pipeline. Edit `tekton/pipelines/maven-pipeline.yaml` and copy below yaml where the placeholder is.

```yaml
    # COSIGN IMAGE SIGN 
    - name: image-signing
      runAfter:
      - verify-deployment
      taskRef:
        name: image-signing
      workspaces:
        - name: output
          workspace: shared-workspace
      params:
        - name: APPLICATION_NAME
          value: "$(params.APPLICATION_NAME)"
        - name: TEAM_NAME
          value: "$(params.TEAM_NAME)"
        - name: VERSION
          value: "$(tasks.maven.results.VERSION)"
        - name: WORK_DIRECTORY
          value: "$(params.APPLICATION_NAME)/$(params.GIT_BRANCH)"
```

3. It's not real unless it's in git, right?

```bash
# git add, commit, push your changes..
git add .
git commit -m  "👨‍🎤 ADD - image-signing-task 👨‍🎤"
git push
```

4. Store the public key in `pet-battle-api` repository for anyone who would like to verify our images. This push will also trigger the pipeline.

```bash
cp cosign.pub /projects/pet-battle-api/
cd /projects/pet-battle-api
git add cosign.pub
git commit -m  "🪑 ADD - cosign public key for image verification 🪑"
git push
```

🪄 Observe the **pet-battle-api** pipeline running with the **image-sign** task.

After the task succesfully finish, go to OpenShift UI > Builds > ImageStreams and select `pet-battle-api`. You'll see a tag ending with `.sig` which shows you that this is image signed. 
![cosign-image-signing](images/cosign-image-signing.png)

5. Let's verify the signed image with the public key:

```bash
cd /projects/pet-battle-api
oc registry login
cosign verify -key cosign.pub default-route-openshift-image-registry.<CLUSTER_DOMAIN>/<TEAM_NAME>-cd-cd/pet-battle-api
```

The output should be like:

```bash
Verification for default-route-openshift-image-registry.<CLUSTER_DOMAIN>/<TEAM_NAME>-ci-cd/pet-battle-api --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - The signatures were verified against the specified public key
  - Any certificates were verified against the Fulcio roots.
{"critical":{"identity":{"docker-reference":"default-route-openshift-image-registry.<CLUSTER_DOMAIN>/<TEAM_NAME>-ci-cd/pet-battle-api"},"image":{"docker-manifest-digest":"sha256:ec332c568ef608b6f1d2d179d9ac154523fbe412b4f893d76d49d267a7973fea"},"type":"cosign container image signature"},"optional":null}
```
