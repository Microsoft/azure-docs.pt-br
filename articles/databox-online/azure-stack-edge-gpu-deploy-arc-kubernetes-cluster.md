---
title: Habilitar o arco do Azure no kubernetes no dispositivo de GPU pro do Azure Stack Edge | Microsoft Docs
description: Descreve como habilitar o arco do Azure em um cluster kubernetes existente em seu dispositivo Azure Stack Edge pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 1d42843805f4fce24368dd07de3a73fec2545957
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567518"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>Habilitar o arco do Azure no cluster kubernetes no dispositivo de GPU pro do Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo mostra como habilitar o arco do Azure em um cluster kubernetes existente em seu dispositivo Azure Stack Edge pro. 

Este procedimento destina-se a aqueles que revisaram as [cargas de trabalho do kubernetes no dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-workload-management.md) e estão familiarizados com os conceitos do [que é o Azure Arc habilitado kubernetes (versão prévia)?](../azure-arc/kubernetes/overview.md).


## <a name="prerequisites"></a>Pré-requisitos

Antes de habilitar o arco do Azure no cluster kubernetes, verifique se você concluiu os seguintes pré-requisitos em seu dispositivo Azure Stack Edge pro e o cliente que será usado para acessar o dispositivo:

### <a name="for-device"></a>Para dispositivo

1. Você tem credenciais de entrada para um dispositivo Azure Stack Edge pro de 1 nó.
    1. O dispositivo está ativado. Consulte [ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    1. O dispositivo tem a função de computação configurada por meio de portal do Azure e tem um cluster kubernetes. Consulte [Configurar computação](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Você tem acesso proprietário à assinatura. Você precisará desse acesso durante a etapa de atribuição de função para sua entidade de serviço.
 

### <a name="for-client-accessing-the-device"></a>Para cliente que acessa o dispositivo

1. Você tem um sistema cliente Windows que será usado para acessar o dispositivo Azure Stack Edge pro.
  
    - O cliente está executando o Windows PowerShell 5,0 ou posterior. Para baixar a versão mais recente do Windows PowerShell, acesse [instalar o Windows PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows).
    
    - Você também pode ter qualquer outro cliente com um [sistema operacional com suporte](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Este artigo descreve o procedimento ao usar um cliente do Windows. 
    
1. Você concluiu o procedimento descrito em [acessar o cluster kubernetes no dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Você:
    
    - Instalado `kubectl` no cliente.    
    - Verifique se a `kubectl` versão do cliente está distorcida sem mais de uma versão da versão mestre do kubernetes em execução no dispositivo pro Edge Azure Stack. 
      - Use `kubectl version` para verificar a versão do kubectl em execução no cliente. Anote a versão completa.
      - Na interface do usuário local do seu dispositivo Azure Stack Edge pro, vá para **atualização de software** e anote o número de versão do servidor kubernetes. 
    
        ![Verificar o número de versão do servidor kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - Verifique se essas duas versões são compatíveis. 


## <a name="register-kubernetes-resource-providers"></a>Registrar provedores de recursos kubernetes

Antes de habilitar o arco do Azure no cluster kubernetes, você precisará habilitar e registrar `Microsoft.Kubernetes` e `Microsoft.KubernetesConfiguration` em sua assinatura. 

1. Para habilitar um provedor de recursos, na portal do Azure, acesse a assinatura que você está planejando usar para a implantação. Vá para **provedores de recursos**. 
1. No painel direito, pesquise os provedores que você deseja adicionar. Neste exemplo, `Microsoft.Kubernetes` e `Microsoft.KubernetesConfiguration` .

    ![Registrar provedores de recursos kubernetes](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. Selecione um provedor de recursos e, na parte superior da barra de comandos, selecione **registrar**. O registro leva vários minutos. 

    ![Registrar provedores de recursos kubernetes 2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. Atualize a interface do usuário até ver que o provedor de recursos está registrado. Repita o processo para ambos os provedores de recursos.
    
    ![Registrar provedores de recursos do kubernetes 3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

Você também pode registrar provedores de recursos por meio do `az cli` . Para obter mais informações, consulte [registrar os dois provedores do Azure Arc habilitado kubernetes](../azure-arc/kubernetes/quickstart-connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>Criar entidade de serviço, atribuir função

1. Verifique se você tem `Subscription ID` e o nome do grupo de recursos usado para a implantação de recursos para seu serviço de Azure Stack Edge. Para obter a ID da assinatura, vá para o Azure Stack recurso do Edge no portal do Azure. Navegue até **visão geral > Essentials**.

    ![Obter ID da assinatura](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    Para obter o nome do grupo de recursos, vá para **Propriedades**.

    ![Obter nome do grupo de recursos](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. Para criar uma entidade de serviço, use o comando a seguir por meio do `az cli` .

    `az ad sp create-for-rbac --skip-assignment --name "<Informative name for service principal>"`  

    Para obter informações sobre como fazer logon no `az cli` , [inicie o Cloud Shell no portal do Azure](../cloud-shell/quickstart-powershell.md#start-cloud-shell)

    Veja um exemplo. 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. Anote o `appID` ,, e, `name` `password` `tenantID` como você usará como entrada no próximo comando.

1. Depois de criar a nova entidade de serviço, atribua a `Kubernetes Cluster - Azure Arc Onboarding` função à entidade recém-criada. Essa é uma função interna do Azure (use a ID de função no comando) com permissões limitadas. Use o seguinte comando:

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    Veja um exemplo.
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    Para obter mais informações sobre como criar uma entidade de serviço e executar a atribuição de função, consulte as etapas em [criar uma entidade de serviço de integração habilitada para Arc do Azure](../azure-arc/kubernetes/create-onboarding-service-principal.md).


## <a name="enable-arc-on-kubernetes-cluster"></a>Habilitar o Arc no cluster do Kubernetes

Siga estas etapas para configurar o cluster kubernetes para o gerenciamento de arco do Azure:

1. [Conecte-se à interface do PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) do seu dispositivo.

1. Tipo:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`


    > [!NOTE]
    > - Para implantar o arco do Azure em seu dispositivo, verifique se você está usando uma [região com suporte para o arco do Azure](../azure-arc/kubernetes/overview.md#supported-regions). 
    > - Use o `az account list-locations` comando para descobrir o nome exato do local a ser passado no `Set-HcsKubernetesAzureArcAgent` cmdlet. Os nomes de local normalmente são formatados sem espaços.
    
    Veja um exemplo:
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "westeurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    No portal do Azure, um recurso deve ser criado com o nome fornecido no comando anterior.

    ![Ir para o recurso de arco do Azure](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Para verificar se o arco do Azure está habilitado com êxito, execute o seguinte comando na interface do PowerShell:

    `kubectl get deployments -n azure-arc`

    Esse comando localiza todos os aplicativos implantados no `azure-arc` namespace correspondente ao arco do Azure.

    Aqui está um exemplo de saída que mostra os agentes Arc do Azure que foram implantados em seu cluster kubernetes no `azure-arc` namespace. 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    Você também pode obter uma lista de pods em execução no cluster kubernetes no `azure-arc` namespace. Um pod é um contêiner de aplicativo, ou processo, em execução no cluster kubernetes. 

    Use o seguinte comando:
    
    `kubectl get pods -n azure-arc`
    
    Aqui está um exemplo de saída.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


Como mostra a saída anterior, o kubernetes habilitado para Arc do Azure consiste em alguns agentes (operadores) que são executados no cluster implantados no `azure-arc` namespace.

- `config-agent`: observa o cluster conectado para os recursos de configuração de controle do código de origem aplicados no cluster e atualiza o estado de conformidade
- `controller-manager`: é um operador de operadores e orquestra interações entre os componentes do Azure Arc
- `metrics-agent`: coleta métricas de outros agentes do Arc para garantir que eles estejam apresentando um desempenho ideal
- `cluster-metadata-operator`: coleta metadados do cluster-versão do cluster, contagem de nós e versão do agente Arc do Azure
- `resource-sync-agent`: sincroniza os metadados de cluster mencionados acima para o Azure
- `clusteridentityoperator`: O kubernetes habilitado para Arc do Azure atualmente dá suporte à identidade atribuída pelo sistema. clusteridentityoperator mantém o certificado MSI (identidade de serviço gerenciado) usado por outros agentes para comunicação com o Azure.
- `flux-logs-agent`: coleta logs dos operadores de fluxo implantados como parte da configuração de controle do código-fonte.
- `connect-agent`: conversa com o recurso de arco do Azure.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Remover o arco do cluster kubernetes

Para remover o gerenciamento de arco do Azure, siga estas etapas:

1. 1. [Conecte-se à interface do PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) do seu dispositivo.
2. Tipo:

    `Remove-HcsKubernetesAzureArcAgent` 


> [!NOTE]
> Por padrão, quando o recurso `yamls` é excluído do repositório git, os recursos correspondentes não são excluídos do cluster kubernetes. Você precisa definir `--sync-garbage-collection`  em Arc OperatorParams para permitir a exclusão de recursos quando excluídos do repositório git. Para obter mais informações, consulte [excluir uma configuração](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters)

## <a name="next-steps"></a>Próximas etapas

Para entender como executar uma implantação do Azure Arc, consulte [implantar um aplicativo PHP sem estado `Guestbook` com o Redis via GitOps em um dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)