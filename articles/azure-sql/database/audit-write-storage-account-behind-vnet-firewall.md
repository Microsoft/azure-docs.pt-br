---
title: Auditoria para conta de armazenamento atrás de rede virtual ou firewall
description: Configuração da auditoria para gravar eventos de banco de dados em uma conta de armazenamento atrás da rede virtual e do firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: 908c9f1d05c83eaa58f77b79a32d956898c35076
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348246"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Gravação de auditoria para conta de armazenamento atrás de rede virtual ou firewall
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


A auditoria para [Banco de Dados SQL do Azure](sql-database-paas-overview.md) e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) dá suporte à gravação de eventos de bancos de dados em uma [Conta de Armazenamento do Azure](../../storage/common/storage-account-overview.md) atrás de uma rede virtual e do firewall.

Este artigo explica duas maneiras de configurar o banco de dados SQL do Azure e a conta de armazenamento do Azure para essa opção. A primeira usa o portal do Azure, a segundo usa REST.

## <a name="background"></a>Segundo plano

A [Rede Virtual do Azure (VNet)](../../virtual-network/virtual-networks-overview.md) é o bloco de construção fundamental de sua rede privada no Azure. Ela permite vários tipos de recursos do Azure, como VMs (Máquinas Virtuais) do Azure, a fim de se comunicar de forma segura com a Internet, com as redes locais e com outras VMs. A VNet é semelhante a uma rede tradicional em seu próprio data center, mas traz benefícios adicionais da infraestrutura do Azure, como escala, disponibilidade e isolamento.

Para saber mais sobre os conceitos de VNet, práticas recomendadas e muito mais, veja [O que é a Rede Virtual do Azure](../../virtual-network/virtual-networks-overview.md).

Para saber mais sobre como criar uma rede virtual, veja o [Guia de Início Rápido: Criação de uma rede virtual usando o portal do Azure](../../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para que a auditoria grave em uma conta de armazenamento atrás de uma VNet ou um firewall, os seguintes pré-requisitos são necessários:

> [!div class="checklist"]
>
> * Uma conta de armazenamento de uso geral v2. Se tiver uma conta de armazenamento de uso geral v1 ou de armazenamento de blobs, [atualize para uma conta de armazenamento de uso geral v2](../../storage/common/storage-account-upgrade.md). Para obter mais informações, veja [Tipos de contas de armazenamento](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * A conta de armazenamento deve estar na mesma assinatura e no mesmo local que o [SQL Server lógico](logical-servers.md).
> * A conta de Armazenamento do Azure requer `Allow trusted Microsoft services to access this storage account`. Defina isso nos **Firewall e Redes Virtuais** da Conta de Armazenamento.
> * Você deve ter a permissão `Microsoft.Authorization/roleAssignments/write` na conta de armazenamento selecionada. Para obter mais informações, veja [Funções internas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Configurar no portal do Azure

Conecte-se ao [portal do Azure](https://portal.azure.com) com a sua assinatura. Navegue até o grupo de recursos e o servidor.

1. Clique em **Auditoria** no cabeçalho de segurança. Selecione **Ativada**.

2. Selecione **Armazenamento**. Selecione a conta de armazenamento onde os logs serão salvos. A conta de armazenamento deve estar em conformidade com os requisitos listados em [Pré-requisitos](#prerequisites).

3. Abra **Detalhes de Armazenamento**

  > [!NOTE]
  > Se a conta de Armazenamento selecionada estiver atrás da VNet, você verá a seguinte mensagem:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Se você não vir essa mensagem, a conta de armazenamento não está atrás de uma VNet.

4. Selecione o número de dias para o período de retenção. Em seguida, clique em **OK**. Os logs anteriores ao período de retenção são excluídos.

5. Selecione **Salvar** nas configurações de auditoria.

Você configurou com êxito a auditoria para gravar em uma conta de armazenamento atrás de uma VNet ou um firewall.

## <a name="configure-with-rest-commands"></a>Configuração com comandos REST

Como alternativa ao uso do portal do Azure, você pode usar comandos REST para configurar a auditoria para gravar eventos de banco de dados em uma conta de armazenamento atrás de uma VNet e um firewall.

Os scripts de exemplo nesta seção exigem que você atualize o script antes de executá-los. Substitua os seguintes valores nos scripts:

|Valor de exemplo|Descrição do exemplo|
|:-----|:-----|
|`<subscriptionId>`| ID de assinatura do Azure|
|`<resource group>`| Resource group|
|`<logical SQL Server>`| Nome do servidor|
|`<administrator login>`| Conta de administrador |
|`<complex password>`| Senha complexa para a conta do administrador|

Para configurar a Auditoria do SQL para gravar eventos em uma conta de armazenamento atrás de uma VNet ou um firewall:

1. Registre seu servidor com o Azure Active Directory (AD do Azure). Use o PowerShell ou a API REST.

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**API REST**](/rest/api/sql/servers/createorupdate):

   Solicitação de exemplo

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Corpo da solicitação

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
     }
   }
   ```

2. Abra o [portal do Azure](https://portal.azure.com). Navegue até sua conta de armazenamento. Selecione **Controle de Acesso (IAM)** e clique em **Adicionar atribuição de função**. Atribua a função de **colaborador de dados do armazenamento de BLOBs** do Azure ao servidor que hospeda o banco de dado que você registrou com o Azure Active Directory (Azure AD) como na etapa anterior.

   > [!NOTE]
   > Somente membros com o privilégio Proprietário podem executar essa etapa. Para várias funções internas do Azure, consulte [funções internas do Azure](../../role-based-access-control/built-in-roles.md).

3. Configure a [política de auditoria de blob do servidor](/rest/api/sql/server%20auditing%20settings/createorupdate), sem especificar um *storageAccountAccessKey* :

   Solicitação de exemplo

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
   ```

   Corpo da solicitação

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

- [Criação ou Atualização da Política de Auditoria de Banco de Dados (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria do servidor (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager

Você pode configurar a auditoria para gravar eventos de banco de dados em uma conta de armazenamento por trás da rede virtual e do firewall usando o modelo [Azure Resource Manager](../../azure-resource-manager/management/overview.md) , conforme mostrado no exemplo a seguir:

> [!IMPORTANT]
> Para usar a conta de armazenamento por trás da rede virtual e do firewall, você precisa definir o parâmetro **isStorageBehindVnet** como true

- [Implantar um SQL Server do Azure com auditoria habilitada para gravar logs de auditoria em um armazenamento de BLOBs](https://azure.microsoft.com/resources/templates/201-sql-auditing-server-policy-to-blob-storage)

> [!NOTE]
> O exemplo vinculado está em um repositório público externo e é fornecido ' no estado em que se encontra ', sem garantia e não tem suporte em nenhum programa/serviço de suporte da Microsoft.

## <a name="next-steps"></a>Próximas etapas

* [Use o PowerShell para criar um ponto de extremidade de serviço de rede virtual e, em seguida, uma regra da rede virtual para o Banco de Dados SQL do Azure.](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [Regras da rede virtual: Operações com APIs REST](/rest/api/sql/virtualnetworkrules)
* [Usar pontos de extremidade de serviço de rede virtual e regras para servidores](vnet-service-endpoint-rule-overview.md)
