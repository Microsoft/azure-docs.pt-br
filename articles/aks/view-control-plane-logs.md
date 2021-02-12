---
title: Exibir logs do controlador de serviço de Kubernetes do Azure (AKS)
description: Saiba como habilitar e exibir os logs do plano de controle kubernetes no serviço kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 7fce3db6f3636a5ee984c8be44f877c5636f4e16
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948716"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>Habilitar e examinar os logs do plano de controle do kubernetes no serviço kubernetes do Azure (AKS)

Com o AKS (serviço kubernetes do Azure), os componentes do plano de controle, como o *Kube-apiserver* e o *Kube-Controller-Manager* , são fornecidos como um serviço gerenciado. Você cria e gerencia os nós que executam o *kubelet* e o runtime do contêiner e implementa seus aplicativos por meio do servidor gerenciado do Kubernetes API. Para ajudar a solucionar problemas de seu aplicativo e serviços, talvez seja necessário exibir os logs gerados por esses componentes do plano de controle. Este artigo mostra como usar os logs de Azure Monitor para habilitar e consultar os logs dos componentes do plano de controle kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer um cluster AKS existente em execução na sua conta do Azure. Se você ainda não tiver um cluster AKS, crie um usando o [Portal do Azure][cli-quickstart] ou [do Portal do Azure][portal-quickstart]. Os logs de Azure Monitor funcionam com o RBAC kubernetes, o RBAC do Azure e os clusters AKS não habilitados para RBAC.

## <a name="enable-resource-logs"></a>Habilitar logs de recursos

Para ajudar a coletar e analisar dados de várias origens, os logs do Azure Monitor disponibilizam uma linguagem de consulta e um mecanismo de análise que fornecem insights ao ambiente. Um workspace é usado para agrupar e analisar os dados e pode se integrar a outros serviços do Azure, como o Application Insights e o Security Center. Para usar uma plataforma diferente para analisar os logs, você pode optar por enviar logs de recursos para uma conta de armazenamento do Azure ou Hub de eventos. Para obter mais informações, veja [O que são os logs do Azure Monitor?][log-analytics-overview].

Os logs de Azure Monitor são habilitados e gerenciados no portal do Azure. Para habilitar a coleta de log para os componentes do plano de controle kubernetes no cluster do AKS, abra o portal do Azure em um navegador da Web e conclua as seguintes etapas:

1. Selecione o grupo de recursos para seu cluster AKS, como *myResourceGroup*. Não selecione o grupo de recursos que contém seus recursos individuais de cluster do AKS, como *MC_myResourceGroup_myAKSCluster_eastus*.

2. No lado esquerdo, escolha **Diagnostic settings**.

3. Selecione o cluster AKS, como *myAKSCluster*, e escolha **Adicionar configuração de diagnóstico**.
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="Captura de tela de portal do Azure em uma janela do navegador mostrando as configurações de diagnóstico, indicando que ' adicionar configuração de diagnóstico ' deve ser selecionado":::

4. Digite um nome, como *myAKSClusterLogs*, e selecione a opção para **Enviar para o espaço de trabalho do Log Analytics**.

5. Selecione um espaço de trabalho existente ou crie um novo. Se você criar um espaço de trabalho, forneça um nome de espaço de trabalho, um grupo de recursos e um local.

6. Na lista de logs disponíveis, selecione os logs que deseja habilitar. Para este exemplo, habilite os logs *Kube* e *Kube-Audit-admin* . Os logs comuns incluem *Kube-apiserver*, *Kube-Controller-Manager* e *Kube-Scheduler*. Você pode retornar e alterar os logs coletados depois que os espaços de trabalho do Log Analytics estiverem ativados.

7. Quando estiver pronto, selecione **Salvar** para ativar a coleta dos logs selecionados.
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Captura de tela de &quot;adicionar configuração de diagnóstico&quot; do portal do Azure. Um destino de ' enviar para Log Analytics espaço de trabalho ' e logs ' Kube-Audit ' e ' Kube-Audit-admin ' estão selecionados":::

## <a name="log-categories"></a>Categorias de log

Além das entradas escritas por kubernetes, os logs de auditoria do seu projeto também têm entradas de AKS.

Os logs de auditoria são registrados em três categorias: *Kube-Audit*, *Kube-Audit-admin* e *Guard*.

- A categoria *Kube-Audit* contém todos os dados de log de auditoria para cada evento de auditoria, incluindo *Get*, *lista*, *criação*, *atualização*, *exclusão*, *patch* e *post*.
- A categoria *Kube-Audit-admin* é um subconjunto da categoria *Kube-Audit* log. *Kube-Audit-admin* reduz o número de logs significativamente, excluindo os eventos de auditoria *Get* e *list* do log.
- A categoria de *proteção* é uma auditoria gerenciada do Azure AD e RBAC do Azure. Para o Azure AD gerenciado: token in, informações do usuário out. Para o RBAC do Azure: revisões de acesso para dentro e fora.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Agendar um pod de teste no cluster do AKS

Para gerar alguns logs, crie um novo pod no seu cluster AKS. O seguinte exemplo de manifesto YAML pode ser usado para criar uma instância básica do NGINX. Crie um arquivo chamado `nginx.yaml` em um editor de sua escolha e cole o seguinte conteúdo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Crie o pod com o comando [kubectl create][kubectl-create] e especifique seu arquivo YAML, conforme mostrado no exemplo a seguir:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Exibir logs coletados

Pode levar até 10 minutos para que os logs de diagnóstico sejam habilitados e apareçam.

> [!NOTE]
> Se você precisar de todos os dados do log de auditoria para fins de conformidade ou outras finalidades, colete e armazene-os em um armazenamento barato, como o armazenamento de BLOB. Use a categoria de log *Kube-Audit-admin* para coletar e salvar um conjunto significativo de dados de log de auditoria para fins de monitoramento e alerta.

No portal do Azure, navegue até o cluster AKS e selecione **logs** no lado esquerdo. Feche a janela *consultas de exemplo* se ela aparecer.

No lado esquerdo, escolha **Logs**. Para exibir os logs de *Kube de auditoria* , insira a seguinte consulta na caixa de texto:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Muitos logs são provavelmente retornados. Para reduzir a consulta para exibir os logs sobre o Pod NGINX criado na etapa anterior, adicione uma instrução *Where* adicional para pesquisar *Nginx* , conforme mostrado na seguinte consulta de exemplo:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Para exibir os logs *Kube-Audit-admin* , insira a seguinte consulta na caixa de texto:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

Neste exemplo, a consulta mostra todos os trabalhos de criação em *Kube-Audit-admin*. Provavelmente, há muitos resultados retornados, para delimitar a consulta para exibir os logs sobre o Pod NGINX criado na etapa anterior, adicione uma instrução *Where* adicional para pesquisar *Nginx* , conforme mostrado na consulta de exemplo a seguir.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Para obter mais informações sobre como consultar e filtrar seus dados de log, consulte [Visualizar ou analisar dados coletados com a pesquisa de registros da análise de logs][analyze-log-analytics].

## <a name="log-event-schema"></a>Esquema do log de eventos

AKS registra os seguintes eventos:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Pulsação][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Perf][log-schema-perf]

## <a name="log-roles"></a>Funções de log

| Função                     | Descrição |
|--------------------------|-------------|
| *aksService*             | O nome de exibição no log de auditoria para a operação do plano de controle (do hcpService) |
| *masterclient*           | O nome de exibição no log de auditoria para MasterClientCertificate, o certificado obtido de AZ AKs Get-Credentials |
| *nodeclient*             | O nome de exibição para ClientCertificate, que é usado por nós de agente |

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como habilitar e examinar os logs dos componentes do plano de controle kubernetes em seu cluster AKS. Para monitorar e solucionar problemas adicionais, você também pode [visualizar os logs do Kubelet][kubelet-logs] e [ativar o acesso ao nó do SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf