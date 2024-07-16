# namespacedelete
cat delete-namespace.sh 
```
#!/bin/bash

kubectl config get-contexts
read -p "Please give the context: " CONTEXT
echo "CONTEXT: $CONTEXT"

kubectl config use-context $CONTEXT
kubectl get ns
read -p "Please git namespace: " NAMESPACE

kubectl get namespace $NAMESPACE -o json >tmp.json

echo "Please edit tmp.json remove finalizer section in an other term or use CTRL z"
read -p "Waiting..."

kubectl proxy &
sleep 5

curl -k -H "Content-Type: application/json" -X PUT --data-binary @tmp.json http://127.0.0.1:8001/api/v1/namespaces/${NAMESPACE}/finalize

sleep 5
kill -9 $(ps -ef|grep 'kubectl proxy'|awk '{print $2}')
kubectl delete ns $NAMESPACE --force --grace-period=0

kubectl get ns

```
