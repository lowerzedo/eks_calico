# 1. Create cluster without nodes

eksctl create cluster \
--name astro-calico-cluster \
--version 1.31 \
--without-nodegroup \
--vpc-nat-mode Disable \
--region ap-southeast-1

# 2. Delete aws-node DaemonSet (Disables AWS VPC CNI Plugin)

kubectl delete daemonset -n kube-system aws-node

<!-- aws ec2 modify-instance-attribute --instance-id <instance-id> --no-source-dest-check -->

# 3. Install Calico operator

kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.2/manifests/tigera-operator.yaml

# 4. Apply Calico config for Calico CNI mode

kubectl create -f calico-cni.yaml

# 5. Add node group with higher pod density

eksctl create nodegroup --cluster astro-calico-cluster --node-type t2.micro --max-pods-per-node 100
