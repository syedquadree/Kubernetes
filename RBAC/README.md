#Generate a RSA Private key
openssl genrsa -out authentication/syed.key 2048
#generate the CSR for private key for Syed user(client)
 openssl req -new -key syed.key -out syed.csr -subj "/CN=syedq/O=Admin/O=Devops"
 root@kubeadmmaster:~/authentication# ls -l syed.csr
-rw-r--r-- 1 root root 932 Sep  9 04:01 syed.csr

#generate client cert for syed user signed by k8s CA
openssl x509 -req -in authentication/syed.csr \
  -CA /etc/kubernetes/pki/ca.crt \
  -CAkey /etc/kubernetes/pki/ca.key \
  -set_serial 101 \
  -extensions v3_req \
  -days 365 \
  -outform PEM \
  -out authentication/syed.crt
#update kubeconfig file with user credentials and context
kubectl config set-credentials syed --client-certificate=authentication/syed.crt --client-key=authentication/syed.key

