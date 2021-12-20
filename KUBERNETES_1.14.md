# Renew expired certificates in Kubernetes 1.14

1. Switch to root:
```
sudo su -
```
2. Create backup location:
```
mkdir -p ~/cert_backup
```
3. Move old certificates to backup location:
```
cd /etc/kubernetes/pki/
mv {apiserver.crt,apiserver-etcd-client.key,apiserver-kubelet-client.crt,front-proxy-ca.crt,front-proxy-client.crt,front-proxy-client.key,front-proxy-ca.key,apiserver-kubelet-client.key,apiserver.key,apiserver-etcd-client.crt} ~/cert_backup
cd /etc/kubernetes/
mv {admin.conf,controller-manager.conf,kubelet.conf,scheduler.conf} ~/cert_backup
```
4. Re-create certificates
```
kubeadm init phase certs all --apiserver-advertise-address <ip_address_of_cluster>
kubeadm init phase kubeconfig all
```
5. Reboot machine:
```
reboot
```
6. Copy admin.config file into HOME/.kube:
```
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```
7. Check pods' status:
```
kubectl get pods -A
```
8. Confirm certificates' expiration date
```
find /etc/kubernetes/pki/ -type f -name "*.crt" -print|egrep -v 'ca.crt$'|xargs -L 1 -t -i bash -c 'openssl x509 -noout -text -in {}|grep After'
```
