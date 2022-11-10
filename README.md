# Velero-backup S3 AWS

## Install binário client

```
https://github.com/vmware-tanzu/velero/releases/download/v1.8.1/velero-v1.8.1-linux-amd64.tar.gz tar -zxvf velero-v1.8.1-linux-amd64.tar.gz -C /usr/local/sbin --strip-components=1 velero-v1.8.1-linux-amd64/velero
```
```
chmod 755 /usr/local/sbin/velero
```

## Crie credentials file

velero-credentials.txt
```
[default]
aws_access_key_id = {accessKey}
aws_secret_access_key = {secretKey}
```

## Install via helm
```
helm repo add vmware-tanzu https://vmware-tanzu.github.io/helm-charts
```
```
helm install velero vmware-tanzu/velero \
--namespace velero --create-namespace \
--set-file 'credentials.secretContents.cloud=/PATHCredentials/velero-credentials.txt' \
--set 'configuration.provider=aws' \
--set 'configuration.backupStorageLocation.bucket=s3-name-bucket' \
--set 'configuration.backupStorageLocation.config.region=us-east-1' \
--set 'configuration.volumeSnapshotLocation.name=default' \
--set 'configuration.volumeSnapshotLocation.config.region=us-east-1' \
--set 'initContainers[0].name=velero-plugin-for-aws' \
--set 'initContainers[0].image=velero/velero-plugin-for-aws' \
--set 'initContainers[0].volumeMounts[0].mountPath=/target' \
--set 'initContainers[0].volumeMounts[0].name=plugins'
```
```
kubectl get deployment/velero -n velero
```
## Example deploy
```
kubectl create -f nginx.yaml
```
```
kubectl get deploy -n nginx-example
```
## Criando um backup e executando um restore 
```
velero backup create nginx-backup --include-namespaces nginx-example
```
```
velero get backups
```
```
kubectl delete namespaces nginx-example
```
```
velero restore create --from-backup nginx-backup
```
```
velero restore describe nginx-backup-20221110113704
```
```
kubectl get deploy -n nginx-example
```
## Backup Scheduling

```
velero schedule create myschedule --schedule="0 */6 * * *"
```
Criar Backup a cada seis horas

ou

Utilize o manifesto example-schedule.yaml

## Para deletar backups 
```
velero delete backup nginx-backup -n velero
```
