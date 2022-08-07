# Authentic IdP
[https://goauthentik.io/](https://goauthentik.io/)


# Sealed Secrets / Secrets
Modify [helm/templates/sealed-secret.yaml](helm/templates/sealed-secret.yaml).  I used kubeseal to encrypt my secrets since this is a public repository.  You can edit the above file and just replace with a normal secret.

# Install
`skaffold deploy`

# First time setup
[https://authentik.home.lab/if/flow/initial-setup/](https://authentik.home.lab/if/flow/initial-setup/)

# If you encounter issues after deploy
If you notice celery errors / failures in the worker node, Authentik giving errors about outposts not working, then run the below command.  This is a known issue on first deploys: https://github.com/goauthentik/authentik/issues/3243

`kubectl exec -it deployment/authentik-worker -c authentik -- ak bootstrap_tasks`


# Setup
Be sure create a custom claim for 'username' and include it as a scope when creating authentic providers.  This will allow you to return the users username if need.
  
Admin Interface -> Customisation -> Property Mappings -> Create  

Expression:
```
return {
  "username": request.user.username
}
```

# Uninstall
`skaffold delete`  

Be sure to DELETE the PVC and PV!  If not, when you reinstall, it will use the old database with the old password.  You will see logs in the worker pod saying "WRONG PASSWORD".  In such a case 
uninstall again, delete the PVC and PV and finally deploy again.