---
title: Como solucionar problemas do Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode solucionar e resolver problemas com o Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 5727702ff973523ce7ab6400c1c7748e0584acbf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010581"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Solução de problemas do Azure Monitor para contêineres

Quando você configurar o monitoramento do cluster do AKS (Serviço de Kubernetes do Azure) com o Azure Monitor para contêineres, poderá encontrar um problema que impede a coleta de dados ou relatórios de status. Este artigo detalha alguns problemas comuns e etapas de solução de problemas.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Erro de autorização durante a operação de atualização ou integração

Ao habilitar Azure Monitor para contêineres ou atualizar um cluster para dar suporte à coleta de métricas, você pode receber um erro semelhante ao seguinte: *a identidade do usuário <cliente> ' with ID do objeto ' <o ObjectID do usuário> ' não tem autorização para executar a ação ' Microsoft. Authorization/roleAssignments/Write ' sobre o escopo*

Durante a integração ou o processo de atualização, é tentada a concessão da atribuição de função de **Editor de métricas de monitoramento** no recurso de cluster. O usuário que inicia o processo para habilitar a Azure Monitor para contêineres ou a atualização para dar suporte à coleção de métricas deve ter acesso à permissão **Microsoft. Authorization/roleAssignments/Write** no escopo de recurso de cluster AKs. Somente os membros das funções internas do **proprietário** e do **administrador de acesso do usuário** recebem acesso a essa permissão. Se suas políticas de segurança exigirem a atribuição de permissões de nível granular, recomendamos que você exiba [funções personalizadas](../../role-based-access-control/custom-roles.md) e atribua-as aos usuários que precisam dela.

Você também pode conceder manualmente essa função da portal do Azure executando as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**, encontrado no canto superior esquerdo. Na lista de recursos, digite **kubernetes**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Azure kubernetes**.
3. Na lista de clusters kubernetes, selecione um na lista.
2. No menu à esquerda, clique em **controle de acesso (iam)**.
3. Selecione **+ Adicionar** para adicionar uma atribuição de função e selecione a função de **Editor de métricas de monitoramento** e, na caixa **selecionar** , digite **AKs** para filtrar os resultados em apenas as entidades de serviço de clusters definidas na assinatura. Selecione aquele na lista específica para esse cluster.
4. Selecione **Salvar** para finalizar a atribuição da função.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>O Azure Monitor para contêineres está habilitado, mas não relata nenhuma informação

Se Azure Monitor para contêineres for habilitado e configurado com êxito, mas você não puder exibir informações de status ou nenhum resultado for retornado de uma consulta de log, você diagnosticará o problema seguindo estas etapas:

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

A tabela abaixo resume os erros conhecidos que você pode encontrar ao usar o Monitor do Azure para contêineres.

| Mensagens de erro  | Ação |
| ---- | --- |
| Mensagem de erro `No data for selected filters`  | Pode levar algum tempo para estabelecer o fluxo de dados de monitoramento para clusters recém-criados. Aguarde pelo menos 10 a 15 minutos para que os dados sejam exibidos para o cluster. |
| Mensagem de erro `Error retrieving data` | Enquanto o cluster do serviço kubernetes do Azure está configurando para monitoramento de integridade e desempenho, uma conexão é estabelecida entre o cluster e o espaço de trabalho do Azure Log Analytics. Um espaço de trabalho do Log Analytics é usado para armazenar todos os dados de monitoramento do cluster. Esse erro pode ocorrer quando seu espaço de trabalho Log Analytics foi excluído. Verifique se o espaço de trabalho foi excluído e, se ele foi, será necessário reabilitar o monitoramento do cluster com Azure Monitor para contêineres e especificar um novo espaço de trabalho ou criar um existente. Para reabilitar, você precisará [desabilitar](container-insights-optout.md) o monitoramento do cluster e [habilitar](container-insights-enable-new-cluster.md) Azure monitor para contêineres novamente. |
| `Error retrieving data` Depois de adicionar o Azure Monitor para contêineres por meio da cli do az aks | Ao habilitar o monitoramento usando `az aks cli` , os Azure monitor para contêineres podem não ser implantados corretamente. Verifique se a solução está implantada. Para verificar, acesse o espaço de trabalho Log Analytics e veja se a solução está disponível selecionando **soluções** no painel à esquerda. Para resolver esse problema, você precisará reimplantar a solução seguindo as instruções em [como implantar o Monitor do Azure para contêineres](container-insights-onboard.md) |

Para ajudar a diagnosticar o problema, fornecemos um [script de solução de problemas](https://aka.ms/troubleshooting-script).

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>O Azure Monitor para os pods do agente de Réplicaset de contêineres não estão agendados no cluster kubernetes não Azure

Azure Monitor for containers o pods do agente de Réplicaset tem uma dependência nos seguintes seletores de nó nos nós de trabalho (ou agente) para o agendamento:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Se os nós de trabalho não tiverem rótulos de nó anexados, o pods de agente de Réplicaset não será agendado. Consulte [kubernetes atribuir rótulo seletores](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) para obter instruções sobre como anexar o rótulo.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Os gráficos de desempenho não mostram a CPU ou memória de nós e contêineres em um cluster não Azure

Azure Monitor para os pods de agente de contêineres usa o ponto de extremidade cAdvisor no agente de nó para coletar as métricas de desempenho. Verifique se o agente em contêineres no nó está configurado para permitir que ele `cAdvisor port: 10255` seja aberto em todos os nós no cluster para coletar métricas de desempenho.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>O cluster kubernetes não Azure não está aparecendo em Azure Monitor para contêineres

Para exibir o cluster kubernetes não Azure no Azure Monitor para contêineres, o acesso de leitura é necessário no espaço de trabalho Log Analytics que dá suporte a essa percepção e no recurso **ContainerInsights (*espaço de trabalho*)** da solução de informações do contêiner.

## <a name="next-steps"></a>Próximas etapas

Com o monitoramento habilitado para capturar métricas de integridade para os nós de cluster do AKS e pods, essas métricas de integridade estão disponíveis no portal do Azure. Para saber como usar o Azure Monitor para contêineres, veja [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md).
