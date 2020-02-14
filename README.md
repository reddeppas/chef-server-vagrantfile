# chef-server-vagrantfile

  Run cmd vagrant up chef13 
  Run cmd vagrant up linuxnode1 
  
  bootstrap node to chefserver
  
  knife bootstrap \<ip-address\> --ssh-user \<vagrant\> --sudo --ssh-identity-file \<vagrant private key file location\> --node-name \<node-name\> --run-list \<runlist\> 
  
  for self signed certificates use --node-ssl-verify-mode none
