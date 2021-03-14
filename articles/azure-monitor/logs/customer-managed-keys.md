---
title: Chave do Azure Monitor gerenciada pelo cliente
description: Informações e etapas para configurar a chave gerenciada pelo cliente para criptografar dados em seus espaços de trabalho do Log Analytics usando uma chave de Azure Key Vault.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 9fdaf42f18c320bf841e710b7066451fca24eaae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030980"
---
# <a name="azure-monitor-customer-managed-key"></a>Chave do Azure Monitor gerenciada pelo cliente 

Os dados no Azure Monitor são criptografados com chaves gerenciadas pela Microsoft. Você pode usar sua própria chave de criptografia para proteger os dados e as consultas salvas em seus espaços de trabalho. Quando você especifica uma chave gerenciada pelo cliente, essa chave é usada para proteger e controlar o acesso aos dados e, uma vez configurada, todos os dados enviados aos seus espaços de trabalho são criptografados com sua chave de Azure Key Vault. Chaves gerenciadas pelo cliente oferecem maior flexibilidade para gerenciar controles de acesso.

Recomendamos revisar [Limitações e restrições](#limitationsandconstraints) abaixo antes da configuração.

## <a name="customer-managed-key-overview"></a>Visão geral da chave gerenciada pelo cliente

[A criptografia em repouso](../../security/fundamentals/encryption-atrest.md) é um requisito comum de privacidade e segurança nas organizações. Você pode permitir que o Azure gerencie completamente a criptografia em repouso, enquanto você tem várias opções para gerenciar de forma rigorosa as chaves de criptografia e criptografia.

Azure Monitor garante que todos os dados e consultas salvas sejam criptografadas em repouso usando chaves gerenciadas pela Microsoft (MMK). O Azure Monitor também fornece uma opção de criptografia usando sua própria chave armazenada em seu [Azure Key Vault](../../key-vault/general/overview.md), que fornece o controle para revogar o acesso aos seus dados a qualquer momento. Azure Monitor uso da criptografia é idêntico ao modo como a [criptografia de armazenamento do Azure](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) Opera.

A chave gerenciada pelo cliente é entregue em [clusters dedicados](./logs-dedicated-clusters.md) , fornecendo maior nível de proteção e controle. Os dados ingeridos em clusters dedicados estão sendo criptografados duas vezes — uma vez no nível de serviço usando chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente e uma vez no nível de infraestrutura usando dois algoritmos de criptografia diferentes e duas chaves diferentes. A [criptografia dupla](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) protege contra um cenário em que um dos algoritmos ou chaves de criptografia pode ser comprometido. Nesse caso, a camada adicional de criptografia continua a proteger seus dados. O cluster dedicado também permite que você proteja seus dados com o controle de [Lockbox](#customer-lockbox-preview) .

Os dados ingeridos nos últimos 14 dias também são mantidos no cache de acesso frequente (com suporte de SSD) para uma operação de mecanismo de consulta eficiente. Esses dados permanecem criptografados com chaves da Microsoft independentemente da configuração de chave gerenciada pelo cliente, mas seu controle sobre os dados SSD adere à [revogação de chave](#key-revocation). Estamos trabalhando para ter dados SSD criptografados com chave gerenciada pelo cliente no primeiro semestre de 2021.

Log Analytics clusters dedicados usam um modelo de [preços](./logs-dedicated-clusters.md#cluster-pricing-model) de reserva de capacidade a partir de 1000 GB/dia.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Como funciona a chave gerenciada pelo cliente no Azure Monitor

Azure Monitor usa identidade gerenciada para conceder acesso ao seu Azure Key Vault. A identidade do cluster de Log Analytics tem suporte no nível de cluster. Para permitir a proteção de chave gerenciada pelo cliente em vários espaços de trabalho, um novo Log Analytics recurso de *cluster* é executado como uma conexão de identidade intermediária entre seu Key Vault e seus espaços de trabalho do log Analytics. O armazenamento do cluster usa a identidade gerenciada que os \' s associados ao recurso de *cluster* para autenticar o Azure Key Vault via Azure Active Directory. 

Após a configuração da chave gerenciada pelo cliente, novos dados ingeridos em espaços de trabalho vinculados ao seu cluster dedicado são criptografados com sua chave. Você pode desvincular espaços de trabalho do cluster a qualquer momento. Os novos dados são ingeridos para Log Analytics armazenamento e criptografados com a chave da Microsoft, enquanto você pode consultar os dados novos e antigos diretamente.

> [!IMPORTANT]
> A capacidade de chave gerenciada pelo cliente é regional. Seus Azure Key Vault, cluster e espaços de trabalho vinculados Log Analytics devem estar na mesma região, mas podem estar em assinaturas diferentes.

![Visão geral da chave gerenciada pelo cliente](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Recurso de *cluster* do Log Analytics com identidade gerenciada com permissões para Key Vault -- A identidade é propagada para o armazenamento de cluster do Log Analytics dedicado subjacente
3. Cluster dedicado de Log Analytics
4. Espaços de trabalho vinculados ao recurso de *cluster* 

### <a name="encryption-keys-operation"></a>Operação de chaves de criptografia

Há três tipos de chaves envolvidas na criptografia de dados de Armazenamento:

- Chave de criptografia de chave de **Kek** (sua chave gerenciada pelo cliente)
- **AEK** - Chave de Criptografia da Conta
- **DEK** - Chave de Criptografia dos Dados

As seguintes regras se aplicam:

- As contas de armazenamento de cluster Log Analytics geram uma chave de criptografia exclusiva para cada conta de armazenamento, que é conhecida como AEK.
- O AEK é usado para derivar DEKs, que são as chaves que são usadas para criptografar cada bloco de dados gravados no disco.
- Quando você configura sua chave no Key Vault e faz referência a ela no cluster, o armazenamento do Azure envia solicitações ao seu Azure Key Vault para encapsular e desencapsular o AEK para executar as operações de criptografia e descriptografia de dados.
- Seu KEK nunca deixa seu Key Vault e, no caso de uma chave HSM, ele nunca deixa o hardware.
- O armazenamento do Azure usa a identidade gerenciada associada ao recurso de *cluster* para autenticar e acessar o Azure Key Vault via Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Customer-Managed etapas de provisionamento de chave

1. Criar o Azure Key Vault e armazenar a chave
1. Criando cluster
1. Conceder permissões ao Key Vault
1. Atualizando o cluster com os detalhes do identificador de chave
1. Vinculando espaços de trabalho Log Analytics

A configuração de chave gerenciada pelo cliente não tem suporte no portal do Azure atualmente e o provisionamento pode ser executado por meio do [PowerShell](/powershell/module/az.operationalinsights/), da [CLI](/cli/azure/monitor/log-analytics) ou de solicitações [REST](/rest/api/loganalytics/) .

### <a name="asynchronous-operations-and-status-check"></a>Operações assíncronas e verificação de status

Algumas das etapas de configuração são executadas de forma assíncrona porque não podem ser concluídas rapidamente. O `status` em resposta pode ser um dos seguintes: ' InProgress ', ' atualizando ', ' excluindo ', ' Succeeded ou ' Failed ' com código de erro.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="rest"></a>[REST](#tab/rest)

Ao usar REST, a resposta inicialmente retorna um código de status HTTP 202 (aceito) e um cabeçalho com a propriedade *Azure-AsyncOperation* :
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Você pode verificar o status da operação assíncrona enviando uma solicitação GET para o ponto de extremidade no cabeçalho *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>Armazenamento da chave de criptografia (KEK)

Crie ou use um Azure Key Vault que você já tem para gerar ou importe uma chave a ser usada para criptografia de dados. O Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso aos dados no Azure Monitor. Você pode verificar essa configuração em propriedades no seu Key Vault, a *Exclusão temporária* e a *Proteção contra limpeza* devem ser habilitadas.

![Configurações de exclusão temporária e proteção contra limpeza](media/customer-managed-keys/soft-purge-protection.png)

Essas configurações podem ser atualizadas no Key Vault por meio da CLI e do PowerShell:

- [Exclusão Reversível](../../key-vault/general/soft-delete-overview.md)
- A [proteção contra limpeza](../../key-vault/general/soft-delete-overview.md#purge-protection) protege contra a exclusão forçada do segredo/cofre mesmo após a exclusão temporária

## <a name="create-cluster"></a>Criar cluster

Os clusters dão suporte a dois [tipos de identidade gerenciados](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types): atribuídos pelo sistema e atribuídos pelo usuário, enquanto uma única identidade pode ser definida em um cluster, dependendo do seu cenário. 
- A identidade gerenciada atribuída pelo sistema é mais simples e gerada automaticamente com a criação do cluster quando a identidade `type` está definida como "*SystemAssigned*". Essa identidade pode ser usada posteriormente para conceder acesso de armazenamento à sua Key Vault para operações de encapsulamento e desencapsulamento. 
  
  Configurações de identidade no cluster para identidade gerenciada atribuída pelo sistema
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Se você quiser configurar a chave gerenciada pelo cliente na criação do cluster, você deverá ter uma identidade atribuída pelo usuário e uma chave concedidas em seu Key Vault antecipadamente e, em seguida, criar o cluster com estas configurações: Identity `type` como "*userassigned*", `UserAssignedIdentities` com a *ID de recurso* da sua identidade.

  Configurações de identidade no cluster para identidade gerenciada atribuída pelo usuário
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft. ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> Você não poderá usar a identidade gerenciada atribuída pelo usuário se seu Key Vault estiver em Private-Link (vNet). Você pode usar a identidade gerenciada atribuída pelo sistema nesse cenário.

Siga o artigo procedimento ilustrado no [clusters dedicados](./logs-dedicated-clusters.md#creating-a-cluster). 

## <a name="grant-key-vault-permissions"></a>Conceder permissões do Key Vault

Criar política de acesso no Key Vault para conceder permissões ao seu cluster. Essas permissões são usadas pelo armazenamento de Azure Monitor Underlay. Abra o Key Vault no portal do Azure e clique em *"políticas de acesso"* e, em seguida, em *"+ Adicionar política de acesso"* para criar uma política com estas configurações:

- Permissões de chave: selecione *' obter '*, *' quebrar chave '* e *' desencapsular chave '*.
- Selecionar entidade de segurança: dependendo do tipo de identidade usado no cluster (sistema ou identidade gerenciada atribuída pelo usuário), insira o nome do cluster ou a ID da entidade de segurança do cluster para a identidade gerenciada atribuída pelo sistema ou o nome de identidade gerenciada atribuído pelo usuário.

![conceder permissões do Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A permissão *Obter* é necessária para verificar se o Key Vault está configurado como recuperável para proteger sua chave e o acesso aos seus dados do Azure Monitor.

## <a name="update-cluster-with-key-identifier-details"></a>Atualizar cluster com detalhes do identificador de chave

Todas as operações no cluster exigem a `Microsoft.OperationalInsights/clusters/write` permissão Action. Essa permissão pode ser concedida por meio do proprietário ou colaborador que contém a `*/write` ação ou por meio da função colaborador de log Analytics que contém a `Microsoft.OperationalInsights/*` ação.

Esta etapa atualiza Azure Monitor armazenamento com a chave e a versão a serem usadas para criptografia de dados. Quando atualizado, sua nova chave está sendo usada para encapsular e desencapsular a chave de armazenamento (AEK).

Selecione a versão atual da sua chave em Azure Key Vault para obter os detalhes do identificador de chave.

![Conceder permissões do Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Atualize keyvaultproperties no cluster com detalhes do identificador de chave.

A operação é assíncrona e pode demorar um pouco para ser concluída.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Resposta**

Ela leva a propagação da chave alguns minutos para ser concluída. Você pode verificar o status de atualização de duas maneiras:
1. Copie o valor da URL de Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação GET no cluster e examine as propriedades *Keyvaultproperties* . A chave atualizada recentemente deve retornar na resposta.

Uma resposta à solicitação GET deve ser parecida com esta quando a atualização da chave for concluída: 202 (aceito) e o cabeçalho
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

---

## <a name="link-workspace-to-cluster"></a>Vincular espaço de trabalho ao cluster

> [!IMPORTANT]
> Esta etapa deve ser executada somente após a conclusão do provisionamento de cluster do Log Analytics. Se você vincular espaços de trabalho e ingerir dados antes do provisionamento, os dados ingeridos serão descartados e não poderão ser recuperados.

Você precisa ter permissões de ' gravação ' no seu espaço de trabalho e cluster para executar essa operação, que incluem `Microsoft.OperationalInsights/workspaces/write` e `Microsoft.OperationalInsights/clusters/write` .

Siga o artigo procedimento ilustrado no [clusters dedicados](./logs-dedicated-clusters.md#link-a-workspace-to-cluster).

## <a name="key-revocation"></a>Revogação de chave

> [!IMPORTANT]
> - A maneira recomendada para revogar o acesso aos seus dados é desabilitar a chave ou excluir a política de acesso em seu Key Vault.
> - Definir o cluster `identity` `type` como "None" também revoga o acesso aos seus dados, mas essa abordagem não é recomendada, pois não é possível reverter a revogação ao reafirmar o `identity` no cluster sem abrir a solicitação de suporte.

O armazenamento de cluster sempre respeitará as alterações nas permissões de chave em uma hora ou antes e o armazenamento ficará indisponível. Todos os novos dados ingeridos em espaços de trabalho vinculados ao cluster são ignorados e não são recuperáveis, os dados se tornam inacessíveis e as consultas nesses espaços de trabalho falham. Os dados anteriormente ingeridos permanecem no armazenamento, desde que seu cluster e seus espaços de trabalho não sejam excluídos. Os dados inacessíveis são administrados pela política de retenção de dados e serão limpos quando a retenção for alcançada. Os dados ingeridos nos últimos 14 dias também são mantidos no cache frequente (com suporte de SSD) para uma operação de mecanismo de consulta eficiente. Isso é excluído na operação de revogação de chave e torna-se inacessível.

O armazenamento do cluster verifica periodicamente sua Key Vault para tentar desencapsular a chave de criptografia e, uma vez acessada, a ingestão de dados e a consulta são retomadas dentro de 30 minutos.

## <a name="key-rotation"></a>Alteração de chaves

A rotação de chaves gerenciada pelo cliente requer uma atualização explícita para o cluster com a nova versão de chave no Azure Key Vault. [Atualize o cluster com os detalhes do identificador de chave](#update-cluster-with-key-identifier-details). Se você não atualizar a nova versão de chave no cluster, o armazenamento de cluster Log Analytics continuará usando sua chave anterior para criptografia. Se você desabilitar ou excluir sua chave antiga antes de atualizar a nova chave no cluster, você entrará no estado de [revogação de chave](#key-revocation) .

Todos os seus dados continuam acessíveis após a operação de revezamento de chaves, já que os dados são sempre criptografados com a AEK (Chave de Criptografia de Conta), embora a AEK seja agora criptografada com sua nova versão de KEK (Chave de Criptografia de Chave) no Key Vault.

## <a name="customer-managed-key-for-saved-queries"></a>Chave gerenciada pelo cliente para consultas salvas

A linguagem de consulta usada em Log Analytics é expressiva e pode conter informações confidenciais em comentários que você adiciona a consultas ou na sintaxe de consulta. Algumas organizações exigem que essas informações sejam mantidas protegidas na política de chave gerenciada pelo cliente e você precisa salvar suas consultas criptografadas com sua chave. Azure Monitor permite armazenar *pesquisas salvas* e consultas *de alertas de log* criptografadas com sua chave em sua própria conta de armazenamento quando conectada ao seu espaço de trabalho. 

> [!NOTE]
> Log Analytics consultas podem ser salvas em vários repositórios, dependendo do cenário usado. As consultas permanecem criptografadas com a chave da Microsoft (MMK) nos seguintes cenários, independentemente da configuração de chave gerenciada pelo cliente: pastas de trabalho no Azure Monitor, painéis do Azure, aplicativo lógico do Azure, Azure Notebooks e Runbooks de automação.

Quando você coloca seu próprio armazenamento (BYOS) e o vincula ao seu espaço de trabalho, o serviço carrega as consultas *salvas* e *alertas de log* para sua conta de armazenamento. Isso significa que você controla a conta de armazenamento e a [política de criptografia em repouso](../../storage/common/customer-managed-keys-overview.md) usando a mesma chave usada para criptografar dados no cluster log Analytics ou em uma chave diferente. No entanto, você será responsável pelos custos associados a essa conta de armazenamento. 

**Considerações antes de definir a chave gerenciada pelo cliente para consultas**
* Você precisa ter permissões de ' gravação ' no espaço de trabalho e na conta de armazenamento
* Certifique-se de criar sua conta de armazenamento na mesma região que o espaço de trabalho Log Analytics está localizado
* As *pesquisas salvas* no armazenamento são consideradas como artefatos de serviço e seu formato pode ser alterado
* As *pesquisas de gravações* existentes são removidas do seu espaço de trabalho. Copie e *salve as pesquisas* necessárias antes da configuração. Você pode exibir as *pesquisas salvas* usando o  [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* Não há suporte para o histórico de consulta e você não poderá ver as consultas que executou
* Você pode vincular uma única conta de armazenamento ao espaço de trabalho com a finalidade de salvar consultas, mas pode ser usado partir as *pesquisas salvas* e consultas de *alertas de log*
* Não há suporte para fixar no painel

**Configurar BYOS para consultas de pesquisas salvas**

Vincular uma conta de armazenamento para *consulta* ao seu espaço de trabalho-- *salvas* as consultas de pesquisas são salvas em sua conta de armazenamento. 

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Após a configuração, qualquer nova consulta de *pesquisa salva* será salva no armazenamento.

**Configurar o BYOS para consultas de alertas de log**

Vincular uma conta de armazenamento para *alertas* ao seu espaço de trabalho-as consultas de *alertas de log* são salvas em sua conta de armazenamento. 

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Após a configuração, qualquer consulta de alerta nova será salva no armazenamento.

## <a name="customer-lockbox-preview"></a>Sistema de Proteção de Dados do Cliente (versão prévia)

O Lockbox fornece a você o controle para aprovar ou rejeitar a solicitação de engenheiro da Microsoft para acessar seus dados durante uma solicitação de suporte.

No Azure Monitor, você tem esse controle sobre os dados em espaços de trabalho vinculados ao seu Log Analytics cluster dedicado. O controle de lockbox se aplica aos dados armazenados em um cluster Log Analytics dedicado, em que é mantido isolado nas contas de armazenamento do cluster em sua assinatura protegida por lockbox.  

Saiba mais sobre [sistema de proteção de dados do cliente para Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Operações de Customer-Managed chave

Customer-Managed chave é fornecida no cluster dedicado e essas operações são mencionadas no [artigo de cluster dedicado](./logs-dedicated-clusters.md#change-cluster-properties)

- Obter todos os clusters no grupo de recursos  
- Obter todos os clusters na assinatura
- Atualizar *reserva de capacidade* no cluster
- Atualizar *billtype* no cluster
- Desvincular um espaço de trabalho do cluster
- Excluir cluster

## <a name="limitations-and-constraints"></a>Limitações e restrições

- O número máximo de clusters por região e assinatura é 2

- O número máximo de espaços de trabalho que podem ser vinculados a um cluster é 1000

- Você pode vincular um espaço de trabalho ao cluster e, em seguida, desvinculá-lo. O número de operações de link de espaço de trabalho no espaço de trabalho específico é limitado a 2 em um período de 30 dias.

- A criptografia de chave gerenciada pelo cliente se aplica a dados recentemente ingeridos após o tempo de configuração. Os dados que foram ingeridos antes da configuração, permanecem criptografados com a chave da Microsoft. Você pode consultar dados ingeridos antes e depois da configuração de chave gerenciada pelo cliente sem problemas.

- O Azure Key Vault deve ser configurado como recuperável. Essas propriedades não são habilitadas por padrão e devem ser configuradas usando a CLI ou o PowerShell:<br>
  - [Exclusão Reversível](../../key-vault/general/soft-delete-overview.md)
  - A [proteção de limpeza](../../key-vault/general/soft-delete-overview.md#purge-protection) deve ser ativada para proteção contra a exclusão forçada do segredo/cofre mesmo após a exclusão reversível.

- A movimentação de cluster para outro grupo de recursos ou assinatura não tem suporte no momento.

- Seu Azure Key Vault, cluster e espaços de trabalho devem estar na mesma região e no mesmo locatário do Azure Active Directory (AD do Azure), mas podem estar em assinaturas diferentes.

- A Lockbox não está disponível atualmente na China. 

- A [criptografia dupla](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) é configurada automaticamente para clusters criados a partir de outubro de 2020 em regiões com suporte. Você pode verificar se o cluster está configurado para criptografia dupla enviando uma solicitação GET no cluster e observando que o `isDoubleEncryptionEnabled` valor é `true` para clusters com criptografia dupla habilitada. 
  - Se você criar um cluster e receber um erro "<Region-Name> não dá suporte à criptografia dupla para clusters.", você ainda poderá criar o cluster sem criptografia dupla adicionando `"properties": {"isDoubleEncryptionEnabled": false}` o corpo da solicitação REST.
  - A configuração de criptografia dupla não pode ser alterada após a criação do cluster.

  - Se o cluster estiver definido com identidade gerenciada atribuída pelo usuário, a configuração `UserAssignedIdentities` com `None` suspende o cluster e impede o acesso aos seus dados, mas você não pode reverter a revogação e ativar o cluster sem abrir a solicitação de suporte. Essa limitação não é aplicada à identidade gerenciada atribuída pelo sistema.

  - Você não poderá usar a chave gerenciada pelo cliente com identidade gerenciada atribuída pelo usuário se seu Key Vault estiver em Private-Link (vNet). Você pode usar a identidade gerenciada atribuída pelo sistema nesse cenário.

## <a name="troubleshooting"></a>Solução de problemas

- Comportamento com disponibilidade do Key Vault
  - Em operação normal -- O armazenamento faz caches de AEK por curtos períodos e volta para Key Vault para desencapsular periodicamente.
    
  - Erros de conexão transitórios -- O armazenamento trata erros transitórios (tempos limite, falhas de conexão, problemas de DNS), permitindo que as chaves permaneçam no cache um pouco mais tempo e isso supera os pequenos problemas de disponibilidade. As capacidades de consulta e ingestão continuam sem interrupção.
    
  - Site ativo -- a indisponibilidade de aproximadamente 30 minutos fará com que a conta de Armazenamento fique indisponível. A capacidade de consulta não está disponível e os dados ingeridos são armazenados em cache por várias horas usando a chave da Microsoft para evitar a perda de dados. Quando o acesso a Key Vault é restaurado, a consulta fica disponível e os dados temporários armazenados em cache são ingeridos no armazenamento de dados e criptografados com a chave gerenciada pelo cliente.

  - Taxa de acesso do Key Vault -- A frequência com que o Armazenamento do Azure Monitor acessa o Key Vault para operações de encapsulamento e desencapsulamento é entre 6 e 60 segundos.

- Se você atualizar o cluster enquanto o cluster estiver no estado de provisionamento ou atualização, a atualização falhará.

- Se você receber um erro de conflito ao criar um cluster, pode ser que você tenha excluído o cluster nos últimos 14 dias e esteja em um período de exclusão reversível. O nome do cluster permanece reservado durante o período de exclusão reversível e você não pode criar um novo cluster com esse nome. O nome é liberado após o período de exclusão reversível quando o cluster é excluído permanentemente.

- O link do espaço de trabalho para o cluster falhará se ele estiver vinculado a outro cluster.

- Se você criar um cluster e especificar o keyvaultproperties imediatamente, a operação poderá falhar, pois a política de acesso não poderá ser definida até que a identidade do sistema seja atribuída ao cluster.

- Se você atualizar o cluster existente com keyvaultproperties e a política de acesso à chave ' Get ' estiver ausente no Key Vault, a operação falhará.

- Se você não implantar o cluster, verifique se seus Azure Key Vault, cluster e espaços de trabalho vinculados Log Analytics estão na mesma região. Eles podem estar em assinaturas diferentes.

- Se você atualizar a versão de chave no Key Vault e não atualizar os novos detalhes do identificador de chave no cluster, o cluster de Log Analytics continuará usando sua chave anterior e seus dados ficarão inacessíveis. Atualize os novos detalhes do identificador de chave no cluster para retomar a ingestão de dados e a capacidade de consultar dados.

- Algumas operações são longas e podem demorar um pouco para serem concluídas--são elas: criação de cluster, atualização de chave de cluster e exclusão de cluster. Você pode verificar o status da operação de duas maneiras:
  1. ao usar o REST, copie o Azure-AsyncOperation valor da URL da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
  2. Enviar solicitação GET para cluster ou espaço de trabalho e observar a resposta. Por exemplo, espaço de trabalho desvinculado não terá o *clusterResourceId* em *recursos*.

- Mensagens de erro
  
  **Criação de cluster**
  -  400--o nome do cluster não é válido. O nome do cluster pode conter caracteres de a-z, A-Z, 0-9 e comprimento de 3-63.
  -  400--o corpo da solicitação é nulo ou está em formato inadequado.
  -  400--o nome do SKU é inválido. Defina o nome do SKU como capacityReservation.
  -  400--a capacidade foi fornecida, mas o SKU não é capacityReservation. Defina o nome do SKU como capacityReservation.
  -  400--capacidade ausente no SKU. Defina o valor de capacidade para 1000 ou superior em etapas de 100 (GB).
  -  400--a capacidade no SKU não está no intervalo. Deve ser no mínimo 1000 e até a capacidade máxima permitida, que está disponível em ' uso e custo estimado ' em seu espaço de trabalho.
  -  400--a capacidade está bloqueada por 30 dias. A redução da capacidade é permitida 30 dias após a atualização.
  -  400--nenhum SKU foi definido. Defina o nome do SKU como capacityReservation e o valor da capacidade como 1000 ou superior em etapas de 100 (GB).
  -  400--a identidade é nula ou está vazia. Defina a identidade com o tipo systemAssigned.
  -  400--keyvaultproperties está definido na criação. Atualize keyvaultproperties após a criação do cluster.
  -  400--a operação não pode ser executada agora. A operação assíncrona está em um estado diferente de Succeeded. O cluster deve concluir sua operação antes que qualquer operação de atualização seja executada.

  **Atualização do cluster**
  -  400--o cluster está em estado de exclusão. A operação assíncrona está em andamento. O cluster deve concluir sua operação antes que qualquer operação de atualização seja executada.
  -  400--keyvaultproperties não está vazio, mas tem um formato inválido. Consulte [atualização do identificador de chave](#update-cluster-with-key-identifier-details).
  -  400--falha ao validar a chave no Key Vault. Pode ser devido à falta de permissões ou quando a chave não existe. Verifique se você [definiu a política de acesso e chave](#grant-key-vault-permissions) no Key Vault.
  -  400--a chave não é recuperável. Key Vault deve ser definido como exclusão reversível e proteção de limpeza. Consulte a [documentação do Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400--a operação não pode ser executada agora. Aguarde a conclusão da operação assíncrona e tente novamente.
  -  400--o cluster está em estado de exclusão. Aguarde a conclusão da operação assíncrona e tente novamente.

  **Obtenção de cluster**
    -  404--cluster não encontrado, o cluster pode ter sido excluído. Se você tentar criar um cluster com esse nome e entrar em conflito, o cluster estará em exclusão reversível por 14 dias. Você pode contatar o suporte para recuperá-lo ou usar outro nome para criar um novo cluster. 

  **Exclusão de cluster**
    -  409--não é possível excluir um cluster no estado de provisionamento. Aguarde a conclusão da operação assíncrona e tente novamente.

  **Link do espaço de trabalho**
  -  404--espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi excluído.
  -  409--operação de desvinculação ou link do espaço de trabalho no processo.
  -  400--cluster não encontrado, o cluster especificado não existe ou foi excluído. Se você tentar criar um cluster com esse nome e entrar em conflito, o cluster estará em exclusão reversível por 14 dias. Você pode contatar o suporte para recuperá-lo.

  **Desvincular espaço de trabalho**
  -  404--espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi excluído.
  -  409--operação de desvinculação ou link do espaço de trabalho no processo.
## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [log Analytics cobrança de cluster dedicado](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- Saiba mais sobre o [design adequado de espaços de trabalho de log Analytics](./design-logs-deployment.md)