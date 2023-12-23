 k create deploy mydeployment --image=nginxdemos/hello --replicas=3
 k expose deploy mydeployment --type=NodePort --port=80 --target-port=80 --dry-run=client -o yaml 
 k create svc nodeport mydeployment --tcp=80:80 --node-port=30009 --dry-run=client -o yaml
 k create cm webConfigmap --from-literal=username='myuser' --from-literal=password='pswd' -o yaml --dry-run=client
 k create secret generic websecret --from-literal=username='myuser' --from-literal=password='pswd' -o yaml --dry-run=client
 k get secrets websecret -o json |jq -r .data.password |base64 --decode && echo

 k set image deploy/mydeployment hello=nginxdemos/hello
 k rollout history  deployment mydeployment