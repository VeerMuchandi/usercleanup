# usercleanup
How to cleanup a user from OpenShift v3?

To delete all the projects owned by a user you can use the following. Substitute the <username> with your own username.

```
oc delete project $(oc get project --template='User       Project
{{range .items}}{{- index .metadata.annotations "openshift.io/requester" }}       {{.metadata.name}}
 {{end}}' | grep <username>| awk '{print $2}')
```
**Note** Use caution, as the projects may be shared with other users. If you loose data you cannot get it back

If you want to delete a bunch of users and all their data from OpenShift, you can do something like this. The script assumes that the authentication mechanism is htpasswd.

```
for i in {01..25}; do \
oc delete user user$i; \
oc delete identity htpasswd_auth:user$i; \
oc delete project $(oc get project --template='User       Project
{{range .items}}{{- index .metadata.annotations "openshift.io/requester" }}       {{.metadata.name}}
 {{end}}' | grep user$i| awk '{print $2}')
done
```
