# Hello Helm Chart

This is a testing repository to play around with k8s resources and helm.

## Installing dependencies
```sh
helm dependency build charts/hello-helm
```

## Deploying chart
```sh
helm install charts/hello-helm --generate-name  # with "random" name
# or
helm install <helm-name> charts/hello-helm      # with custom name
```

## Check deployment
```
helm ls
kubctl get pods
```

## Things to try
[x] Dependencies
[ ] PSC or PV
[ ] Deployment
[ ] Service
[ ] Uninstall without removing service
[ ] Scaler
[ ] Secrets and Environment Variables
[ ] Packaging

## Open questions
### What should be the git repository?
1. the folder created by `helm create <chart>`
2. the folder above the former folder
3. `helm create <chart>` should be done to a sub directory (usually `charts/`)
   similar to Ansible roles. The git repository would be the one _containing_
   the sub directory

I guess this depends on if you want a shareable chart like you would want to
share a Ansible role. If so, you might want to have the first option.

If you share the chart not by source code but by packaging (and pushing it into
a helm repo) it might be better to go with 2 or 3 for easier distinction between
chart relevant files and git repository specific things, e.g. README.

For this git repository I went with option 3.
