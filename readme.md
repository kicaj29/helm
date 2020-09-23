- [why helm (v3)](#why-helm--v3-)
- [chart](#chart)
- [persistency](#persistency)
- [three-way merge patches update](#three-way-merge-patches-update)
- [namespaces](#namespaces)
- [helm installation on win10](#helm-installation-on-win10)
  * [install helm chart](#install-helm-chart)
  * [uninstall helm chart](#uninstall-helm-chart)
  * [helm variables](#helm-variables)
- [building helm charts](#building-helm-charts)
- [helm release and release revision.](#helm-release-and-release-revision)
- [basic commands](#basic-commands)
- [install, upgrade, rollback](#install--upgrade--rollback)
  * [chart1](#chart1)
  * [chart2](#chart2)
  * [rollback to revision 1 of the release](#rollback-to-revision-1-of-the-release)
    + [history](#history)
  * [chart 3](#chart-3)
  * [chart 4 - INGRESS controller](#chart-4---ingress-controller)
- [Customizing charts with helm templates](#customizing-charts-with-helm-templates)
  * [Testing custom templates](#testing-custom-templates)
    + [Static](#static)
    + [Dynamic](#dynamic)
  * [Data sources for values](#data-sources-for-values)
  * [Functions and pipelines](#functions-and-pipelines)
  * [Modifying scope using with](#modifying-scope-using-with)
  * [Controlling carriage returns, whitespaces, indent.](#controlling-carriage-returns--whitespaces--indent)
  * [Logical operators and functions](#logical-operators-and-functions)
  * [Flow control](#flow-control)
  * [Variables](#variables)
  * [Helper functions (sub-templates)](#helper-functions--sub-templates-)
    + [Abstract charts - library](#abstract-charts---library)
  * [chart 5 - custom values](#chart-5---custom-values)
  * [chart 6 - logic with building connection string to mongodb](#chart-6---logic-with-building-connection-string-to-mongodb)
  * [chart 7 - installing DEV and TEST environment](#chart-7---installing-dev-and-test-environment)
- [Managing dependencies](#managing-dependencies)
  * [Packaging charts](#packaging-charts)
  * [Publishing charts in helm repository](#publishing-charts-in-helm-repository)
    + [Example how to package charts](#example-how-to-package-charts)
    + [Example how to publish charts](#example-how-to-publish-charts)
  * [Configuring helm client to work with repositories](#configuring-helm-client-to-work-with-repositories)
- [Using existing helm chart](#using-existing-helm-chart)
- [links](#links)

# why helm (v3)

**Packaging**

> With kubectl you do not install application as atomic set of kubernetes objects. Rather you deploy each object (yaml) separately. Also these objects might have dependencies between each other and order of installation might be important. Helm allows to group all kuberentes objects (yaml files) in one package and install the whole package. kubectl does not support rollbacks but helm supports it. Additionally to this helm supports out of the box replacing placeholders like: build number, env. prefix.

**Versioning**

> kubectl does not support versioning of kubernets objects

# chart
With helm you install your application as an entity defined by your **chart** and not a set of independent kubernetes objects. Chart is definition of your application.

# persistency

Helm stores release manifests (application versions) inside Kubernetes as secrets.

# three-way merge patches update

What if I modify Kubernetes objects with a tools other then Helm like kubectl. Helm tree compares the three manifests: old chart, new chart, live state. Based on this it creates a patch that merges the updates in best possible way. For example if something has been changed via kubectl and now we want apply a new chart then the change that was done via kubectl will stay as long as there is no conflict with new chart. The same is relvant also for rollbacks!

![3-way-merge](images/3-way-merge.png)

# namespaces

Helm supports namespaces like kubectl.

# helm installation on win10

![local-k8s-and-helm](images/local-k8s-and-helm.png)
>NOTE: in next steps I used K8s cluster installed together with docker. I did not used VirtualBox.

Run PowerShell as admin and execute:
```
choco install kubernetes-helm
```

![install-helm-win10](images/install-helm-win10.png)

To check version execute:
```
helm version --short
```

>NOTE: helm connection string is configured in the same file that is used by *kubectl* command: %USERPROFILE%\.kube\config

By default helm tree is not configured to use any repository. If you want to install existing packages you have to add keys to repositories containing some charts.
To install official helm repository execute the following command:

```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```
![install-official-repo](images/install-official-repo.png)

## install helm chart

Next we can install sample chart available in the official repository.

```
helm install demo-mysql stable/mysql
```
![heml-install-mysql](images/heml-install-mysql.png)
![my-sql-status](images/my-sql-status.png)

To get password execute command and next use some online page for base64 decode:
![my-sql-get-password](images/my-sql-get-password.png)
The password is **ot1TBGzWp7**.

Next install mysql client https://dev.mysql.com/downloads/workbench/ and connect to installed by helm mysql.

![connect-to-mysql](images/connect-to-mysql.png)
>NOTE: it was mandatory to run port forwarding to be able connect to the mysql!

## uninstall helm chart

```
helm uninstall demo-mysql
```

![uninstall-chart](images/uninstall-chart.png)

## helm variables

```
helm env
```

![helm-env](images/helm-env.png)

# building helm charts

https://helm.sh/docs/topics/charts/

![chart-structure](images/chart-structure.png)


# helm release and release revision.
Helm supports release concept including release the same chart but with different K8s object names, for example: test, pre-prod, prod.   

In case we did a change only in single yaml file that we can update only this file and not do the new whole release - it is called **release revision**.

# basic commands
| Action| Command|
|----------|----------|
| Install a release | helm install [release] [chart] |
| Upgrade to a release revision| helm upgrade [release] [chart] |
| Rollback to a release revision | helm rollback [release] [revision] |
| Print release history | helm history [release] |
| Display release status | helm status [release] |
| Show details of a release | helm get all [release] |
| Uninstall a release | helm uninstall [release] |
| List releases | helm list |

# install, upgrade, rollback

## chart1

[chart1](charts/chart1/chart/guestbook)

>NOTE: template properties do not support dash -

We refer to local chart and not chart from official helm repostory.

```
helm install demoguestbook guestbook
helm uninstall demoguestbook
```
![install-char1](images/install-char1.png)

Open in web browser http://localhost:30007/ to see the app.

![chart1-app](images/chart1-app.png)

Next we can use the following command to see release details:

```
helm get manifest demoguestbook
```

## chart2

[chart2](charts/chart2/chart/guestbook)

Here we deploy the same chart but with different application. The change was only in the application and not in the chart.
*appVersion* and *description* from *Chart.yaml* are set to 1.1 but *version* of the chart stays the same 0.1.0.   
In *frontend.yaml* we refer to new image *phico/frontend:1.1*.

```
helm upgrade demoguestbook guestbook
```

![chart2-app](images/chart2-app.png)

## rollback to revision 1 of the release

```
helm rollback demoguestbook 1
```

![rollback.png](images/rollback.png)

### history

```
helm history demoguestbook
```

![helm-history](images/helm-history.png)

## chart 3

Chart 3 contains classic example with front-end, back-end and database.

[chart3](charts/chart3/chart/guestbook)

chart 3 adds back-end and database.

*version* field in main *Chart.yaml* is updated to 1.1.0 because there are changes in infrastructure.

```
helm upgrade demoguestbook guestbook
```
![chart3-app](images/chart3-app.png)

Next we can check status and history:

![chart3-history](images/chart3-history.png)

and we can open the new app: http://localhost:31705

![app-20](images/app-20.png)

## chart 4 - INGRESS controller

>NOTE: before we start we have to uninstall previous release because upgrade is not possible.

![upgrade-not-possible](images/upgrade-not-possible.png)

[chart4](charts/chart4/chart/guestbook)

First we have to install ingress controller
https://kubernetes.github.io/ingress-nginx/deploy/#using-helm
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install release-ingress ingress-nginx/ingress-nginx
```

After this we can see that nginx server is running:

![ingress-nginx-empty](images/ingress-nginx-empty.png)

Next we can release a package that uses ingress controller.

```
helm install demoguestbook guestbook
```

After this we have to add entries to *C:\Windows\System32\drivers\etc\hosts*
to have mapping between DNS names and localhost address.

```txt
127.0.0.1 frontend.local
127.0.0.1 backend.local
```

This names are also used in [ingress.yaml](charts/chart4/chart/guestbook/charts/frontend/templates/ingress.yaml).

Finally we can open the UI in web browser:

![app-frontend.local](images/app-frontend.local.png)


# Customizing charts with helm templates

One of the reason to use customizing charts is it have possibility to generate K8s object names based on a release name. For example: dev-fronted, test-frontend, preprod-frontend, prod-frontend.   
There is alternative option to install different releases in different namespaces or different clusters but well designed helm chart should support such function to be able install it always without any name conflicts.

Helm template engine uses **Go Template** engine https://golang.org/pkg/text/template/ .

![helm-template-engine](images/helm-template-engine.png)

Use the following command to read generated manifest file:
```
helm get manifest [release name]
```

## Testing custom templates

### Static
```
helm template [chart]
```
- works without K8s cluster
- static release name

### Dynamic
```
helm install [release] [chart] --debug -dry-run
```
- real helm install but without commit
- can generate a release-name

## Data sources for values

- values.yaml
- other-file.yaml: ```helm install -f file```
- variables: ```helm install --set foo=bar```
- hierarchically organized
- chart data: ```name: {{.Chart.Name}})``` (first letter of property is upper case)
- release data: ```name: {{.Release.Name}}```, ```name: {{.Release.IsInstall}}``` etc.
- K8s data: ```annotations: K8s{{.Capabilities.KubeVersion}}```
- file data: ```annotations: data:{{.Files.Get conf.ini}}```
- template data: ```annotations: tpl:{{.Template.Name}}```

## Functions and pipelines

Some of basics:

| Function | Pipeline |
|----------|----------|
| default default_value value | value \| default default_value |
| quote value | value \| quote |
| upper value | value \| upper |
| trunc value 63| value \| trunc 63 |
| trimSuffix "-" value | value \| trimSuffix "-" |
| b64enc value | value \| b64enc |
| randAlphaNum 10 | value \| randAlphaNum 10 |
| toYaml value | value \| toYaml |
| printf format value ... | list value ... \| join "-" |

## Modifying scope using with

```helm
spec:
    {{with .Values.service}}
    type: {{.type}}
    ports:
        - port: {{.port}}
          targetPort: 80
    {{end}}
```

>NOTE: directive **with** causes adding not needed carriage returns.

## Controlling carriage returns, whitespaces, indent.

- use dash to remove not needed carriage returns.
```helm
spec:
    {{with .Values.service-}}
    type: {{.type}}
    ports:
        - port: {{.port}}
          targetPort: 80
    {{-end}}
```

- all indentations from the template are preserved. If needed it can be modified by the indentation function.

```helm
spec:
    type: ClusterIP
    ports:
        - port: 80
          targetPort: 80
    {{indent 6 .Values.tcp}}
```

- formatted output (printf)

```
apiVersion: v1
kind: Service
metadata:
    labels:
        app.kubernetes.io/name:{{- printf "%s-%s" .Release.Name .Values.service.name -}}
```

## Logical operators and functions

| Operator | Function | Example |
|----------|----------|---------|
| Equal to | eq | eq .Val1 .Val2        |
| Not equal to | ne | ne .Val1 .Val2        |
| Greater than | gt | gt .Val1 .Val2        |
| Lower than | lt | lt .Val1 .Val2        |
| Or | or | or .Val1 .Val2        |
| And | and | and .Val1 .Val2        |
| Not | not | not .Val1        |

## Flow control

* conditions
```
{{- if .Values.ingress.enabled}}
apiVersion: extensions/v1beta1
kind: ingress
metadata:
    labels:
        app.kuberenetes.io/name
{{- if .Values.service.name -}}
{{ .Values.service.name | trimSuffix "-"}}
{{- else -}}
{{ .Chart.Name }}
{{- end -}}
{{- end -}}
```
* loops

Scope in the loop is defined by type from the array. All fields are accessed as relative to the current scope.

```
spec:
{{- range .Values.ingress.hosts}}
    -host: {{ .hostname | quote }}
    http:
        paths:
        {{- range .paths}}
            - path: {{.path}}
              backend:
                serviceName: {{.service}}
                servicePort: http
        {{- end}}
{{- end}}
```
## Variables

Variables are needed when we want access some field that is not available in the current scope.

$ is called global variable. It refers to the build-in variable that allows to access root data.

```
spec:
{{- range $currentHost := .Values.ingress.hosts}}
    - host: {{.hostname | quote}}
        http:
            paths:
            {{- range .paths}}
            - path: {{.path}}-{{$currentHost.suffix}}
              backend:
                serviceName: {{$.Release.Name}}{{.service}}
                servicePort: http
            {{- end}}
{{- end}}
```

## Helper functions (sub-templates)

Names of sub-templates are global so it is good practice to add prefix with the name of the chart.

**_helpers.tpl**

```
{{- define "mychart.fullname" -}}
{{- if .Values.fullnameOverride -}}
{{- .Values.fullnameOverride | trunc 63 | trimSuffix "-" -}}
{{- else -}}
{{- printf "%s-%s" .Release.Name $name | trunc 63 | trimSuffix "-" -}}
{{- end -}}
{{- end -}}
```

Next we can call this function with its name and scope (dot for example if we want pass current scope).

```
metadata:
    labels:
        app.kubernetes.io/name: {{include "mychart.fullname" .}}
```

Helper files are stored in **templates** folder. This files are not used to generate manifest because files prefixed by '_' are not rendered as K8s objects.

>NOTE: if you want have some files that should be completely ignored by helm create file **.helmignore** place it in the root folder and add the files names to this file.

### Abstract charts - library

Abstract charts do not generate any manifest. They are only used by other charts.

Chart.yaml
```
apiVersion: v2
name: myLibrary
appVersion: "1.0"
description: A Helm library chart for Guestbook App
version: 1.2.1
type: library
```

## chart 5 - custom values

[chart5](charts/chart5-customizing-charts/chart/guestbook)

This chart show simple usage of custom values. It does not fully work because backend chart uses not working connection string to mongodb.
The connection string is not correct because not mongodb has dynamic name.

![not-working-backend](images/not-working-backend.png)

This problem is solved in chart 6.

## chart 6 - logic with building connection string to mongodb

[chart6](charts/chart6-customizing-charts-fixed-mongodb/chart/guestbook)

This chart solves problem that occurred in chart 5 - incorrect connection string to mongodb. 

## chart 7 - installing DEV and TEST environment

[chart7](charts/chart7-DEV-and-TEST-environment/chart/guestbook)

Some important points:
* This chart shows how to generate app URL based on release name.
* It contains one ingress definition.
* This chart contains example how to [iterate through array](charts/chart7-DEV-and-TEST-environment/chart/guestbook/templates/ingress.yaml).
* It shows how to override values from values.yaml by parent chart (sections backend and frontend from [values.yaml](charts/chart7-DEV-and-TEST-environment/chart/guestbook/values.yaml))

>NOTE: the following example requires adding additional entries in *C:\Windows\System32\drivers\etc\hosts* ```127.0.0.1 dev.frontend.local <br /> 127.0.0.1 dev.backend.local <br /> 127.0.0.1 test.frontend.local <br /> 127.0.0.1 test.backend.local```

```ps
helm install dev guestbook --set frontend.config.guestbook_name=DEV
helm install test guestbook --set frontend.config.guestbook_name=TEST
```

Sample execution:
```
PS C:\GitHub\kicaj29\helm\charts\chart7-DEV-and-TEST-environment\chart> helm install dev guestbook --set frontend.config.guestbook_name=DEV
NAME: dev
LAST DEPLOYED: Fri Jul  3 21:14:48 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Congratulations ! You installed guestbook chart sucessfully.
Release name is dev

You can access the Guestbook application at the following urls :
  http://dev.frontend.local
  http://dev.backend.local
Have fun !
PS C:\GitHub\kicaj29\helm\charts\chart7-DEV-and-TEST-environment\chart> helm install test guestbook --set frontend.config.guestbook_name=TEST
NAME: test
LAST DEPLOYED: Fri Jul  3 21:14:59 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Congratulations ! You installed guestbook chart sucessfully.
Release name is test

You can access the Guestbook application at the following urls :
  http://test.frontend.local
  http://test.backend.local
Have fun !
PS C:\GitHub\kicaj29\helm\charts\chart7-DEV-and-TEST-environment\chart> kubectl get pods
NAME                                                        READY   STATUS    RESTARTS   AGE
dev-backend-78995fd9f5-gcn4c                                0/1     Error     2          41s
dev-database-86587b7845-8vrwq                               1/1     Running   0          41s
dev-frontend-749bd48bcf-hkjfz                               1/1     Running   0          41s
ingress-release-ingress-nginx-controller-784ffc969d-gtqzh   1/1     Running   0          15d
test-backend-5b7cbcd488-zdhz8                               1/1     Running   1          30s
test-database-69d589bc8-kq9hh                               1/1     Running   0          30s
test-frontend-869cfcd845-hpl6t                              1/1     Running   0          30s
PS C:\GitHub\kicaj29\helm\charts\chart7-DEV-and-TEST-environment\chart>
```
> :warning: From some reason there is an issue with backend pod from DEV env. There was raised question about it: https://disqus.com/by/disqus_pJ1gDaYRml/

Error from the dev backend pod:

```ps
Guestbook API listening on port 3000!
/home/node/app/node_modules/mongodb/lib/topologies/server.js:240
            throw err;
            ^

MongoError: Authentication failed.
    at Function._getError (/home/node/app/node_modules/mongodb-core/lib/auth/scram.js:125:14)
    at sendAuthCommand (/home/node/app/node_modules/mongodb-core/lib/auth/scram.js:175:31)
    at Connection.messageHandler (/home/node/app/node_modules/mongodb-core/lib/connection/connect.js:334:5)
    at emitTwo (events.js:126:13)
    at Connection.emit (events.js:214:7)
    at processMessage (/home/node/app/node_modules/mongodb-core/lib/connection/connection.js:364:10)
    at Socket.<anonymous> (/home/node/app/node_modules/mongodb-core/lib/connection/connection.js:533:15)
    at emitOne (events.js:116:13)
    at Socket.emit (events.js:211:7)
```

# Managing dependencies
## Packaging charts
There is dedicated command for this. It takes version from yaml file and appends it to the archive name.

```
helm package [chart_name]
```
## Publishing charts in helm repository

[Chartmuseum](https://github.com/helm/chartmuseum) is HTTP server that is dedicated helm repository.   

Helm repository also support signing charts:
```ps
helm package --sign
helm verify chart.tgz
helm install --verify
```

### Example how to package charts

[chart8-pack-publish](charts/chart8-pack-publish/chart/guestbook/dist)

```ps
PS C:\Windows\system32> move C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\charts C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist
PS C:\Windows\system32> move C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\charts\* C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist
PS C:\Windows\system32> cd C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist
PS C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist> ls


    Directory: C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----         7/6/2020   7:53 AM                backend
d-----         7/6/2020   7:53 AM                database
d-----         7/6/2020   7:53 AM                frontend


PS C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist> helm package backend database frontend
Successfully packaged chart and saved it to: C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist\backend-1.2.2.tgz
Successfully packaged chart and saved it to: C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist\database-1.2.2.tgz
Successfully packaged chart and saved it to: C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist\frontend-1.2.2.tgz
PS C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist> helm repo index .
PS C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist> ls


    Directory: C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----         7/6/2020   7:53 AM                backend
d-----         7/6/2020   7:53 AM                database
d-----         7/6/2020   7:53 AM                frontend
-a----         7/6/2020   7:57 AM           1256 backend-1.2.2.tgz
-a----         7/6/2020   7:57 AM           1221 database-1.2.2.tgz
-a----         7/6/2020   7:57 AM           1157 frontend-1.2.2.tgz
-a----         7/6/2020   7:58 AM           1068 index.yaml


PS C:\GitHub\kicaj29\helm\charts\chart8-pack-publish\chart\guestbook\dist>
```

### Example how to publish charts

First run locally https://chartmuseum.com/

```
PS C:\Users\48506> docker run --rm -it -p 8888:8080 -v C:/helmchartswindows:/charts -e DEBUG=true -e STORAGE=local -e STORAGE_LOCAL_ROOTDIR=/charts chartmuseum/chartmuseum:latest
2020-07-05T04:53:35.605Z        DEBUG   Fetching chart list from storage        {"repo": ""}
2020-07-05T04:53:35.647Z        DEBUG   No change detected between cache and storage    {"repo": ""}
2020-07-05T04:53:35.647Z        INFO    Starting ChartMuseum    {"port": 8080}
2020-07-05T04:53:58.448Z        DEBUG   [1] Incoming request: / {"reqID": "0f498614-dff3-41f7-9c17-5c08db577318"}
2020-07-05T04:53:58.449Z        INFO    [1] Request served      {"path": "/", "comment": "", "clientIP": "172.17.0.1", "method": "GET", "statusCode": 200, "latency":
```

Next place index.yaml and tgz files in dedicated folder:
![chartmuseum-files.png](images/chartmuseum-files.png)

Next available charts can be viewed here:
![chartmuseum-get.png](images/chartmuseum-get.png)

## Configuring helm client to work with repositories

Helm maintains a list of repositories. Sample execution:

```
PS C:\GitHub\kicaj29\helm> helm repo list
NAME            URL
stable          https://kubernetes-charts.storage.googleapis.com
ingress-nginx   https://kubernetes.github.io/ingress-nginx
```

To add repository execute the following command:
```
helm repo add [repo name] [address]
helm repo add myrepo http://myserver.org/charts
```
To remove repo from the client:
```
helm repo remove myrepo
```

By default after installation helm is not connected to any repository. Usually one of the first steps is to add official helm 3 repository:

```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```

# Using existing helm chart
TBD

# links
https://app.pluralsight.com/library/courses/kubernetes-packaging-applications-helm/exercise-files   
https://github.com/phcollignon/helm3   
https://www.youtube.com/watch?v=sHUSiM8jqbA (ingress controller installation)
https://github.com/helm/charts