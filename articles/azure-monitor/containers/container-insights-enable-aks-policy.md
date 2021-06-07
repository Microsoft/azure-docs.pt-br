---
title: Habilitar o complemento de monitoramento AKS usando o Azure Policy
description: Descreve como habilitar o complemento de monitoramento AKS usando a política personalizada do Azure.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713891"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Habilitar o complemento de monitoramento do AKS usando o Azure Policy
Este artigo descreve como habilitar o complemento de monitoramento AKS usando a política personalizada do Azure. A política personalizada de complemento de monitoramento pode ser atribuída na assinatura ou no escopo do grupo de recursos. Se o Azure Log Analytics Workspace e o cluster AKS estiverem em assinaturas diferentes, a identidade gerenciada usada pela atribuição de política precisará ter as permissões de função necessárias nas assinaturas ou menos no recurso do espaço de trabalho Log Analytics. Da mesma forma, se a política estiver no escopo do grupo de recursos, a identidade gerenciada deverá ter as permissões de função necessárias no espaço de trabalho Log Analytics se o espaço de trabalho não estiver no escopo do grupo de recursos selecionado.

O complemento de monitoramento requer as seguintes funções na identidade gerenciada usada pelo Azure Policy:

 - [Azure-kubernetes-Service-Contribute-role](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [log-Analytics-colaborador](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Criar e atribuir definição de política usando portal do Azure

### <a name="create-policy-definition"></a>Criar definição de política

1. Baixe a definição de política personalizada do Azure para habilitar o complemento de monitoramento AKS.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. Navegue até https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions e crie a definição de política com os detalhes a seguir na caixa de diálogo criação de definição de política.
 
    - **Local de definição**: escolha a assinatura do Azure onde a definição de política deve ser armazenada.
    - **Nome**: *(visualização) AKS-Monitoring-addon*
    - **Descrição**: *política personalizada do Azure para habilitar o complemento de monitoramento no (s) cluster (es) kubernetes do Azure no escopo especificado*
    - **Categoria**: escolha *usar existente* e escolha *kubernetes* na lista suspensa.
    - **Regra de política**: Remova as regras de exemplo existentes e copie o conteúdo de *azurepolicy.jsno* download na etapa #1 acima.

### <a name="assign-policy-definition-to-specified-scope"></a>Atribuir definição de política ao escopo especificado

> [!NOTE]
>  A identidade gerenciada será criada automaticamente e atribuída as funções especificadas na definição de política.

1. Selecione o complemento de monitoramento de AKS de definição de política *(visualização)* que você acabou de criar.
4. Clique em *atribuir** * e especifique um **escopo** de onde a política deve ser atribuída. 
5. Clique em **Avançar** e forneça a ID de recurso do espaço de trabalho log Analytics do Azure. A ID do recurso deve estar nesse formato `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` .
6. Crie uma tarefa de correção caso queira aplicar a política a clusters AKS existentes no escopo selecionado.
7. Clique na opção **revisar + criar** para criar a atribuição de política.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Criar e atribuir definição de política usando CLI do Azure

### <a name="create-policy-definition"></a>Criar definição de política

1. Baixe os arquivos de parâmetros e regras de definição de política personalizada do Azure com os seguintes comandos:

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Crie a definição de política com o seguinte comando:

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Atribuir definição de política ao escopo especificado

- Crie a atribuição de política com o seguinte comando:

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Azure Policy](../../governance/policy/overview.md).
- Saiba como a [segurança de correção funciona](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).
- Saiba mais sobre as [informações de contêiner](./container-insights-overview.md).
- Instale a [CLI do Azure](/cli/azure/install-azure-cli).