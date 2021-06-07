---
title: Como solucionar problemas do contêiner | Microsoft Docs
description: Este artigo descreve como você pode solucionar problemas e resolver questões com o contêiner insights.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: b7618e9073308da67a8e17c82375a0f05925a542
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627108"
---
# <a name="troubleshooting-container-insights"></a>Solucionando problemas de contêineres

Ao configurar o monitoramento do seu cluster do AKS (serviço kubernetes do Azure) com o contêiner insights, você pode encontrar um problema que impede a coleta de dados ou o status de relatórios. Este artigo detalha alguns problemas comuns e etapas de solução de problemas.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Erro de autorização durante a operação de atualização ou integração

Ao habilitar o insights de contêiner ou atualizar um cluster para dar suporte à coleta de métricas, você pode receber um erro semelhante ao seguinte: *a identidade do usuário de <do cliente> ' com a ID de objeto ' <objectId> ' do usuário não tem autorização para executar a ação ' Microsoft. Authorization/roleAssignments/Write ' sobre o escopo*

Durante a integração ou o processo de atualização, é tentada a concessão da atribuição de função de **Editor de métricas de monitoramento** no recurso de cluster. O usuário que inicia o processo para habilitar o insights de contêiner ou a atualização para dar suporte à coleção de métricas deve ter acesso à permissão **Microsoft. Authorization/roleAssignments/Write** no escopo de recurso de cluster AKs. Somente os membros das funções internas do **proprietário** e do **administrador de acesso do usuário** recebem acesso a essa permissão. Se suas políticas de segurança exigirem a atribuição de permissões de nível granular, recomendamos que você exiba [funções personalizadas](../../role-based-access-control/custom-roles.md) e atribua-as aos usuários que precisam dela.

Você também pode conceder manualmente essa função da portal do Azure executando as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**, encontrado no canto superior esquerdo. Na lista de recursos, digite **kubernetes**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Azure kubernetes**.
3. Na lista de clusters kubernetes, selecione um na lista.
2. No menu à esquerda, clique em **controle de acesso (iam)**.
3. Selecione **+ Adicionar** para adicionar uma atribuição de função e selecione a função de **Editor de métricas de monitoramento** e, na caixa **selecionar** , digite **AKs** para filtrar os resultados em apenas as entidades de serviço de clusters definidas na assinatura. Selecione aquele na lista específica para esse cluster.
4. Selecione **Salvar** para finalizar a atribuição da função.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>O insights de contêiner está habilitado, mas não está relatando nenhuma informação

Se o contêiner insights for habilitado e configurado com êxito, mas você não puder exibir informações de status ou nenhum resultado for retornado de uma consulta de log, você diagnosticará o problema seguindo estas etapas:

1. Verifique o status do agente executando o comando:

    `kubectl get ds omsagent --namespace=kube-system`

    A saída deve ser semelhante ao seguinte exemplo, que indica que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Se você tiver nós do Windows Server, verifique o status do agente executando o comando:

    `kubectl get ds omsagent-win --namespace=kube-system`

    A saída deve ser semelhante ao seguinte exemplo, que indica que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Verifique o status de implantação com a versão do agente *06072018* ou posterior usando o comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A saída deve ser semelhante ao seguinte exemplo, que indica que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Verifique o status do pod para verificar se ele está em execução usando o comando: `kubectl get pods --namespace=kube-system`

    A saída deve ser semelhante ao seguinte exemplo, com o status de *Executando* para o omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Mensagens de erro

A tabela a seguir resume os erros conhecidos que você pode encontrar ao usar o contêiner insights.

| Mensagens de erro  | Ação |
| ---- | --- |
| Mensagem de erro `No data for selected filters`  | Pode levar algum tempo para estabelecer o fluxo de dados de monitoramento para clusters recém-criados. Aguarde pelo menos 10 a 15 minutos para que os dados sejam exibidos para o cluster. |
| Mensagem de erro `Error retrieving data` | Enquanto o cluster do serviço kubernetes do Azure está configurando para monitoramento de integridade e desempenho, uma conexão é estabelecida entre o cluster e o espaço de trabalho do Azure Log Analytics. Um espaço de trabalho do Log Analytics é usado para armazenar todos os dados de monitoramento do cluster. Esse erro pode ocorrer quando seu espaço de trabalho Log Analytics foi excluído. Verifique se o espaço de trabalho foi excluído e, se foi, você precisará reabilitar o monitoramento do cluster com o contêiner insights e especificar um novo espaço de trabalho ou criar um existente. Para reabilitar, você precisará [desabilitar](container-insights-optout.md) o monitoramento do cluster e [habilitar](container-insights-enable-new-cluster.md) o insights de contêiner novamente. |
| `Error retrieving data` Depois de adicionar informações de contêiner por meio do AZ AKs CLI | Ao habilitar o monitoramento usando o `az aks cli` , os insights de contêiner podem não ser implantados corretamente. Verifique se a solução está implantada. Para verificar, acesse o espaço de trabalho Log Analytics e veja se a solução está disponível selecionando **soluções** no painel à esquerda. Para resolver esse problema, você precisará reimplantar a solução seguindo as instruções sobre [como implantar informações de contêiner](container-insights-onboard.md) |

Para ajudar a diagnosticar o problema, fornecemos um [script de solução de problemas](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot).

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>O agente de informações do contêiner pods de Réplicaset não está agendado no cluster kubernetes não Azure

O agente de informações do contêiner pods de Réplicaset tem uma dependência nos seguintes seletores de nó nos nós de trabalho (ou agente) para o agendamento:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Se os nós de trabalho não tiverem rótulos de nó anexados, o pods de agente de Réplicaset não será agendado. Consulte [kubernetes atribuir rótulo seletores](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) para obter instruções sobre como anexar o rótulo.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Os gráficos de desempenho não mostram a CPU ou memória de nós e contêineres em um cluster não Azure

O agente de informações de contêiner pods usa o ponto de extremidade cAdvisor no agente de nó para reunir as métricas de desempenho. Verifique se o agente em contêineres no nó está configurado para permitir que ele `cAdvisor port: 10255` seja aberto em todos os nós no cluster para coletar métricas de desempenho.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>O cluster kubernetes não Azure não está aparecendo em informações de contêiner

Para exibir o cluster kubernetes não Azure em insights de contêiner, o acesso de leitura é necessário no espaço de trabalho Log Analytics que dá suporte a essa Insight e no recurso de solução ContainerInsights do contêiner insights **(*espaço de trabalho*)**.

## <a name="metrics-arent-being-collected"></a>As métricas não estão sendo coletadas

1. Verifique se o cluster está em uma [região com suporte para métricas personalizadas](../essentials/metrics-custom-overview.md#supported-regions).

2. Verifique se a atribuição de função de **Editor de métricas de monitoramento** existe usando o seguinte comando da CLI:

    ``` azurecli
    az role assignment list --assignee "SP/UserassignedMSI for omsagent" --scope "/subscriptions/<subid>/resourcegroups/<RG>/providers/Microsoft.ContainerService/managedClusters/<clustername>" --role "Monitoring Metrics Publisher"
    ```
    Para clusters com MSI, a ID de cliente atribuída ao usuário para omsagent é alterada toda vez que o monitoramento está habilitado e desabilitado, portanto, a atribuição de função deve existir na ID do cliente MSI atual. 

3. Para clusters com Azure Active Directory identidade de Pod habilitada e usando o MSI:

   - Verifique o rótulo necessário **kubernetes.Azure.com/ManagedBy: AKs**  está presente no pods omsagent usando o seguinte comando:

        `kubectl get pods --show-labels -n kube-system | grep omsagent`

    - Verifique se as exceções estão habilitadas quando a identidade do pod estiver habilitada usando um dos métodos com suporte em https://github.com/Azure/aad-pod-identity#1-deploy-aad-pod-identity .

        Execute o seguinte comando para verificar:

        `kubectl get AzurePodIdentityException -A -o yaml`

        Você deve receber uma saída semelhante à seguinte:

        ```
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: mic-exception
        namespace: default
        spec:
        podLabels:
        app: mic
        component: mic
        ---
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: aks-addon-exception
        namespace: kube-system
        spec:
        podLabels:
        kubernetes.azure.com/managedby: aks
        ```



## <a name="next-steps"></a>Próximas etapas

Com o monitoramento habilitado para capturar métricas de integridade para os nós de cluster do AKS e pods, essas métricas de integridade estão disponíveis no portal do Azure. Para saber como usar o contêiner insights, consulte [exibir a integridade do serviço kubernetes do Azure](container-insights-analyze.md).
