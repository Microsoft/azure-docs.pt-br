---
title: Configurar clusters kubernetes híbridos com insights de contêiner | Microsoft Docs
description: Este artigo descreve como você pode configurar o contêiner insights para monitorar clusters kubernetes hospedados em Azure Stack ou em outro ambiente.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d2692b4a634d60ef62339f68277591d711260712
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711239"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Configurar clusters kubernetes híbridos com insights de contêiner

As informações de contêiner fornecem uma experiência de monitoramento avançada para o AKS (serviço kubernetes do Azure) e o [mecanismo do AKS no Azure](https://github.com/Azure/aks-engine), que é um cluster kubernetes autogerenciado hospedado no Azure. Este artigo descreve como habilitar o monitoramento de clusters kubernetes hospedados fora do Azure e obter uma experiência de monitoramento semelhante.

## <a name="supported-configurations"></a>Configurações com suporte

As configurações a seguir são oficialmente suportadas com informações de contêiner. Se você tiver uma versão diferente do kubernetes e das versões do sistema operacional, envie um email para askcoin@microsoft.com .

- Sistemas

    - Kubernetes local
    - Mecanismo AKS no Azure e Azure Stack. Para obter mais informações, consulte [AKs Engine on Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versão 4 e superior, locais ou outros ambientes de nuvem.

- As versões do kubernetes e da política de suporte são as mesmas que as versões do [AKs com suporte](../../aks/supported-kubernetes-versions.md).

- Há suporte para os tempos de execução de contêiner a seguir: os tempos de execução compatíveis com o Docker, o Moby e o CRI, como O CRI-O e o contêiner.

- A versão do SO Linux para nós mestre e de trabalho com suporte são: Ubuntu (18, 4 LTS e 16, 4 LTS) e Red Hat Enterprise Linux CoreOS 43,81.

- Suporte ao controle de acesso: kubernetes RBAC e non-RBAC

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o seguinte:

- Um [workspace do Log Analytics](../logs/design-logs-deployment.md).

    O insights de contêiner dá suporte a um espaço de trabalho Log Analytics nas regiões listadas em produtos do Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Para criar seu próprio espaço de trabalho, ele pode ser criado por meio de [Azure Resource Manager](../logs/resource-manager-workspace.md), por meio do [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal do Azure](../logs/quick-create-workspace.md).

    >[!NOTE]
    >Não há suporte para a habilitação do monitoramento de vários clusters com o mesmo nome de cluster para o mesmo espaço de trabalho Log Analytics. Os nomes de cluster devem ser exclusivos.
    >

- Você é membro da **função colaborador de log Analytics** para habilitar o monitoramento de contêiner. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [gerenciar o acesso ao espaço de trabalho e aos dados de log](../logs/manage-access.md).

- Para exibir os dados de monitoramento, você precisa ter [*log Analytics função leitor*](../logs/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho log Analytics, configurada com o contêiner insights.

- [Helm Client](https://helm.sh/docs/using_helm/) para carregar o gráfico do contêiner insights para o cluster kubernetes especificado.

- As seguintes informações de configuração de proxy e firewall são necessárias para que a versão em contêiner do agente de Log Analytics para Linux se comunique com Azure Monitor:

    |Recurso de agente|Portas |
    |------|---------|
    |*.ods.opinsights.azure.com |Porta 443 |
    |*.oms.opinsights.azure.com |Porta 443 |
    |*. dc.services.visualstudio.com |Porta 443 |

- O agente em contêineres requer que o Kubelet `cAdvisor secure port: 10250` ou `unsecure port :10255` seja aberto em todos os nós no cluster para coletar métricas de desempenho. Recomendamos que você configure `secure port: 10250` o no cAdvisor do Kubelet se ele ainda não estiver configurado.

- O agente em contêiner requer que as seguintes variáveis de ambiente sejam especificadas no contêiner para se comunicar com o serviço de API kubernetes no cluster para coletar dados de inventário `KUBERNETES_SERVICE_HOST` e `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>A versão mínima do agente com suporte para monitorar clusters híbridos do kubernetes é ciprod10182019 ou posterior.

## <a name="enable-monitoring"></a>Habilitar o monitoramento

A habilitação de informações de contêiner para o cluster híbrido kubernetes consiste em executar as seguintes etapas na ordem.

1. Configure seu espaço de trabalho Log Analytics com a solução de informações de contêiner.   

2. Habilite o gráfico HELM do contêiner insights com o espaço de trabalho Log Analytics.

Para obter mais informações sobre soluções de monitoramento no Azure Monitor consulte [aqui](../../azure-monitor/insights/solutions.md).

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Como adicionar a solução de contêineres de Azure Monitor

Você pode implantar a solução com o modelo de Azure Resource Manager fornecido usando o cmdlet Azure PowerShell `New-AzResourceGroupDeployment` ou com CLI do Azure.

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:

- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.59 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para habilitar o monitoramento e o outro contém valores de parâmetro que você configura para especificar o seguinte:

- **workspaceResourceId** -a ID de recurso completa do seu espaço de trabalho log Analytics.
- **workspaceRegion** -a região em que o espaço de trabalho é criado, que também é conhecido como **local** nas propriedades do espaço de trabalho ao exibir da portal do Azure.

Para identificar primeiro a ID de recurso completo do seu espaço de trabalho Log Analytics necessário para o `workspaceResourceId` valor do parâmetro no **containerSolutionParams.jsno** arquivo, execute as etapas a seguir e execute o cmdlet do PowerShell ou o comando CLI do Azure para adicionar a solução.

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

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, localize o nome do espaço de trabalho e copie a ID de recurso completo do espaço de trabalho Log Analytics sob a **ID** do campo.

4. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Salve esse arquivo como containerSolution.jsem uma pasta local.

6. Cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Edite os valores de **workspaceResourceId** usando o valor que você copiou na etapa 3 e, para **workspaceRegion** , copie o valor da **região** depois de executar o comando de CLI do Azure [AZ monitor log-Analytics Workspace show](/cli/azure/monitor/log-analytics/workspace#az-monitor-log-analytics-workspace-list&preserve-view=true).

8. Salve esse arquivo como containerSolutionParams.jsem uma pasta local.

9. Você está pronto para implantar o modelo.

   - Para implantar com Azure PowerShell, use os seguintes comandos na pasta que contém o modelo:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Para implantar com o CLI do Azure, execute os seguintes comandos:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

## <a name="install-the-helm-chart"></a>Instalar o gráfico HELM

Nesta seção, você instalará o agente em contêineres para insights de contêiner. Antes de continuar, você precisa identificar a ID do espaço de trabalho necessária para o `omsagent.secret.wsid` parâmetro e a chave primária necessária para o `omsagent.secret.key` parâmetro. Você pode identificar essas informações executando as etapas a seguir e, em seguida, executar os comandos para instalar o agente usando o gráfico HELM.

1. Execute o seguinte comando para identificar a ID do espaço de trabalho:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    Na saída, localize o nome do espaço de trabalho sob o **nome** do campo e, em seguida, copie a ID do espaço de trabalho do log Analytics espaço de trabalho no campo **CustomerID**.

2. Execute o seguinte comando para identificar a chave primária para o espaço de trabalho:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    Na saída, localize a chave primária no campo **primarySharedKey** e, em seguida, copie o valor.

>[!NOTE]
>Os comandos a seguir são aplicáveis somente para a versão 2 do Helm. O uso do `--name` parâmetro não é aplicável com a versão 3 do Helm. 

>[!NOTE]
>Se o cluster kubernetes se comunicar por meio de um servidor proxy, configure o parâmetro `omsagent.proxy` com a URL do servidor proxy. Se o cluster não se comunicar por meio de um servidor proxy, você não precisará especificar esse parâmetro. Para obter mais informações, consulte [Configurar ponto de extremidade de proxy](#configure-proxy-endpoint) posteriormente neste artigo.

3. Adicione o repositório de gráficos do Azure à lista local executando o seguinte comando:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Instale o gráfico executando o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Se o espaço de trabalho Log Analytics estiver na 21Vianet do Azure na China, execute o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Se o workspace do Log Analytics estiver no Azure US Government, execute o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Habilitar o gráfico Helm usando o modelo de API

Você pode especificar um complemento no arquivo JSON de especificação de cluster do mecanismo AKS, também conhecido como modelo de API. Nesse complemento, forneça a versão codificada em base64 do `WorkspaceGUID` e `WorkspaceKey` do log Analytics espaço de trabalho em que os dados de monitoramento coletados são armazenados. Você pode encontrar o `WorkspaceGUID` e o `WorkspaceKey` usando as etapas 1 e 2 na seção anterior.

As definições de API com suporte para o cluster de Hub de Azure Stack podem ser encontradas neste exemplo- [kubernetes-container-monitoring_existing_workspace_id_and_key.jsem](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Especificamente, localize a propriedade **addons** em **kubernetesConfig**:

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Configurar uma coleta de dados do agente

Em estrela com a versão 1.0.0 do gráfico, as configurações de coleta de dados do agente são controladas por meio do ConfigMap. Consulte a documentação sobre as configurações de coleta de dados do agente [aqui](container-insights-agent-config.md).

Depois de ter implantado com êxito o gráfico, você pode revisar os dados para o cluster kubernetes híbrido em informações de contêiner do portal do Azure.  

>[!NOTE]
>A latência de ingestão é de cerca de cinco a dez minutos do agente para ser confirmada no espaço de trabalho Log Analytics do Azure. O status do cluster mostra o valor **sem dados** ou **desconhecido** até que todos os dados de monitoramento necessários estejam disponíveis no Azure monitor.

## <a name="configure-proxy-endpoint"></a>Configurar ponto de extremidade proxy

Começando com a versão de gráfico 2.7.1, o gráfico dará suporte à especificação do ponto de extremidade de proxy com o `omsagent.proxy` parâmetro de gráfico. Isso permite que ele se comunique por meio do servidor proxy. A comunicação entre o agente de informações do contêiner e o Azure Monitor pode ser um servidor proxy HTTP ou HTTPS, e a autenticação anônima e básica (nome de usuário/senha) têm suporte.

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

Por exemplo: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Se você especificar o protocolo como **http**, as solicitações HTTP serão criadas usando a conexão segura SSL/TLS. O servidor proxy deve dar suporte a protocolos SSL/TLS.

## <a name="troubleshooting"></a>Solução de problemas

Se você encontrar um erro ao tentar habilitar o monitoramento para o cluster kubernetes híbrido, copie o [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) de script do PowerShell e salve-o em uma pasta no computador. Esse script é fornecido para ajudar a detectar e corrigir os problemas encontrados. Os problemas projetados para detectar e tentar a correção do são os seguintes:

- O espaço de trabalho de Log Analytics especificado é válido
- O espaço de trabalho Log Analytics é configurado com a solução de informações de contêiner. Caso contrário, configure o espaço de trabalho.
- Os pods OmsAgent réplicaset estão em execução
- Os pods OmsAgent daemonset estão em execução
- O serviço de integridade do OmsAgent está em execução
- A ID e a chave do espaço de trabalho Log Analytics configuradas no agente em contêiner correspondem ao espaço de trabalho com o qual a percepção está configurada.
- Valide se todos os nós de trabalho do Linux têm o `kubernetes.io/role=agent` rótulo para agendar o Pod RS. Se ele não existir, adicione-o.
- Valide `cAdvisor secure port:10250` ou `unsecure port: 10255` é aberto em todos os nós no cluster.

Para executar com Azure PowerShell, use os seguintes comandos na pasta que contém o script:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Próximas etapas

Com o monitoramento habilitado para coletar a utilização de recursos e de integridade do seu cluster kubernetes híbrido e cargas de trabalho em execução neles, saiba [como usar](container-insights-analyze.md) o contêiner insights.