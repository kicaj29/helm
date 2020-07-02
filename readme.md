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
- [links](#links)
- [why helm (v3)](#why-helm--v3--1)
- [chart](#chart-1)
- [persistency](#persistency-1)
- [three-way merge patches update](#three-way-merge-patches-update-1)
- [namespaces](#namespaces-1)
- [helm installation on win10](#helm-installation-on-win10-1)
  * [install helm chart](#install-helm-chart-1)
  * [uninstall helm chart](#uninstall-helm-chart-1)
  * [helm variables](#helm-variables-1)
- [building helm charts](#building-helm-charts-1)
- [helm release and release revision.](#helm-release-and-release-revision-1)
- [basic commands](#basic-commands-1)
- [install, upgrade, rollback](#install--upgrade--rollback-1)
  * [chart1](#chart1-1)
  * [chart2](#chart2-1)
  * [rollback to revision 1 of the release](#rollback-to-revision-1-of-the-release-1)
    + [history](#history-1)
  * [chart 3](#chart-3-1)
  * [chart 4 - INGRESS controller](#chart-4---ingress-controller-1)
- [Customizing charts with helm templates](#customizing-charts-with-helm-templates-1)
  * [Testing custom templates](#testing-custom-templates-1)
    + [Static](#static-1)
    + [Dynamic](#dynamic-1)
  * [Data sources for values](#data-sources-for-values-1)
  * [Functions and pipelines](#functions-and-pipelines-1)
  * [Modifying scope using with](#modifying-scope-using-with-1)
  * [Controlling carriage returns, whitespaces, indent.](#controlling-carriage-returns--whitespaces--indent-1)
  * [Logical operators and functions](#logical-operators-and-functions-1)
  * [Flow control](#flow-control-1)
  * [Variables](#variables-1)
  * [Helper functions (sub-templates)](#helper-functions--sub-templates--1)
    + [Abstract charts - library](#abstract-charts---library-1)
  * [chart 5 - custom values](#chart-5---custom-values-1)
- [links](#links-1)
- [why helm (v3)](#why-helm--v3--2)
- [chart](#chart-2)
- [persistency](#persistency-2)
- [three-way merge patches update](#three-way-merge-patches-update-2)
- [namespaces](#namespaces-2)
- [helm installation on win10](#helm-installation-on-win10-2)
  * [install helm chart](#install-helm-chart-2)
  * [uninstall helm chart](#uninstall-helm-chart-2)
  * [helm variables](#helm-variables-2)
- [building helm charts](#building-helm-charts-2)
- [helm release and release revision.](#helm-release-and-release-revision-2)
- [basic commands](#basic-commands-2)
- [install, upgrade, rollback](#install--upgrade--rollback-2)
  * [chart1](#chart1-2)
  * [chart2](#chart2-2)
  * [rollback to revision 1 of the release](#rollback-to-revision-1-of-the-release-2)
    + [history](#history-2)
  * [chart 3](#chart-3-2)
  * [chart 4 - INGRESS controller](#chart-4---ingress-controller-2)
- [Customizing charts with helm templates](#customizing-charts-with-helm-templates-2)
  * [Testing custom templates](#testing-custom-templates-2)
    + [Static](#static-2)
    + [Dynamic](#dynamic-2)
  * [Data sources for values](#data-sources-for-values-2)
  * [Functions and pipelines](#functions-and-pipelines-2)
  * [Modifying scope using with](#modifying-scope-using-with-2)
  * [Controlling carriage returns, whitespaces, indent.](#controlling-carriage-returns--whitespaces--indent-2)
  * [Logical operators and functions](#logical-operators-and-functions-2)
  * [Flow control](#flow-control-2)
  * [Variables](#variables-2)
  * [Helper functions (sub-templates)](#helper-functions--sub-templates--2)
    + [Abstract charts - library](#abstract-charts---library-2)
  * [chart 5 - custom values](#chart-5---custom-values-2)
  * [chart 6 - logic with building connection string to mongodb](#chart-6---logic-with-building-connection-string-to-mongodb)
- [links](#links-2)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>



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

# links
https://app.pluralsight.com/library/courses/kubernetes-packaging-applications-helm/exercise-files   
https://github.com/phcollignon/helm3   
https://www.youtube.com/watch?v=sHUSiM8jqbA (ingress controller installation)
https://github.com/helm/charts
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

# links
https://app.pluralsight.com/library/courses/kubernetes-packaging-applications-helm/exercise-files   
https://github.com/phcollignon/helm3   
https://www.youtube.com/watch?v=sHUSiM8jqbA (ingress controller installation)
https://github.com/helm/charts

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

# links
https://app.pluralsight.com/library/courses/kubernetes-packaging-applications-helm/exercise-files   
https://github.com/phcollignon/helm3   
https://www.youtube.com/watch?v=sHUSiM8jqbA (ingress controller installation)
https://github.com/helm/charts