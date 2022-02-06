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

## Upgrading chart
Since we have mysql as dependency, we need to make sure to keep the password on
upgrades.

The method I use exposed the password to all users on that host. **Do not use
this method on a host you do not trust!** I have not looked into a method that
does not expose the password to the process tree.

Assuming your chat name is `hello-helm-1644148306`:
```sh
CHART_NAME=hello-helm-1644148306-mysql
MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace "default" ${CHART_NAME}-mysql -o jsonpath="{.data.mysql-root-password}" | base64 --decode)
helm upgrade $CHART_NAME ./charts/hello-helm --set mysql.auth.rootPassword=$MYSQL_ROOT_PASSWORD     # bad! read above
```

## Overriding values
To override values from the default `values.yaml`:
```sh
helm install -f my-values.yaml charts/hello-helm --generate-name
# same for upgrades
helm upgrade -f my-values.yaml $CHART_NAME ./charts/hello-helm --set mysql.auth.rootPassword=$MYSQL_ROOT_PASSWORD   # bad! read above
```

## Keeping Service after uninstall
Keeping a service (or other resource) works via
`metadata.annotations."helm.sh/resource-policy": "keep"`.

I could hard code the annotation but wanted to make it optional and default to
`delete`. This did not work and needs further investigation. The service will
not get deleted on `helm unistall` even with explicit `delete` as
`helm.sh/resource-policy`.

## Configure PersistentVolume for nginx Pod
Option to create PV via PVC and mount PV at `/mnt/data/` in nginx Container:
`persistentVolume: True`

## Things to try
[x] Dependencies
[x] PSC or PV
[ ] Deployment
[ ] Service
[x] Uninstall without removing service
[x] Scaler
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
