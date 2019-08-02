---
title: Implantar uma instância do Prometheus autônomo em um cluster do Azure Red Hat OpenShift | Microsoft Docs
description: Crie uma instância do Prometheus em um cluster do Azure Red Hat OpenShift para monitorar métricas do seu aplicativo.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, TOA, openshift, métricas, red hat
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827014"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Implantar uma instância do Prometheus autônomo em um cluster do Azure Red Hat OpenShift

Este artigo descreve como configurar uma instância do Prometheus autônomo que usa a descoberta de serviço em um cluster do Azure Red Hat OpenShift.

> [!NOTE]
> Acesso de administrador do cliente para cluster Azure Red Hat OpenShift não é necessário.

Instalação de destino:

- Um projeto (Prometeu-projeto), que contém o Prometheus e Alertmanager.
- Dois projetos (aplicativo Projeto1 e Projeto2 do aplicativo), que contêm os aplicativos para monitorar.

Você vai preparar alguns arquivos de configuração do Prometheus localmente. Crie uma nova pasta para armazená-los. Arquivos de configuração são armazenados no cluster como segredos, no caso de tokens secretas forem adicionadas posteriormente ao cluster.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Entrar para o cluster usando a ferramenta OC

1. Abra um navegador da web e, em seguida, vá para o console web do seu cluster (https://openshift. *id aleatório*. *região*. azmosa.io).
2. Entre com suas credenciais do Azure.
3. Selecione seu nome de usuário no canto superior direito e, em seguida, selecione **comando de logon de cópia**.
4. Cole o nome de usuário no terminal que você usará.

> [!NOTE]
> Para ver se você está conectado ao cluster correto, execute o `oc whoami -c` comando.

## <a name="prepare-the-projects"></a>Preparar os projetos

Para criar os projetos, execute os seguintes comandos:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Você pode usar o `-n` ou `--namespace` parâmetro, ou selecione um projeto ativo, executando o `oc project` comando.

## <a name="prepare-the-prometheus-configuration-file"></a>Preparar o arquivo de configuração do Prometheus
Crie um arquivo de prometheus.yml digitando o seguinte conteúdo:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Crie um segredo chamado Prom inserindo a seguinte configuração:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

O arquivo de prometheus.yml é um arquivo de configuração básico do Prometheus. Ele define os intervalos e configura a descoberta automática em três projetos (project Prometeu, project1 do aplicativo, Projeto2 do aplicativo). No arquivo de configuração anterior, os pontos de extremidade de descoberta automática são obtidos por meio de HTTP sem autenticação.

Para obter mais informações sobre pontos de extremidade de captura, consulte [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Preparar o arquivo de configuração Alertmanager
Crie um arquivo de alertmanager.yml digitando o seguinte conteúdo:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Crie um segredo chamado alertas Prom inserindo a seguinte configuração:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml é o arquivo de configuração do Gerenciador de alertas.

> [!NOTE]
> Para verificar se as duas etapas anteriores, execute o `oc get secret -n prometheus-project` comando.

## <a name="start-prometheus-and-alertmanager"></a>Iniciar o Prometheus e Alertmanager
Vá para [repositório de origem do openshift](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e baixe a [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) modelo. Aplique o modelo de projeto Prometeu inserindo a seguinte configuração:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
O arquivo standalone.yaml prometheus é um modelo do OpenShift. Ele criará uma instância do Prometheus com o proxy de oauth na frente ele e uma instância de Alertmanager, também são protegidos com o proxy de oauth. Neste modelo, oauth-proxy está configurado para permitir que qualquer usuário que pode "get" o namespace do projeto do prometheus (consulte a `-openshift-sar` sinalizador).

> [!NOTE]
> Para verificar se o prom StatefulSet tem igual desejado e o atual número de réplicas, execute o `oc get statefulset -n prometheus-project` comando. Para verificar todos os recursos do projeto, execute o `oc get all -n prometheus-project` comando.

## <a name="add-permissions-to-allow-service-discovery"></a>Adicionar permissões para permitir a descoberta de serviço

Crie um arquivo do prometheus sdrole.yml digitando o seguinte conteúdo:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Para aplicar o modelo a todos os projetos que você deseja permitir a descoberta de serviço, execute os seguintes comandos:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Para que o Prometheus para coletar métricas de si mesma, aplique as permissões no projeto do prometheus.

> [!NOTE]
> Para verificar a função e RoleBinding foram criados corretamente, execute as `oc get role` e `oc get rolebinding` comandos.

## <a name="optional-deploy-example-application"></a>Opcional: Implantar o aplicativo de exemplo

Tudo está funcionando, mas não há nenhuma fonte de métricas. Vá para a URL do Prometheus (https://prom-prometheus-project.apps. *id aleatório*. *região*.azmosa.io/). Você pode localizá-la usando o seguinte comando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Lembre-se de adicionar o prefixo https:// ao início do nome do host.

O **Status > descoberta de serviço** página mostrará 0 de 0/destinos de Active Directory.

Para implantar um aplicativo de exemplo, que expõe as métricas básicas de Python no ponto de extremidade /metrics, execute os seguintes comandos:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Os novos aplicativos devem aparecer como destinos válidos na página de descoberta de serviço dentro de 30 segundos após a implantação.

Para obter mais detalhes, selecione **Status** > **destinos**.

> [!NOTE]
> Para cada destino capturado com êxito, Prometeu adiciona um ponto de dados em que a métrica de cima. Selecione **Prometheus** no canto superior esquerdo, insira **backup** como a expressão e, em seguida, selecione **Execute**.

## <a name="next-steps"></a>Próximas etapas

Você pode adicionar instrumentação personalizada de Prometeu aos seus aplicativos. A biblioteca de cliente do Prometheus, que simplifica a preparação de métricas do Prometheus, está pronta para linguagens de programação diferentes.

Para obter mais informações, consulte as seguintes bibliotecas do GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
