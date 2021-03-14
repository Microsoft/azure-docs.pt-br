---
title: Implantar instância de Prometheus no cluster do Azure Red Hat OpenShift
description: Crie uma instância do Prometheus em um cluster do Azure Red Hat OpenShift para monitorar as métricas do seu aplicativo.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometheus, aro, openshift, métricas, Red Hat
ms.openlocfilehash: 974096e515401ab5e292dbad4c9beba7f1611a8a
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633437"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Implantar uma instância Prometheus autônoma em um cluster do Azure Red Hat OpenShift

> [!IMPORTANT]
> O Azure Red Hat OpenShift 3,11 será desativado em 30 de junho de 2022. O suporte para a criação de novos clusters do Azure Red Hat OpenShift 3,11 continua até 30 de novembro de 2020. Após a aposentadoria, os clusters restantes do Azure Red Hat OpenShift 3,11 serão desligados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster do Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se você tiver dúvidas específicas, [entre em contato conosco](mailto:arofeedback@microsoft.com).

Este artigo descreve como configurar uma instância Prometheus autônoma que usa a descoberta de serviço em um cluster do Azure Red Hat OpenShift.

> [!NOTE]
> O acesso de administrador do cliente ao cluster do Azure Red Hat OpenShift não é necessário.

Configuração de destino:

- Um projeto (Prometheus-Project), que contém Prometheus e Alertmanager.
- Dois projetos (App-Projeto1 e app-Projeto2), que contêm os aplicativos a serem monitorados.

Você preparará alguns arquivos de configuração Prometheus localmente. Crie uma nova pasta para armazená-las. Os arquivos de configuração são armazenados no cluster como segredos, caso os tokens secretos sejam adicionados posteriormente ao cluster.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Entre no cluster usando a ferramenta OC

1. Abra um navegador da Web e vá para o console Web do seu cluster ( https://openshift .*Random-ID*. *Region*. azmosa.IO).
2. Entre com suas credenciais do Azure.
3. Selecione seu nome de usuário no canto superior direito e, em seguida, selecione o **comando copiar logon**.
4. Cole seu nome de usuário no terminal que você usará.

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
> Você pode usar o `-n` parâmetro ou ou `--namespace` selecionar um projeto ativo executando o `oc project` comando.

## <a name="prepare-the-prometheus-configuration-file"></a>Preparar o arquivo de configuração do Prometheus
Crie um arquivo Prometheus. yml inserindo o seguinte conteúdo:
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
Crie um segredo chamado Prom digitando a seguinte configuração:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

O arquivo Prometheus. yml é um arquivo de configuração de Prometheus básico. Ele define os intervalos e configura a descoberta automática em três projetos (Prometheus-Project, app-Projeto1, app-Projeto2). No arquivo de configuração anterior, os pontos de extremidade descobertos automaticamente são refeitos sobre HTTP sem autenticação.

Para obter mais informações sobre os pontos de extremidade de recorte, consulte [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Preparar o arquivo de configuração do Alertmanager
Crie um arquivo alertmanager. yml inserindo o seguinte conteúdo:
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
Crie um segredo chamado Prom-Alerts digitando a seguinte configuração:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager. yml é o arquivo de configuração do Alert Manager.

> [!NOTE]
> Para verificar as duas etapas anteriores, execute o `oc get secret -n prometheus-project` comando.

## <a name="start-prometheus-and-alertmanager"></a>Iniciar o Prometheus e o Alertmanager
Acesse o [repositório openshift/Origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e baixe o modelo [Prometheus-standalone. YAML](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) . Aplique o modelo ao Prometheus-Project inserindo a seguinte configuração:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
O arquivo Prometheus-standalone. YAML é um modelo OpenShift. Ele criará uma instância de Prometheus com o proxy OAuth na frente e uma instância de Alertmanager, também protegida com o OAuth proxy. Neste modelo, o proxy OAuth é configurado para permitir que qualquer usuário que possa "obter" o namespace Prometheus (Veja o `-openshift-sar` sinalizador).

> [!NOTE]
> Para verificar se o Prom com estado definido é igual ao desejado e às réplicas de número atual, execute o `oc get statefulset -n prometheus-project` comando. Para verificar todos os recursos no projeto, execute o `oc get all -n prometheus-project` comando.

## <a name="add-permissions-to-allow-service-discovery"></a>Adicionar permissões para permitir a descoberta de serviço

Crie um arquivo Prometheus-sdrole. yml inserindo o seguinte conteúdo:
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
Para aplicar o modelo a todos os projetos dos quais você deseja permitir a descoberta de serviço, execute os seguintes comandos:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Para verificar se a função e a Funçãobinding foram criadas corretamente, execute os `oc get role` `oc get rolebinding` comandos e.

## <a name="optional-deploy-example-application"></a>Opcional: implantar aplicativo de exemplo

Tudo está funcionando, mas não há fontes de métricas. Vá para a URL do Prometheus ( https://prom-prometheus-project.apps .*Random-ID*. *Region*. azmosa.IO/). Você pode encontrá-lo usando o seguinte comando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Lembre-se de adicionar o prefixo https://ao início do nome do host.

O **Status > página descoberta de serviço** mostrará os destinos ativos 0/0.

Para implantar um aplicativo de exemplo, que expõe as métricas básicas do Python no ponto de extremidade/Metrics, execute os seguintes comandos:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Os novos aplicativos devem aparecer como destinos válidos na página de descoberta de serviço dentro de 30 segundos após a implantação.

Para obter mais detalhes, selecione destinos de **status**  >  .

> [!NOTE]
> Para cada destino resumido com êxito, o Prometheus adiciona um ponto de dados na métrica acima. Selecione **Prometheus** no canto superior esquerdo **, insira como** a expressão e, em seguida, selecione **executar**.

## <a name="next-steps"></a>Próximas etapas

Você pode adicionar instrumentação Prometheus personalizada aos seus aplicativos. A biblioteca de cliente Prometheus, que simplifica a preparação de métricas de Prometheus, está pronta para diferentes linguagens de programação.

Para obter mais informações, consulte as seguintes bibliotecas do GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
