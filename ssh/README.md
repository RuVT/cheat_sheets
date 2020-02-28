# SSH 
ssh or Secure Shell the every day tool for any person that want to control computers remotely.

## Creating your ssh keys
```bash
ssh-keygen -t rsa -C "key for github" -f ~/.ssh/my_github_key
# -C is a comment to add to the key so you can differentiate them 
# -f is the path to the output keys, once created you will have a <key_name> and <key_name>.pub files, first one if the private.
```

## SSH to a machine
```
# normal connection
ssh -i ~/.ssh/my_private_key my_user@ip_or_dns_host 

# connection through a proxy
ssh -i ~/.ssh/my_private_key_B my_user_B@ip_or_dns_host_B -o ProxyCommand="ssh -i ~/.ssh/my_private_key_A my_user_A@ip_or_dns_host_A -W %h:%p" 

```
