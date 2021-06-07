---
title: Como parar de monitorar o cluster kubernetes híbrido | Microsoft Docs
description: Este artigo descreve como você pode interromper o monitoramento de seu cluster kubernetes híbrido com insights de contêiner.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: e8708d6b860683cc96a806160ccc7c8e33949ab2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713687"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Como parar de monitorar o cluster híbrido

Depois de habilitar o monitoramento do cluster kubernetes, você poderá parar de monitorar o cluster com o contêiner insights se decidir que não deseja mais monitorá-lo. Este artigo mostra como fazer isso para os seguintes ambientes:

- Mecanismo de AKS no Azure e Azure Stack
- OpenShift versão 4 e superior
- Kubernetes habilitado para Azure Arc (versão prévia)

## <a name="how-to-stop-monitoring-using-helm"></a>Como interromper o monitoramento usando o Helm

As etapas a seguir se aplicam aos seguintes ambientes:

- Mecanismo de AKS no Azure e Azure Stack
- OpenShift versão 4 e superior

1. Para identificar primeiro a versão do gráfico do Helm insights do contêiner instalada no cluster, execute o seguinte comando Helm.

    ```
    helm list
    ```

    A saída será semelhante à seguinte:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *Azmon-contêineres-Release-1* representa a versão do gráfico de Helm para insights de contêiner.

2. Para excluir a versão do gráfico, execute o comando Helm a seguir.

    `helm delete <releaseName>`

    Exemplo:

    `helm delete azmon-containers-release-1`

    Isso removerá a versão do cluster. Você pode verificar executando o `helm list` comando:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

A alteração da configuração pode levar alguns minutos para ser concluída. Como o Helm rastreia suas versões mesmo depois de excluí-las, você pode auditar o histórico de um cluster e até mesmo cancelar a exclusão de uma versão com `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Como interromper o monitoramento em kubernetes habilitados para Arc

### <a name="using-powershell"></a>Usando o PowerShell

1. Baixe e salve o script em uma pasta local que configura o cluster com o complemento de monitoramento usando os seguintes comandos:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Configure a `$azureArcClusterResourceId` variável definindo os valores correspondentes para `subscriptionId` `resourceGroupName` e `clusterName` representando a ID de recurso do seu recurso de cluster kubernetes habilitado para arco do Azure.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configure a `$kubeContext` variável com o **Kube-Context** do cluster executando o comando `kubectl config get-contexts` . Se você quiser usar o contexto atual, defina o valor como `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Execute o comando a seguir para interromper o monitoramento do cluster.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>Usando a entidade de serviço
O script *disable-monitoring.ps1* usa o logon de dispositivo interativo. Se preferir um logon não interativo, você poderá usar uma entidade de serviço existente ou criar uma nova que tenha as permissões necessárias, conforme descrito em [pré-requisitos](container-insights-enable-arc-enabled-clusters.md#prerequisites). Para usar a entidade de serviço, você terá que passar $servicePrincipalClientId, $servicePrincipalClientSecret e $tenantId parâmetros com valores de entidade de serviço que você pretende usar para enable-monitoring.ps1 script.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Por exemplo:

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>Como usar o Bash

1. Baixe e salve o script em uma pasta local que configura o cluster com o complemento de monitoramento usando os seguintes comandos:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Configure a `azureArcClusterResourceId` variável definindo os valores correspondentes para `subscriptionId` `resourceGroupName` e `clusterName` representando a ID de recurso do seu recurso de cluster kubernetes habilitado para arco do Azure.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configure a `kubeContext` variável com o **Kube-Context** do cluster executando o comando `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Para interromper o monitoramento do cluster, há comandos diferentes fornecidos com base no seu cenário de implantação.

    Execute o comando a seguir para interromper o monitoramento do cluster usando o contexto atual.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Execute o comando a seguir para interromper o monitoramento do cluster especificando um contexto

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>Usando a entidade de serviço
O script bash *Disable-Monitoring.sh* usa o logon de dispositivo interativo. Se preferir um logon não interativo, você poderá usar uma entidade de serviço existente ou criar uma nova que tenha as permissões necessárias, conforme descrito em [pré-requisitos](container-insights-enable-arc-enabled-clusters.md#prerequisites). Para usar a entidade de serviço, você terá que passar--Client-ID,--cliente-Secret e--Tenant-ID valores da entidade de serviço que você pretende usar para *Enable-Monitoring.sh* o script bash.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Por exemplo:

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>Próximas etapas

Se o espaço de trabalho Log Analytics foi criado apenas para dar suporte ao monitoramento do cluster e ele não for mais necessário, você precisará excluí-lo manualmente. Se você não estiver familiarizado com como excluir um espaço de trabalho, consulte [excluir um espaço de trabalho do Azure log Analytics](../logs/delete-workspace.md).