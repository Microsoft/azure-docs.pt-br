---
title: Azure Monitor os clusters dedicados
description: Os clientes que ingerirem mais de 1 TB um dia de dados de monitoramento poderão usar clusters dedicados em vez de compartilhados
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 1fbb8f82366ee961d10ce8a6bc098128bad6555a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041001"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor os clusters dedicados

Os Azure Monitor logs de clusters dedicados são uma opção de implantação que habilita recursos avançados para clientes do Azure Monitor logs. Os clientes com clusters dedicados podem escolher os espaços de trabalho a serem hospedados nesses clusters.

Os recursos que exigem clusters dedicados são:

- **[Chaves gerenciadas pelo cliente](../logs/customer-managed-keys.md)** – criptografe os dados do cluster usando chaves que são fornecidas e controladas pelo cliente.
- **[Lockbox](../logs/customer-managed-keys.md#customer-lockbox-preview)** -os clientes podem controlar as solicitações de acesso dos engenheiros de suporte da Microsoft para dados.
- A **[criptografia dupla](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** protege contra um cenário em que um dos algoritmos ou chaves de criptografia pode ser comprometido. Nesse caso, a camada adicional de criptografia continua a proteger seus dados.
- **[Vários espaços de trabalho](../logs/cross-workspace-query.md)** – se um cliente estiver usando mais de um espaço de trabalho para produção, poderá fazer sentido usar o cluster dedicado. As consultas entre espaços de trabalho serão executadas mais rapidamente se todos os espaços de trabalho estiverem no mesmo cluster. Também pode ser mais econômico usar o cluster dedicado, pois as camadas de reserva de capacidade atribuídas levam em conta toda a ingestão de cluster e se aplicam a todos os seus espaços de trabalho, mesmo que algumas delas sejam pequenas e não elegíveis para o desconto de reserva de capacidade.

Os clusters dedicados exigem que os clientes sejam confirmados usando uma capacidade de pelo menos 1 TB de ingestão de dados por dia. A migração para um cluster dedicado é simples. Não há perda de dados ou interrupção de serviço. 

## <a name="management"></a>Gerenciamento 

Os clusters dedicados são gerenciados por meio de um recurso do Azure que representa Azure Monitor clusters de log. Todas as operações são feitas neste recurso usando o PowerShell ou a API REST.

Depois que o cluster é criado, ele pode ser configurado e espaços de trabalho vinculados a ele. Quando um espaço de trabalho é vinculado a um cluster, novos dados enviados ao espaço de trabalho residem no cluster. Somente espaços de trabalho que estão na mesma região que o cluster podem ser vinculados ao cluster. Os espaços de trabalho podem não ser semelhantes a um cluster com algumas limitações. Mais detalhes sobre essas limitações estão incluídos neste artigo. 

Os dados ingeridos em clusters dedicados estão sendo criptografados duas vezes — uma vez no nível de serviço usando chaves gerenciadas pela Microsoft ou [chave gerenciada pelo cliente](../logs/customer-managed-keys.md)e uma vez no nível de infraestrutura usando dois algoritmos de criptografia diferentes e duas chaves diferentes. A [criptografia dupla](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) protege contra um cenário em que um dos algoritmos ou chaves de criptografia pode ser comprometido. Nesse caso, a camada adicional de criptografia continua a proteger seus dados. O cluster dedicado também permite que você proteja seus dados com o controle de [Lockbox](../logs/customer-managed-keys.md#customer-lockbox-preview) .

Todas as operações no nível do cluster exigem a `Microsoft.OperationalInsights/clusters/write` permissão Action no cluster. Essa permissão pode ser concedida por meio do proprietário ou colaborador que contém a `*/write` ação ou por meio da função colaborador de log Analytics que contém a `Microsoft.OperationalInsights/*` ação. Para obter mais informações sobre Log Analytics permissões, consulte [gerenciar o acesso a dados de log e espaços de trabalho no Azure monitor](./manage-access.md). 


## <a name="cluster-pricing-model"></a>Modelo de preços do cluster

Log Analytics clusters dedicados usam um modelo de preços de reserva de capacidade, que de pelo menos 1000 GB/dia. Qualquer uso acima do nível de reserva será cobrado com a taxa de Pagamento Conforme o Uso.  As informações de preços de reserva de capacidade estão disponíveis na [página de preços Azure monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

O nível de reserva de capacidade do cluster é configurado por meio de programação com Azure Resource Manager usando o `Capacity` parâmetro em `Sku` . A `Capacity` é especificada em unidades de GB e pode ter valores de 1.000 GB/dia ou mais em incrementos de 100 GB/dia.

Há dois modos de cobrança para uso em um cluster. Eles podem ser especificados pelo `billingType` parâmetro ao configurar o cluster. 

1. **Cluster**: nesse caso (que é o padrão), a cobrança de dados ingeridos é feita no nível do cluster. As quantidades de dados ingeridos de cada espaço de trabalho associado a um cluster são agregadas para calcular a fatura diária do cluster. 

2. **Espaços de trabalho**: os custos de reserva de capacidade para o cluster são atribuídos proporcionalmente aos espaços de trabalho no cluster (após a contabilização de alocações por nó da [central de segurança do Azure](../../security-center/index.yml) para cada espaço de trabalho).

Se o seu espaço de trabalho estiver usando o tipo de preço herdado por nó, quando ele estiver vinculado a um cluster, ele será cobrado com base nos dados ingeridos em relação à reserva de capacidade do cluster e não mais por nó. As alocações de dados por nó da central de segurança do Azure continuarão a ser aplicadas.

Mais detalhes são cobrados para Log Analytics clusters dedicados estão disponíveis [aqui]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters).

## <a name="asynchronous-operations-and-status-check"></a>Operações assíncronas e verificação de status

Algumas das etapas de configuração são executadas de forma assíncrona porque não podem ser concluídas rapidamente. O status em resposta Contains pode ser um dos seguintes: ' InProgress ', ' atualizando ', ' excluindo ', ' Succeeded ou ' Failed ', incluindo o código de erro. Ao usar REST, a resposta inicialmente retorna um código de status HTTP 202 (aceito) e o cabeçalho com a propriedade Azure-AsyncOperation:

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Você pode verificar o status da operação assíncrona enviando uma solicitação GET ao valor do cabeçalho Azure-AsyncOperation:

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

## <a name="creating-a-cluster"></a>Criar um cluster

Primeiro, você cria recursos de cluster para começar a criar um cluster dedicado.

As propriedades a seguir devem ser especificadas:

- **ClusterName**: usado para fins administrativos. Os usuários não são expostos a esse nome.
- **ResourceGroupName**: como para qualquer recurso do Azure, os clusters pertencem a um grupo de recursos. Recomendamos que você use um grupo de recursos de ti central porque os clusters geralmente são compartilhados por muitas equipes na organização. Para obter mais considerações de design, examine [criando sua implantação de logs de Azure monitor](../logs/design-logs-deployment.md)
- **Local**: um cluster está localizado em uma região específica do Azure. Somente os espaços de trabalho localizados nessa região podem ser vinculados a esse cluster.
- **SkuCapacity**: você deve especificar o nível de *reserva de capacidade* (SKU) ao criar um recurso de *cluster* . O nível de *reserva de capacidade* pode estar no intervalo de 1.000 gb a 3.000 GB por dia. Você pode atualizá-lo em etapas de 100 mais tarde, se necessário. Se você precisar de um nível de reserva de capacidade superior a 3.000 GB por dia, entre em contato conosco em LAIngestionRate@microsoft.com . Para obter mais informações sobre os custos de cluster, consulte [gerenciar custos para clusters de log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters)

Depois de criar o recurso de *cluster* , você pode editar propriedades adicionais, como *SKU*, * keyvaultproperties ou *billtype*. Veja mais detalhes abaixo.

Você pode ter até 2 clusters ativos por assinatura por região. Se o cluster for excluído, ele ainda estará reservado por 14 dias. Você pode ter até 4 clusters reservados por assinatura por região (ativa ou excluída recentemente).

> [!WARNING]
> A criação do cluster dispara a alocação de recursos e o provisionamento. Esta operação pode levar algumas horas para ser concluída. É recomendável executá-lo de forma assíncrona.

A conta de usuário que cria os clusters deve ter a permissão de criação de recurso do Azure padrão: `Microsoft.Resources/deployments/*` e a permissão `Microsoft.OperationalInsights/clusters/write` de gravação de cluster ao ter em suas atribuições de função essa ação específica ou `Microsoft.OperationalInsights/*` `*/write` .

### <a name="create"></a>Criar 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Chamar* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Resposta*

Deve ser 202 (aceito) e um cabeçalho.

### <a name="check-cluster-provisioning-status"></a>Verificar o status de provisionamento do cluster

O provisionamento do cluster de Log Analytics demora um pouco para ser concluído. Você pode verificar o estado de provisionamento de várias maneiras:

- Execute Get-AzOperationalInsightsCluster comando do PowerShell com o nome do grupo de recursos e verifique a propriedade ProvisioningState. O valor é *ProvisioningAccount* durante o provisionamento e com *êxito* quando concluído.
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Copie o valor da URL de Azure-AsyncOperation da resposta e siga a verificação de status de operações assíncronas.

- Envie uma solicitação GET no recurso de *cluster* e examine o valor *provisioningState*. O valor é *ProvisioningAccount* durante o provisionamento e com *êxito* quando concluído.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
   Authorization: Bearer <token>
   ```

   **Resposta**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

O GUID *PrincipalId* é gerado pelo serviço de identidade gerenciada para o recurso de *cluster* .

## <a name="link-a-workspace-to-cluster"></a>Vincular um espaço de trabalho ao cluster

Quando um espaço de trabalho é vinculado a um cluster dedicado, novos dados que são ingeridos no espaço de trabalho são roteados para o novo cluster enquanto os dados existentes permanecem no cluster existente. Se o cluster dedicado for criptografado usando chaves gerenciadas pelo cliente (CMK), somente os novos dados serão criptografados com a chave. O sistema está abstraindo essa diferença dos usuários e os usuários apenas consultam o espaço de trabalho como de costume enquanto o sistema executa consultas entre clusters no back-end.

Um cluster pode ser vinculado a até 100 espaços de trabalho. Os espaços de trabalho vinculados estão localizados na mesma região que o cluster. Para proteger o back-end do sistema e evitar a fragmentação de dados, um espaço de trabalho não pode ser vinculado a um cluster mais de duas vezes por mês.

Para executar a operação de vínculo, você precisa ter permissões de ' gravação ' para o espaço de trabalho e o recurso de *cluster* :

- No espaço de trabalho: *Microsoft. OperationalInsights/Workspaces/Write*
- No recurso de *cluster* : *Microsoft. OperationalInsights/clusters/Write*

Além dos aspectos de cobrança, o espaço de trabalho vinculado mantém suas próprias configurações, como o comprimento da retenção de dados.
O espaço de trabalho e o cluster podem estar em assinaturas diferentes. É possível que o espaço de trabalho e o cluster estejam em locatários diferentes se o Azure Lighthouse for usado para mapear ambos para um único locatário.

Como qualquer operação de cluster, a vinculação de um espaço de trabalho pode ser executada somente após a conclusão do provisionamento de cluster Log Analytics.

> [!WARNING]
> Vincular um espaço de trabalho a um cluster requer a sincronização de vários componentes de back-end e a garantia de hidratação de cache. Esta operação pode levar até duas horas para ser concluída. Recomendamos executá-lo de forma assíncrona.


**PowerShell**

Use o seguinte comando do PowerShell para vincular a um cluster:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Use a seguinte chamada REST para vincular a um cluster:

*Enviar*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Resposta*

202 (aceito) e cabeçalho.

### <a name="check-workspace-link-status"></a>Verificar status do link do espaço de trabalho
  
Se você usar chaves gerenciadas pelo cliente, os dados ingeridos serão armazenados criptografados com a chave gerenciada após a operação de associação, o que pode levar até 90 minutos para ser concluído. 

Você pode verificar o estado de associação do workspace de duas maneiras:

- Copie o valor da URL de Azure-AsyncOperation da resposta e siga a verificação de status de operações assíncronas.

- Execute a operação get no espaço de trabalho e observe se a propriedade *clusterResourceId* está presente na resposta em *recursos*.

**CLI**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST**

*Chamar*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

*Resposta*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="change-cluster-properties"></a>Alterar propriedades do cluster

Depois de criar o recurso de *cluster* e ele ser totalmente provisionado, você pode editar propriedades adicionais no nível de cluster usando o PowerShell ou a API REST. Além das propriedades que estão disponíveis durante a criação do cluster, as propriedades adicionais só podem ser definidas depois que o cluster tiver sido provisionado:

- **Keyvaultproperties** – atualiza a chave no Azure Key Vault. Consulte [Atualizar cluster com detalhes do identificador de chave](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details). Ele contém os seguintes parâmetros: *KeyVaultUri*, *KeyName*, *keyversion*. 
- **billtype** – a propriedade *billtype* determina a atribuição de cobrança para o recurso de *cluster* e seus dados:
  - **Cluster** (padrão)-os custos de reserva de capacidade para o cluster são atribuídos ao recurso de *cluster* .
  - **Espaços de trabalho** -os custos de reserva de capacidade para o cluster são atribuídos proporcionalmente aos espaços de trabalho no cluster, com o recurso de *cluster* sendo cobrado por parte do uso se o total de dados ingeridos para o dia estiver sob a reserva de capacidade. Consulte [log Analytics clusters dedicados](./manage-cost-storage.md#log-analytics-dedicated-clusters) para saber mais sobre o modelo de preços do cluster. 

> [!NOTE]
> Não há suporte para a propriedade *billtype* no PowerShell.

### <a name="get-all-clusters-in-resource-group"></a>Obter todos os clusters no grupo de recursos
  
**CLI**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST**

*Chamar*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*Resposta*
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

### <a name="get-all-clusters-in-subscription"></a>Obter todos os clusters na assinatura

**CLI**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST**

*Chamar*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*Resposta*
    
O mesmo que para ' clusters em um grupo de recursos ', mas no escopo da assinatura.



### <a name="update-capacity-reservation-in-cluster"></a>Atualizar reserva de capacidade no cluster

Quando o volume de dados para seus espaços de trabalho vinculados é alterado ao longo do tempo e você deseja atualizar o nível de reserva de capacidade adequadamente. A capacidade é especificada em unidades de GB e pode ter valores de 1000 GB/dia ou mais em incrementos de 100 GB/dia. Observe que você não precisa fornecer o corpo da solicitação REST completa, mas deve incluir a SKU.

**CLI**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST**

*Chamar*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>Atualizar billtype no cluster

A propriedade *billtype* determina a atribuição de cobrança para o cluster e seus dados:
- *cluster* (padrão) -- A cobrança é atribuída à assinatura que hospeda seu recurso de cluster
- *workspaces* -- A cobrança é atribuída às assinaturas que hospedam seus workspaces proporcionalmente

**REST**

*Chamar*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ```

### <a name="unlink-a-workspace-from-cluster"></a>Desvincular um espaço de trabalho do cluster

Você pode desvincular um espaço de trabalho de um cluster. Depois de desvincular um espaço de trabalho do cluster, os novos dados associados a esse espaço de trabalho não são enviados para o cluster dedicado. Além disso, a cobrança do espaço de trabalho não é mais feita por meio do cluster. Os dados antigos do espaço de trabalho desvinculado podem ser deixados no cluster. Se esses dados forem criptografados usando chaves gerenciadas pelo cliente (CMK), os segredos de Key Vault serão mantidos. O sistema é abstrair essa alteração de Log Analytics usuários. Os usuários podem apenas consultar o espaço de trabalho como de costume. O sistema executa consultas entre clusters no back-end, conforme necessário, sem nenhuma indicação para os usuários.  

> [!WARNING] 
> Há um limite de duas operações de vinculação para um espaço de trabalho específico em um mês. Reserve um tempo para considerar e planeje desvincular ações de forma adequada.

**CLI**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**PowerShell**

Use o seguinte comando do PowerShell para desvincular um espaço de trabalho do cluster:

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>Excluir cluster

Um recurso de cluster dedicado pode ser excluído. Você deve desvincular todos os espaços de trabalho do cluster antes de excluí-lo. Você precisa de permissões de “gravação” no recurso de *cluster* para executar essa operação. 

Depois que o recurso de cluster é excluído, o cluster físico entra em um processo de limpeza e exclusão. A exclusão de um cluster exclui todos os dados que foram armazenados no cluster. Os dados podem ser de espaços de trabalho que foram vinculados ao cluster no passado.

Um recurso de *cluster* que foi excluído nos últimos 14 dias está no estado de exclusão temporária e pode ser recuperado com seus dados. Como todos os espaços de trabalho foram desassociados do recurso de *cluster* com a exclusão de recursos de *cluster* , você precisa associar novamente os espaços de trabalho após a recuperação. A operação de recuperação não pode ser executada pelo usuário entre em contato com seu canal da Microsoft ou suporte para solicitações de recuperação.

Dentro dos 14 dias após a exclusão, o nome do recurso de cluster é reservado e não pode ser usado por outros recursos.

> [!WARNING] 
> Há um limite de três clusters por assinatura. Os clusters ativos e com exclusão reversível são contados como parte disso. Os clientes não devem criar procedimentos recorrentes que criem e excluam clusters. Ele tem um impacto significativo em Log Analytics sistemas de back-end.

**PowerShell**

Use o seguinte comando do PowerShell para excluir um cluster:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Use a seguinte chamada REST para excluir um cluster:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Resposta**

  200 OK

## <a name="limits-and-constraints"></a>Limites e restrições

- O número máximo de clusters ativos por região e assinatura é 2

- O número máximo de clusters reservados (ativos ou excluídos recentemente) por região e assinatura é 4 

- O máximo de espaços de trabalho vinculados ao cluster é 1000

- Você pode vincular um espaço de trabalho ao cluster e, em seguida, desvinculá-lo. O número de operações de link de espaço de trabalho no espaço de trabalho específico é limitado a 2 em um período de 30 dias.

- A movimentação de cluster para outro grupo de recursos ou assinatura não tem suporte no momento.

- A Lockbox não está disponível atualmente na China. 

- A [criptografia dupla](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) é configurada automaticamente para clusters criados a partir de outubro de 2020 em regiões com suporte. Você pode verificar se o cluster está configurado para criptografia dupla enviando uma solicitação GET no cluster e observando que o `isDoubleEncryptionEnabled` valor é `true` para clusters com criptografia dupla habilitada. 
  - Se você criar um cluster e receber um erro "<Region-Name> não dá suporte à criptografia dupla para clusters.", você ainda poderá criar o cluster sem criptografia dupla adicionando `"properties": {"isDoubleEncryptionEnabled": false}` o corpo da solicitação REST.
  - A configuração de criptografia dupla não pode ser alterada após a criação do cluster.

## <a name="troubleshooting"></a>Solução de problemas

- Se você receber um erro de conflito ao criar um cluster, pode ser que você tenha excluído o cluster nos últimos 14 dias e esteja em um estado de exclusão reversível. O nome do cluster permanece reservado durante o período de exclusão reversível e você não pode criar um novo cluster com esse nome. O nome é liberado após o período de exclusão reversível quando o cluster é excluído permanentemente.

- Se você atualizar o cluster enquanto o cluster estiver no estado de provisionamento ou atualização, a atualização falhará.

- Algumas operações são longas e podem demorar um pouco para serem concluídas--são elas: criação de cluster, atualização de chave de cluster e exclusão de cluster. Você pode verificar o status da operação de duas maneiras:
  - Ao usar o REST, copie o Azure-AsyncOperation valor da URL da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
  - Enviar solicitação GET para cluster ou espaço de trabalho e observar a resposta. Por exemplo, espaço de trabalho desvinculado não terá o *clusterResourceId* em *recursos*.

- O link do espaço de trabalho para o cluster falhará se ele estiver vinculado a outro cluster.

- Mensagens de erro
  
  Criação de cluster:
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

  Atualização do cluster
  -  400--o cluster está em estado de exclusão. A operação assíncrona está em andamento. O cluster deve concluir sua operação antes que qualquer operação de atualização seja executada.
  -  400--keyvaultproperties não está vazio, mas tem um formato inválido. Consulte [atualização do identificador de chave](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details).
  -  400--falha ao validar a chave no Key Vault. Pode ser devido à falta de permissões ou quando a chave não existe. Verifique se você [definiu a política de acesso e chave](../logs/customer-managed-keys.md#grant-key-vault-permissions) no Key Vault.
  -  400--a chave não é recuperável. Key Vault deve ser definido como exclusão reversível e proteção de limpeza. Consulte a [documentação do Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400--a operação não pode ser executada agora. Aguarde a conclusão da operação assíncrona e tente novamente.
  -  400--o cluster está em estado de exclusão. Aguarde a conclusão da operação assíncrona e tente novamente.

  Get do cluster:
    -  404--cluster não encontrado, o cluster pode ter sido excluído. Se você tentar criar um cluster com esse nome e entrar em conflito, o cluster estará em exclusão reversível por 14 dias. Você pode contatar o suporte para recuperá-lo ou usar outro nome para criar um novo cluster. 

  Exclusão de cluster
    -  409--não é possível excluir um cluster no estado de provisionamento. Aguarde a conclusão da operação assíncrona e tente novamente.

  Link do espaço de trabalho:
  -  404--espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi excluído.
  -  409--operação de desvinculação ou link do espaço de trabalho no processo.
  -  400--cluster não encontrado, o cluster especificado não existe ou foi excluído. Se você tentar criar um cluster com esse nome e entrar em conflito, o cluster estará em exclusão reversível por 14 dias. Você pode contatar o suporte para recuperá-lo.

  Desvincular espaço de trabalho:
  -  404--espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi excluído.
  -  409--operação de desvinculação ou link do espaço de trabalho no processo.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [log Analytics cobrança de cluster dedicado](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- Saiba mais sobre o [design adequado de espaços de trabalho de log Analytics](../logs/design-logs-deployment.md)