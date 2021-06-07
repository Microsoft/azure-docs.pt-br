---
title: Configurar o cluster kubernetes habilitado para Arc do Azure com insights de contêiner | Microsoft Docs
description: Este artigo descreve como configurar o monitoramento com informações de contêiner em clusters kubernetes habilitados para Arc do Azure.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 307f9d9928042410dc9b4443aba5c019c592980c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711290"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Habilitar o monitoramento do cluster do Kubernetes habilitado para Azure Arc

As informações de contêiner fornecem uma experiência de monitoramento avançada para os clusters do AKS (serviço kubernetes do Azure) e do mecanismo do AKS. Este artigo descreve como habilitar o monitoramento de seus clusters kubernetes hospedados fora do Azure que são habilitados com o Arc do Azure para obter uma experiência de monitoramento semelhante.

As informações de contêiner podem ser habilitadas para uma ou mais implantações existentes do kubernetes usando um script do PowerShell ou bash.

## <a name="supported-configurations"></a>Configurações com suporte

O contêiner insights dá suporte ao monitoramento do Azure Arc habilitado kubernetes (versão prévia), conforme descrito no artigo de [visão geral](container-insights-overview.md) , com exceção dos seguintes recursos:

- Dados dinâmicos (visualização)

O seguinte é oficialmente suportado com o contêiner insights:

- As versões do kubernetes e da política de suporte são as mesmas que as versões do [AKs com suporte](../../aks/supported-kubernetes-versions.md).

- Há suporte para os tempos de execução de contêiner a seguir: os tempos de execução compatíveis com o Docker, o Moby e o CRI, como O CRI-O e o contêiner.

- A versão do SO Linux para nós mestre e de trabalho com suporte é: Ubuntu (18, 4 LTS e 16, 4 LTS).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o seguinte:

- Um workspace do Log Analytics.

    O insights de contêiner dá suporte a um espaço de trabalho Log Analytics nas regiões listadas em produtos do Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Para criar seu próprio espaço de trabalho, ele pode ser criado por meio de [Azure Resource Manager](../logs/resource-manager-workspace.md), por meio do [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal do Azure](../logs/quick-create-workspace.md).

- Para habilitar e acessar os recursos em insights de contêiner, no mínimo você precisa ser um membro da função *colaborador* do Azure na assinatura do Azure e um membro da função colaborador de [*log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) do espaço de trabalho log Analytics configurado com informações de contêiner.

- Você é um membro da função [colaborador](../../role-based-access-control/built-in-roles.md#contributor) no recurso de cluster de arco do Azure.

- Para exibir os dados de monitoramento, você é um membro da permissão da função [*leitor de log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) com o espaço de trabalho log Analytics configurado com o contêiner insights.

- [Helm Client](https://helm.sh/docs/using_helm/) para carregar o gráfico do contêiner insights para o cluster kubernetes especificado.

- As seguintes informações de configuração de proxy e firewall são necessárias para que a versão em contêiner do agente de Log Analytics para Linux se comunique com Azure Monitor:

    |Recurso de agente|Portas |
    |------|---------|
    |`*.ods.opinsights.azure.com` |Porta 443 |
    |`*.oms.opinsights.azure.com` |Porta 443 |
    |`*.dc.services.visualstudio.com` |Porta 443 |

- O agente em contêineres requer que o Kubelet `cAdvisor secure port: 10250` ou `unsecure port :10255` seja aberto em todos os nós no cluster para coletar métricas de desempenho. Recomendamos que você configure `secure port: 10250` o no cAdvisor do Kubelet se ele ainda não estiver configurado.

- O agente em contêiner requer que as seguintes variáveis de ambiente sejam especificadas no contêiner para se comunicar com o serviço de API kubernetes no cluster para coletar dados de inventário `KUBERNETES_SERVICE_HOST` e `KUBERNETES_PORT_443_TCP_PORT` .

    >[!IMPORTANT]
    >A versão mínima do agente com suporte para monitorar clusters kubernetes habilitados para Arc é ciprod04162020 ou posterior.

- O [PowerShell Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) é necessário se você habilitar o monitoramento usando o método com script do PowerShell.

- A [versão 4 do bash](https://www.gnu.org/software/bash/) será necessária se você habilitar o monitoramento usando o método script bash.

## <a name="identify-workspace-resource-id"></a>Identificar ID de recurso do espaço de trabalho

Para habilitar o monitoramento do cluster usando o script do PowerShell ou bash baixado anteriormente e integrá-lo a um espaço de trabalho do Log Analytics existente, execute as seguintes etapas para primeiro identificar a ID do recurso completo do seu espaço de trabalho Log Analytics. Isso é necessário para o `workspaceResourceId` parâmetro quando você executa o comando para habilitar o complemento de monitoramento no espaço de trabalho especificado. Se você não tiver um espaço de trabalho para especificar, poderá ignorar a inclusão do `workspaceResourceId` parâmetro e permitir que o script crie um novo espaço de trabalho para você.

1. Liste todas as assinaturas às quais você tem acesso usando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída será semelhante à seguinte:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Copie o valor de **SubscriptionId**.

2. Alterne para a assinatura que hospeda o espaço de trabalho Log Analytics usando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. O exemplo a seguir exibe a lista de espaços de trabalho em suas assinaturas no formato JSON padrão.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, localize o nome do espaço de trabalho e copie a ID de recurso completo do espaço de trabalho Log Analytics sob a **ID** do campo.

## <a name="enable-monitoring-using-powershell"></a>Habilitar o monitoramento usando o PowerShell

1. Baixe e salve o script em uma pasta local que configura o cluster com o complemento de monitoramento usando os seguintes comandos:

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. Configure a `$azureArcClusterResourceId` variável definindo os valores correspondentes para `subscriptionId` `resourceGroupName` e `clusterName` representando a ID de recurso do seu recurso de cluster kubernetes habilitado para arco do Azure.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configure a `$kubeContext` variável com o **Kube-Context** do cluster executando o comando `kubectl config get-contexts` . Se você quiser usar o contexto atual, defina o valor como `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Se você quiser usar o Azure Monitor Log Analytics espaço de trabalho existente, configure a variável `$logAnalyticsWorkspaceResourceId` com o valor correspondente que representa a ID do recurso do espaço de trabalho. Caso contrário, defina a variável como `""` e o script criará um espaço de trabalho padrão no grupo de recursos padrão da assinatura do cluster, caso ainda não exista uma na região. O espaço de trabalho padrão criado é semelhante ao formato de *defaultworkspace- \<SubscriptionID> - \<Region>*.

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Se o cluster kubernetes habilitado para Arc se comunicar por meio de um servidor proxy, configure a variável `$proxyEndpoint` com a URL do servidor proxy. Se o cluster não se comunicar por meio de um servidor proxy, você poderá definir o valor como `""` .  Para obter mais informações, consulte [Configurar ponto de extremidade de proxy](#configure-proxy-endpoint) posteriormente neste artigo.

6. Execute o comando a seguir para habilitar o monitoramento.

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

### <a name="using-service-principal"></a>Usando a entidade de serviço
O script *enable-monitoring.ps1* usa o logon de dispositivo interativo. Se preferir um logon não interativo, você poderá usar uma entidade de serviço existente ou criar uma nova que tenha as permissões necessárias, conforme descrito em [pré-requisitos](#prerequisites). Para usar a entidade de serviço, você terá que passar $servicePrincipalClientId, $servicePrincipalClientSecret e $tenantId parâmetros com valores de entidade de serviço que você pretende usar para *enable-monitoring.ps1* script.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

A atribuição de função abaixo só será aplicável se você estiver usando o espaço de trabalho Log Analytics existente em uma assinatura diferente do Azure do que o recurso de cluster K8s conectado do Arc.

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Por exemplo:

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Habilitar o uso do script bash

Execute as etapas a seguir para habilitar o monitoramento usando o script bash fornecido.

1. Baixe e salve o script em uma pasta local que configura o cluster com o complemento de monitoramento usando os seguintes comandos:

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. Configure a `azureArcClusterResourceId` variável definindo os valores correspondentes para `subscriptionId` `resourceGroupName` e `clusterName` representando a ID de recurso do seu recurso de cluster kubernetes habilitado para arco do Azure.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configure a `kubeContext` variável com o **Kube-Context** do cluster executando o comando `kubectl config get-contexts` . Se você quiser usar o contexto atual, defina o valor como `""` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Se você quiser usar o Azure Monitor Log Analytics espaço de trabalho existente, configure a variável `logAnalyticsWorkspaceResourceId` com o valor correspondente que representa a ID do recurso do espaço de trabalho. Caso contrário, defina a variável como `""` e o script criará um espaço de trabalho padrão no grupo de recursos padrão da assinatura do cluster, caso ainda não exista uma na região. O espaço de trabalho padrão criado é semelhante ao formato de *defaultworkspace- \<SubscriptionID> - \<Region>*.

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Se o cluster kubernetes habilitado para Arc se comunicar por meio de um servidor proxy, configure a variável `proxyEndpoint` com a URL do servidor proxy. Se o cluster não se comunicar por meio de um servidor proxy, você poderá definir o valor como `""` . Para obter mais informações, consulte [Configurar ponto de extremidade de proxy](#configure-proxy-endpoint) posteriormente neste artigo.

6. Para habilitar o monitoramento em seu cluster, há comandos diferentes fornecidos com base em seu cenário de implantação.

    Execute o comando a seguir para habilitar o monitoramento com opções padrão, como usar o Kube atual, criar um espaço de trabalho de Log Analytics padrão e sem especificar um servidor proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Execute o seguinte comando para criar um espaço de trabalho de Log Analytics padrão e sem especificar um servidor proxy:

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    Execute o seguinte comando para usar um espaço de trabalho de Log Analytics existente e sem especificar um servidor proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    Execute o seguinte comando para usar um espaço de trabalho Log Analytics existente e especificar um servidor proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

### <a name="using-service-principal"></a>Usando a entidade de serviço
O script bash *Enable-Monitoring.sh* usa o logon de dispositivo interativo. Se preferir um logon não interativo, você poderá usar uma entidade de serviço existente ou criar uma nova que tenha as permissões necessárias, conforme descrito em [pré-requisitos](#prerequisites). Para usar a entidade de serviço, você terá que passar--Client-ID,--cliente-Secret e--Tenant-ID valores da entidade de serviço que você pretende usar para *Enable-Monitoring.sh* o script bash.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

A atribuição de função abaixo só será aplicável se você estiver usando o espaço de trabalho Log Analytics existente em uma assinatura diferente do Azure do que o recurso de cluster K8s conectado do Arc.

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Por exemplo:

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>Configurar ponto de extremidade proxy

Com o agente em contêiner para informações de contêiner, você pode configurar um ponto de extremidade de proxy para permitir que ele se comunique por meio do servidor proxy. A comunicação entre o agente em contêiner e o Azure Monitor pode ser um servidor proxy HTTP ou HTTPS e a autenticação anônima e básica (nome de usuário/senha) têm suporte.

O valor de configuração de proxy tem a seguinte sintaxe: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Se o servidor proxy não exigir autenticação, você ainda precisará especificar um nome de usuário/senha do psuedo. Isso pode ser qualquer nome de usuário ou senha.

|Propriedade| Descrição |
|--------|-------------|
|Protocolo | http ou https |
|usuário | Nome de usuário opcional para autenticação de proxy |
|password | Senha opcional para autenticação de proxy |
|proxyhost | Endereço ou FQDN do servidor proxy |
|porta | Número da porta opcional para o servidor proxy |

Por exemplo: `http://user01:password@proxy01.contoso.com:3128`

Se você especificar o protocolo como **http**, as solicitações HTTP serão criadas usando a conexão segura SSL/TLS. O servidor proxy deve dar suporte a protocolos SSL/TLS.

### <a name="configure-using-powershell"></a>Configurar usando o PowerShell

Especifique o nome de usuário e a senha, o endereço IP ou o FQDN e o número da porta para o servidor proxy. Por exemplo:

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Configurar usando bash

Especifique o nome de usuário e a senha, o endereço IP ou o FQDN e o número da porta para o servidor proxy. Por exemplo:

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>Próximas etapas

- Com o monitoramento habilitado para coletar a utilização de recursos e de integridade de seu cluster kubernetes habilitado para Arc e cargas de trabalho em execução neles, saiba [como usar o](container-insights-analyze.md) contêiner insights.

- Por padrão, o agente em contêiner coleta os logs de contêiner stdout/stderr de todos os contêineres em execução em todos os namespaces, exceto Kube-System. Para configurar a coleta de log de contêiner específica para namespaces ou namespaces específicos, examine [configuração do agente do insights de contêiner](container-insights-agent-config.md) para definir as configurações de coleta de dados desejadas para o arquivo de configurações do ConfigMap.

- Para revisar e analisar as métricas de Prometheus do seu cluster, examine [Configurar a recorte de métricas do Prometheus](container-insights-prometheus-integration.md)

- Para saber como parar de monitorar o cluster kubernetes habilitado para Arc com o contêiner insights, consulte [como parar de monitorar o cluster híbrido](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes).