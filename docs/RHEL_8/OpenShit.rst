------------------
OpenShift Tutorial
------------------

Follow the tutorial [Container Development Kit]_


.. code::

    PS C:\Users\reist\Downloads> .\cdk-3.15.0-1-minishift-windows-amd64.exe setup-cdk
    Setting up CDK 3 on host using 'C:\Users\reist\.minishift' as Minishift's home directory
    Copying minishift-rhel7.iso to 'C:\Users\reist\.minishift\cache\iso\minishift-rhel7.iso'
    Copying oc.exe to 'C:\Users\reist\.minishift\cache\oc\v3.11.346\windows\oc.exe'
    Creating configuration file 'C:\Users\reist\.minishift\config\config.json'
    Creating marker file 'C:\Users\reist\.minishift\cdk'
    Default add-ons anyuid, admin-user, xpaas, registry-route, che, htpasswd-identity-provider, eap-cd installed
    Default add-ons anyuid, admin-user, xpaas enabled
    CDK 3 setup complete.
    PS C:\Users\reist\Downloads>



 .. code::
    ./cdk-3.15.0-1-minishift-windows-amd64.exe config set vm-driver virtualbox
    $env:MINISHIFT_USERNAME='reistlein@yahoo.fr'

Source the environment

.. code::
    
    & minishift oc-env | Invoke-Expression

    
.. code::
    minishift.exe start

.. [Container Development Kit]    https://developers.redhat.com/products/cdk/hello-world?success=true&tcWhenSigned=January+1%2C+1970&tcWhenEnds=January+1%2C+1970&tcEndsIn=0&tcDuration=365&tcDownloadFileName=cdk-3.15.0-1-minishift-windows-amd64.exe&tcRedirect=5000&tcSrcLink=https%3A%2F%2Fdevelopers.redhat.com%2Fcontent-gateway%2Fcontent%2Forigin%2Ffiles%2Fsha256%2F47%2F471b4deebcb540dfa429a4d41e2fbcf06759620041feb689a084b3bcbf0b65e4%2Fcdk-3.15.0-1-minishift-windows-amd64.exe&p=Product%3A+Container+Development+Kit+%28CDK%29+3.x&pv=3.15.0&tcDownloadURL=https%3A%2F%2Faccess.cdn.redhat.com%2Fcontent%2Forigin%2Ffiles%2Fsha256%2F47%2F471b4deebcb540dfa429a4d41e2fbcf06759620041feb689a084b3bcbf0b65e4%2Fcdk-3.15.0-1-minishift-windows-amd64.exe%3F_auth_%3D1615045994_4cdb7304257d960dbe3a5a1636ca826c#fndtn-windows


https://192.168.99.100:8443/console/project/myproject/browse/routes


Login on OpenShift


.. code::

oc login https://192.168.99.100:8443 --token=Md8ekXry0dMOWSf0VaK3uXEoiFlxNYNwNItGTMhkR9s


PS C:\Users\reist> oc explain pod
KIND:     Pod
VERSION:  v1

DESCRIPTION:
     Pod is a collection of containers that can run on a host. This resource is
     created by clients and scheduled onto hosts.

PS C:\Users\reist> oc explain pod.spec

oc get pods --watch
oc create -f .\pods\lab-pod.yaml
oc rsh  lab-pod
oc port-forward pod/lab-pod 8080


* Checklist
    * Output from oc get pods contains the pod you uploaded in step 5
    * Output from oc describe pod/lab-pod has the correct name and MESSAGE environment value
    * curl localhost:8080 (> Invoke-RestMethod -Uri "http://localhost:8080" -Method Get)  prints the message you entered in step 3

oc delete pod lab-pod