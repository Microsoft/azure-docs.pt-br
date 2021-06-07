---
title: Impor uma versão mínima necessária da TLS (segurança da camada de transporte) para solicitações de entrada
titleSuffix: Azure Storage
description: Configure uma conta de armazenamento para exigir uma versão mínima do protocolo TLS para clientes que fazem solicitações no armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 257cd8dce2a080203f116a6f0d5b7c7ebd6d13f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593169"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Impor uma versão mínima necessária da TLS (segurança da camada de transporte) para solicitações a uma conta de armazenamento

A comunicação entre um aplicativo cliente e uma conta de armazenamento do Azure é criptografada usando o protocolo TLS. O TLS é um protocolo criptográfico padrão que garante a privacidade e a integridade dos dados entre clientes e serviços pela Internet. Para obter mais informações sobre o TLS, consulte [segurança da camada de transporte](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Atualmente, o armazenamento do Azure dá suporte a três versões do protocolo TLS: 1,0, 1,1 e 1,2. O armazenamento do Azure usa o TLS 1,2 em pontos de extremidade HTTPS públicos, mas o TLS 1,0 e o TLS 1,1 ainda têm suporte para compatibilidade com versões anteriores.

Por padrão, as contas de armazenamento do Azure permitem que os clientes enviem e recebam dados com a versão mais antiga do TLS, TLS 1,0 e superior. Para impor medidas de segurança mais estritas, você pode configurar sua conta de armazenamento para exigir que os clientes enviem e recebam dados com uma versão mais recente do TLS. Se uma conta de armazenamento exigir uma versão mínima do TLS, todas as solicitações feitas com uma versão mais antiga falharão.

Este artigo descreve como usar uma estrutura de arrastar (detecção-correção-auditoria-governança) para gerenciar continuamente o TLS seguro para suas contas de armazenamento.

Para obter informações sobre como especificar uma versão específica do TLS ao enviar uma solicitação de um aplicativo cliente, consulte [configurar TLS (segurança de camada de transporte) para um aplicativo cliente](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Detectar a versão do TLS usada por aplicativos cliente

Ao impor uma versão mínima do TLS para sua conta de armazenamento, você corre o risco de rejeitar solicitações de clientes que estão enviando dados com uma versão mais antiga do TLS. Para entender como a configuração da versão mínima do TLS pode afetar os aplicativos cliente, a Microsoft recomenda que você habilite o registro em log para sua conta de armazenamento do Azure e analise os logs após um intervalo de tempo para detectar quais versões dos aplicativos cliente TLS estão usando.

Para registrar solicitações em sua conta de armazenamento do Azure e determinar a versão do TLS usada pelo cliente, você pode usar o log de armazenamento do Azure no Azure Monitor (visualização). Para obter mais informações, consulte [monitorar o armazenamento do Azure](../blobs/monitor-blob-storage.md).

O log de armazenamento do Azure no Azure Monitor dá suporte ao uso de consultas de log para analisar dados de log. Para consultar logs, você pode usar um espaço de trabalho de Log Analytics do Azure. Para saber mais sobre consultas de log, consulte [tutorial: introdução às consultas de log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md).

Para registrar dados do armazenamento do Azure com Azure Monitor e analisá-los com o Azure Log Analytics, você deve primeiro criar uma configuração de diagnóstico que indica quais tipos de solicitações e para quais serviços de armazenamento você deseja registrar dados. Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Essa visualização habilita logs para BLOBs (incluindo Azure Data Lake Storage Gen2), arquivos, filas e tabelas. Para criar uma configuração de diagnóstico no portal do Azure, siga estas etapas:

1. Crie um novo espaço de trabalho Log Analytics na assinatura que contém sua conta de armazenamento do Azure. Depois de configurar o log para sua conta de armazenamento, os logs estarão disponíveis no espaço de trabalho Log Analytics. Para obter mais informações, confira [Criar um workspace do Log Analytics no portal do Azure](../../azure-monitor/logs/quick-create-workspace.md).
1. Navegue até sua conta de armazenamento no portal do Azure.
1. Na seção monitoramento, selecione **configurações de diagnóstico (versão prévia)**.
1. Selecione o serviço de armazenamento do Azure para o qual você deseja registrar solicitações. Por exemplo, escolha **blob** para registrar solicitações ao armazenamento de BLOBs.
1. Selecione **Adicionar configuração de diagnóstico**.
1. Forneça um nome para a configuração de diagnóstico.
1. Em **detalhes da categoria**, na seção **log** , escolha quais tipos de solicitações registrar em log. Você pode registrar em log as solicitações de leitura, gravação e exclusão. Por exemplo, escolher **StorageRead** e **StorageWrite** registrará em log as solicitações de leitura e gravação para o serviço selecionado.
1. Em **detalhes de destino**, selecione **Enviar para log Analytics**. Selecione sua assinatura e o espaço de trabalho Log Analytics criado anteriormente, conforme mostrado na imagem a seguir.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Captura de tela mostrando como criar uma configuração de diagnóstico para solicitações de log":::

Depois de criar a configuração de diagnóstico, as solicitações para a conta de armazenamento são registradas subsequentemente de acordo com essa configuração. Para obter mais informações, consulte [criar configuração de diagnóstico para coletar logs de recursos e métricas no Azure](../../azure-monitor/essentials/diagnostic-settings.md).

Para obter uma referência dos campos disponíveis nos logs de armazenamento do Azure no Azure Monitor, consulte [logs de recursos (versão prévia)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Consultar solicitações registradas por versão de TLS

Os logs de armazenamento do Azure no Azure Monitor incluem a versão de TLS usada para enviar uma solicitação para uma conta de armazenamento. Use a propriedade **TlsVersion** para verificar a versão de TLS de uma solicitação registrada.

Para determinar quantas solicitações foram feitas no armazenamento de BLOBs com versões diferentes do TLS nos últimos sete dias, abra seu espaço de trabalho do Log Analytics. Em seguida, Cole a consulta a seguir em uma nova consulta de log e execute-a. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Os resultados mostram a contagem do número de solicitações feitas com cada versão do TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Captura de tela mostrando os resultados da consulta do log Analytics para retornar a versão do TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Consultar solicitações registradas por endereço IP do chamador e cabeçalho do agente do usuário

Os logs de armazenamento do Azure no Azure Monitor também incluem o endereço IP do chamador e o cabeçalho do agente do usuário para ajudá-lo a avaliar quais aplicativos cliente acessaram a conta de armazenamento. Você pode analisar esses valores para decidir se os aplicativos cliente devem ser atualizados para usar uma versão mais recente do TLS ou se é aceitável falha na solicitação do cliente se ele não for enviado com a versão mínima do TLS.

Para determinar quais clientes fizeram solicitações com uma versão do TLS anterior ao TLS 1,2 nos últimos sete dias, Cole a consulta a seguir em uma nova consulta de log e execute-a. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>Corrigir riscos de segurança com uma versão mínima do TLS

Quando você estiver confiante de que o tráfego de clientes que usam versões mais antigas do TLS é mínimo, ou que é aceitável a falha de solicitações feitas com uma versão mais antiga do TLS, você pode iniciar a imposição de uma versão mínima do TLS em sua conta de armazenamento. Exigir que os clientes usem uma versão mínima do TLS para fazer solicitações em uma conta de armazenamento faz parte de uma estratégia para minimizar os riscos de segurança para seus dados.

> [!IMPORTANT]
> Se você estiver usando um serviço que se conecta ao armazenamento do Azure, verifique se esse serviço está usando a versão apropriada do TLS para enviar solicitações ao armazenamento do Azure antes de definir a versão mínima necessária para uma conta de armazenamento.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>Configurar a versão mínima do TLS para uma conta de armazenamento

Para configurar a versão mínima do TLS para uma conta de armazenamento, defina a versão **MinimumTlsVersion** para a conta. Essa propriedade está disponível para todas as contas de armazenamento que são criadas com o modelo de implantação Azure Resource Manager. Para obter mais informações sobre o modelo de implantação Azure Resource Manager, consulte [visão geral da conta de armazenamento](storage-account-overview.md).

A propriedade **MinimumTlsVersion** não é definida por padrão e não retorna um valor até que você a defina explicitamente.  Se o valor da propriedade for **nulo**, a conta de armazenamento permitirá solicitações enviadas com o TLS versão 1,0 ou superior.

# <a name="portal"></a>[Portal](#tab/portal)

Quando você cria uma conta de armazenamento com o portal do Azure, a versão mínima do TLS é definida como 1,2 por padrão.

Para configurar a versão mínima do TLS para uma conta de armazenamento existente com o portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Selecione a definição de **configuração** .
1. Em **versão mínima do TLS**, use a lista suspensa para selecionar a versão mínima do TLS necessária para acessar os dados nessa conta de armazenamento, conforme mostrado na imagem a seguir.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Captura de tela mostrando como configurar a versão mínima do TLS no portal do Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para configurar a versão mínima do TLS para uma conta de armazenamento com o PowerShell, instale [Azure PowerShell versão 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) ou posterior. Em seguida, configure a propriedade **MinimumTLSVersion** para uma conta de armazenamento nova ou existente. Os valores válidos para **MinimumTlsVersion** são `TLS1_0` , `TLS1_1` e `TLS1_2` .

O exemplo a seguir cria uma conta de armazenamento e define o **MinimumTLSVersion** como TLS 1,1 e, em seguida, atualiza a conta e define o **MinimumTLSVersion** como TLS 1,2. O exemplo também recupera o valor da propriedade em cada caso. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar a versão mínima do TLS para uma conta de armazenamento com CLI do Azure, instale CLI do Azure versão 2.9.0 ou posterior. Para obter mais informações, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Em seguida, configure a propriedade **minimumTlsVersion** para uma conta de armazenamento nova ou existente. Os valores válidos para **minimumTlsVersion** são `TLS1_0` , `TLS1_1` e `TLS1_2` .

O exemplo a seguir cria uma conta de armazenamento e define o **minimumTLSVersion** como TLS 1,1. Em seguida, ele atualiza a conta e define a propriedade **minimumTLSVersion** como TLS 1,2. O exemplo também recupera o valor da propriedade em cada caso. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Modelo](#tab/template)

Para configurar a versão mínima do TLS para uma conta de armazenamento com um modelo, crie um modelo com a propriedade **MinimumTLSVersion** definida como `TLS1_0` , `TLS1_1` ou `TLS1_2` . As etapas a seguir descrevem como criar um modelo no portal do Azure.

1. Na portal do Azure, escolha **criar um recurso**.
1. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **Enter**.
1. Escolha **implantação de modelo (implantar usando modelos personalizados) (versão prévia)**, escolha **criar** e, em seguida, escolha **criar seu próprio modelo no editor**.
1. No editor de modelo, Cole o JSON a seguir para criar uma nova conta e definir a versão mínima do TLS como TLS 1,2. Lembre-se de substituir os espaços reservados entre parênteses pelos seus próprios valores.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Salve o modelo.
1. Especifique o parâmetro do grupo de recursos e escolha o botão **revisar + criar** para implantar o modelo e criar uma conta de armazenamento com a propriedade **MinimumTLSVersion** configurada.

---

> [!NOTE]
> Depois de atualizar a versão mínima do TLS para a conta de armazenamento, pode levar até 30 segundos antes que a alteração seja totalmente propagada.

Configurar a versão mínima do TLS requer a versão 2019-04-01 ou posterior do provedor de recursos de armazenamento do Azure. Para obter mais informações, consulte [API REST do provedor de recursos de armazenamento do Azure](/rest/api/storagerp/).

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>Verificar a versão mínima necessária do TLS para várias contas

Para verificar a versão mínima necessária do TLS em um conjunto de contas de armazenamento com o desempenho ideal, você pode usar o Gerenciador de grafo de recursos do Azure no portal do Azure. Para saber mais sobre como usar o Gerenciador de grafo de recursos, consulte [início rápido: executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure](../../governance/resource-graph/first-query-portal.md).

A execução da consulta a seguir no Gerenciador de grafo de recursos retorna uma lista de contas de armazenamento e exibe a versão mínima do TLS para cada conta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>Testar a versão mínima do TLS de um cliente

Para testar se a versão mínima necessária do TLS para uma conta de armazenamento proíbe chamadas feitas com uma versão mais antiga, você pode configurar um cliente para usar uma versão mais antiga do TLS. Para obter mais informações sobre como configurar um cliente para usar uma versão específica do TLS, consulte [configurar TLS (segurança da camada de transporte) para um aplicativo cliente](transport-layer-security-configure-client-version.md).

Quando um cliente acessa uma conta de armazenamento usando uma versão de TLS que não atende à versão mínima do TLS configurada para a conta, o armazenamento do Azure retorna o código de erro 400 erro (solicitação incorreta) e uma mensagem indicando que a versão do TLS usada não é permitida para fazer solicitações nessa conta de armazenamento.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Usar Azure Policy para auditar a conformidade

Se você tiver um grande número de contas de armazenamento, convém realizar uma auditoria para garantir que todas as contas sejam configuradas para a versão mínima do TLS que sua organização requer. Para auditar um conjunto de contas de armazenamento para sua conformidade, use Azure Policy. Azure Policy é um serviço que você pode usar para criar, atribuir e gerenciar políticas que aplicam regras aos recursos do Azure. Azure Policy ajuda a manter esses recursos em conformidade com seus padrões corporativos e contratos de nível de serviço. Para saber mais, confira [Visão geral do Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Criar uma política com um efeito de auditoria

Azure Policy dá suporte a efeitos que determinam o que acontece quando uma regra de política é avaliada em um recurso. O efeito de auditoria cria um aviso quando um recurso não está em conformidade, mas não interrompe a solicitação. Para obter mais informações sobre efeitos, consulte [entender Azure Policy efeitos](../../governance/policy/concepts/effects.md).

Para criar uma política com um efeito de auditoria para a versão mínima do TLS com o portal do Azure, siga estas etapas:

1. Na portal do Azure, navegue até o serviço de Azure Policy.
1. Na seção **criação** , selecione **definições**.
1. Selecione **Adicionar definição de política** para criar uma nova definição de política.
1. Para o campo **local de definição** , selecione o botão **mais** para especificar onde o recurso de política de auditoria está localizado.
1. Especifique um nome para a política. Opcionalmente, você pode especificar uma descrição e uma categoria.
1. Em **regra de política**, adicione a definição de política a seguir à seção **policyRule** .

    ```json
    {
      "policyRule": {
        "if": {
          "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
                "equals": "TLS1_2"
              }
            }
          ]
        },
        "then": {
          "effect": "audit"
        }
      }
    }
    ```

1. Salve a política.

### <a name="assign-the-policy"></a>Atribuir a política

Em seguida, atribua a política a um recurso. O escopo da política corresponde a esse recurso e a todos os recursos abaixo dele. Para obter mais informações sobre atribuição de política, consulte [Azure Policy estrutura de atribuição](../../governance/policy/concepts/assignment-structure.md).

Para atribuir a política com a portal do Azure, siga estas etapas:

1. Na portal do Azure, navegue até o serviço de Azure Policy.
1. Na seção **criação** , selecione **atribuições**.
1. Selecione **atribuir política** para criar uma nova atribuição de política.
1. Para o campo **escopo** , selecione o escopo da atribuição de política.
1. Para o campo **definição de política** , selecione o botão **mais** e, em seguida, selecione a política que você definiu na seção anterior na lista.
1. Forneça um nome para a atribuição de política. A descrição é opcional.
1. Deixe **imposição de política** definida como *habilitada*. Essa configuração não tem efeito sobre a política de auditoria.
1. Selecione **revisão + criar** para criar a atribuição.

### <a name="view-compliance-report"></a>Exibir relatório de conformidade

Depois de atribuir a política, você poderá exibir o relatório de conformidade. O relatório de conformidade de uma política de auditoria fornece informações sobre quais contas de armazenamento não estão em conformidade com a política. Para obter mais informações, consulte [obter dados de conformidade da política](../../governance/policy/how-to/get-compliance-data.md).

Pode levar vários minutos para que o relatório de conformidade fique disponível depois que a atribuição de política for criada.

Para exibir o relatório de conformidade no portal do Azure, siga estas etapas:

1. Na portal do Azure, navegue até o serviço de Azure Policy.
1. Selecione **conformidade**.
1. Filtre os resultados para o nome da atribuição de política que você criou na etapa anterior. O relatório mostra quantos recursos não estão em conformidade com a política.
1. Você pode fazer uma busca detalhada no relatório para obter detalhes adicionais, incluindo uma lista de contas de armazenamento que não estão em conformidade.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="Captura de tela mostrando o relatório de conformidade para a política de auditoria para a versão mínima do TLS":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Usar Azure Policy para impor a versão mínima do TLS

O Azure Policy dá suporte à governança de nuvem, garantindo que os recursos do Azure sigam os requisitos e padrões. Para impor um requisito mínimo de versão TLS para as contas de armazenamento em sua organização, você pode criar uma política que impede a criação de uma nova conta de armazenamento que defina o requisito mínimo de TLS para uma versão mais antiga do TLS do que a que é ditada pela política. Essa política também impedirá todas as alterações de configuração em uma conta existente se a configuração de versão mínima do TLS para essa conta não estiver em conformidade com a política.

A política de imposição usa o efeito de negação para evitar uma solicitação que criaria ou modificaria uma conta de armazenamento para que a versão mínima do TLS não respeite mais os padrões da sua organização. Para obter mais informações sobre efeitos, consulte [entender Azure Policy efeitos](../../governance/policy/concepts/effects.md).

Para criar uma política com um efeito de negação para uma versão mínima do TLS que seja inferior a TLS 1,2, siga as mesmas etapas descritas em [usar Azure Policy para auditar a conformidade](#use-azure-policy-to-audit-for-compliance), mas forneça o JSON a seguir na seção **policyRule** da definição de política:

```json
{
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "not": {
            "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
            "equals": "TLS1_2"
          }
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

Depois de criar a política com o efeito de negação e atribuí-la a um escopo, um usuário não poderá criar uma conta de armazenamento com uma versão de TLS mínima que seja mais antiga que 1,2. Nem um usuário pode fazer alterações de configuração em uma conta de armazenamento existente que atualmente exija uma versão mínima do TLS que seja mais antiga que 1,2. A tentativa de fazer isso resulta em um erro. A versão mínima necessária do TLS para a conta de armazenamento deve ser definida como 1,2 para prosseguir com a criação ou configuração da conta.

A imagem a seguir mostra o erro que ocorre se você tentar criar uma conta de armazenamento com a versão mínima do TLS definida como TLS 1,0 (o padrão para uma nova conta) quando uma política com um efeito de negação exigir que a versão mínima do TLS seja definida como TLS 1,2.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="Captura de tela mostrando o erro que ocorre ao criar uma conta de armazenamento em violação de política":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>Permissões necessárias para exigir uma versão mínima do TLS

Para definir a propriedade **MinimumTlsVersion** para a conta de armazenamento, um usuário deve ter permissões para criar e gerenciar contas de armazenamento. As funções do Azure RBAC (controle de acesso baseado em função) que fornecem essas permissões incluem o **Microsoft. Storage/storageAccounts/Write** ou **Microsoft. Storage/ \* storageAccounts/** Action. As funções internas com essa ação incluem:

- A função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager
- A função [colaborador](../../role-based-access-control/built-in-roles.md#contributor) de Azure Resource Manager
- A função de [colaborador da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Essas funções não fornecem acesso a dados em uma conta de armazenamento por meio do Azure Active Directory (Azure AD). No entanto, eles incluem a **ação Microsoft. Storage/storageAccounts/listkeys/**, que concede acesso às chaves de acesso da conta. Com essa permissão, um usuário pode usar as chaves de acesso da conta para acessar todos os dados em uma conta de armazenamento.

As atribuições de função devem ser delimitadas ao nível da conta de armazenamento ou superior para permitir que um usuário exija uma versão mínima do TLS para a conta de armazenamento. Para obter mais informações sobre o escopo da função, consulte [entender o escopo do RBAC do Azure](../../role-based-access-control/scope-overview.md).

Tenha cuidado para restringir a atribuição dessas funções somente aos que exigem a capacidade de criar uma conta de armazenamento ou atualizar suas propriedades. Use o princípio de privilégios mínimos para garantir que os usuários tenham as menores permissões necessárias para realizar suas tarefas. Para obter mais informações sobre como gerenciar o acesso com o RBAC do Azure, consulte [práticas recomendadas para o RBAC do Azure](../../role-based-access-control/best-practices.md).

> [!NOTE]
> O administrador de serviço de funções de administrador de assinatura clássica e Co-Administrator incluem o equivalente da função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager. A função de **proprietário** inclui todas as ações, de modo que um usuário com uma dessas funções administrativas também pode criar e gerenciar contas de armazenamento. Para obter mais informações, confira [Funções clássicas de administrador da assinatura, funções do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="network-considerations"></a>Considerações de rede

Quando um cliente envia uma solicitação para a conta de armazenamento, o cliente estabelece uma conexão com o ponto de extremidade público da conta de armazenamento primeiro, antes de processar qualquer solicitação. A configuração de versão mínima do TLS é verificada depois que a conexão é estabelecida. Se a solicitação usar uma versão anterior do TLS do que a especificada pela configuração, a conexão continuará a ser bem-sucedida, mas a solicitação eventualmente falhará. Para obter mais informações sobre pontos de extremidade públicos para o armazenamento do Azure, consulte [sintaxe de URI de recurso](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax).

## <a name="next-steps"></a>Próximas etapas

- [Configurar o protocolo TLS para um aplicativo cliente](transport-layer-security-configure-client-version.md)
- [Recomendações de segurança para o armazenamento de blobs](../blobs/security-recommendations.md)
