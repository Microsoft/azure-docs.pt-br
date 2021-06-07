---
title: Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso no ISEs
description: Crie e gerencie suas próprias chaves de criptografia para proteger dados em repouso para ambientes de serviço de integração (ISEs) em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: mijos, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: d31fbd813f0c5d63ee9eddbff5b299209618626b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98629667"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso para ambientes de serviço de integração (ISEs) em aplicativos lógicos do Azure

Os aplicativos lógicos do Azure dependem do armazenamento do Azure para armazenar e [criptografar automaticamente os dados em repouso](../storage/common/storage-service-encryption.md). Essa criptografia protege seus dados e ajuda a atender aos compromissos de segurança e conformidade da organização. Por padrão, o armazenamento do Azure usa chaves gerenciadas pela Microsoft para criptografar seus dados. Para obter mais informações sobre como funciona a criptografia de armazenamento do Azure, consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md) e [criptografia de dados do Azure em repouso](../security/fundamentals/encryption-atrest.md).

Quando você cria um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) para hospedar seus aplicativos lógicos e deseja obter mais controle sobre as chaves de criptografia usadas pelo armazenamento do Azure, você pode configurar, usar e gerenciar sua própria chave usando [Azure Key Vault](../key-vault/general/overview.md). Esse recurso é conhecido como "Bring Your Own Key" (BYOK) e sua chave é chamada de "chave gerenciada pelo cliente". Com esse recurso, o armazenamento do Azure habilita automaticamente a criptografia [dupla ou a *criptografia de infraestrutura* usando chaves gerenciadas por plataforma](../security/fundamentals/double-encryption.md) para sua chave. Para saber mais, confira [criptografar dados duplicados com criptografia de infraestrutura](../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption).

Este tópico mostra como configurar e especificar sua própria chave de criptografia a ser usada ao criar o ISE usando a API REST de aplicativos lógicos. Para ver as etapas gerais para criar um ISE por meio da API REST de aplicativos lógicos, consulte [criar um ambiente de serviço de integração (ISE) usando a API REST de aplicativos lógicos](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Considerações

* Neste momento, o suporte de chave gerenciada pelo cliente para um ISE está disponível somente nestas regiões do Azure: oeste dos EUA 2, leste dos EUA e Sul EUA Central

* Você pode especificar uma chave gerenciada pelo cliente *somente quando criar o ISE*, não depois. Não é possível desabilitar essa chave após a criação do ISE. No momento, não existe suporte para a rotação de uma chave gerenciada pelo cliente para um ISE.

* Para dar suporte a chaves gerenciadas pelo cliente, seu ISE exige que você habilite a [identidade gerenciada atribuída pelo sistema ou pelo usuário](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Essa identidade permite que o ISE autentique o acesso a recursos protegidos, como máquinas virtuais e outros sistemas ou serviços, que estão no ou estão conectados a uma rede virtual do Azure. Dessa forma, você não precisa entrar com suas credenciais.

* No momento, para criar um ISE que ofereça suporte a chaves gerenciadas pelo cliente e que tenha o tipo de identidade gerenciada habilitado, você precisa chamar a API REST dos aplicativos lógicos usando uma solicitação HTTPS PUT.

* Você deve [fornecer acesso ao cofre de chaves para a identidade gerenciada do ISE](#identity-access-to-key-vault), mas o tempo depende de qual identidade gerenciada você usa.

  * **Identidade gerenciada atribuída pelo sistema**: dentro de *30 minutos depois* de enviar a solicitação HTTPS Put que cria o ISE, você deve [fornecer acesso ao cofre de chaves para a identidade gerenciada do ISE](#identity-access-to-key-vault). Caso contrário, a criação do ISE falhará e você receberá um erro de permissões.

  * **Identidade gerenciada atribuída pelo usuário**: antes de enviar a solicitação HTTPS Put que cria o ISE, [conceda ao cofre de chaves acesso à identidade gerenciada do ISE](#identity-access-to-key-vault).

## <a name="prerequisites"></a>Pré-requisitos

* Os mesmos [pré-requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisitos para habilitar o acesso ao ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) como quando você cria um ISE no portal do Azure

* Um cofre de chaves do Azure que tem as propriedades **exclusão reversível** e **não limpar** habilitadas

  Para obter mais informações sobre como habilitar essas propriedades, consulte [visão geral Azure Key Vault exclusão reversível](../key-vault/general/soft-delete-overview.md) e [Configurar chaves gerenciadas pelo cliente com Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md). Se você for novo no [Azure Key Vault](../key-vault/general/overview.md), saiba como criar um cofre de chaves usando [portal do Azure](../key-vault/general/quick-create-portal.md), [CLI do Azure](../key-vault/general/quick-create-cli.md)ou [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

* No cofre de chaves, uma chave criada com esses valores de propriedade:

  | Propriedade | Valor |
  |----------|-------|
  | **Tipo de Chave** | RSA |
  | **Tamanho da chave RSA** | 2.048 |
  | **Enabled** | Sim |
  |||

  ![Criar sua chave de criptografia gerenciada pelo cliente](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Para obter mais informações, consulte [Configurar chaves gerenciadas pelo cliente com Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md) ou o comando Azure PowerShell, [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey).

* Uma ferramenta que você pode usar para criar seu ISE chamando a API REST de aplicativos lógicos com uma solicitação HTTPS PUT. Por exemplo, você pode usar o [postmaster](https://www.getpostman.com/downloads/)ou pode criar um aplicativo lógico que executa essa tarefa.

<a name="enable-support-key-managed-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Criar ISE com o cofre de chaves e o suporte de identidade gerenciada

Para criar seu ISE chamando a API REST dos aplicativos lógicos, faça esta solicitação HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A versão 2019-05-01 da API REST dos aplicativos lógicos requer que você faça sua própria solicitação HTTPS PUT para conectores ISE.

A implantação geralmente leva dentro de duas horas para ser concluída. Em alguns casos, a implantação pode levar até quatro horas. Para verificar o status da implantação, na [portal do Azure](https://portal.azure.com), na barra de ferramentas do Azure, selecione o ícone notificações, que abre o painel notificações.

> [!NOTE]
> Se a implantação falhar ou se você excluir o ISE, o Azure poderá levar até uma hora para liberar as sub-redes. Esse atraso significa que você precisará esperar antes de reutilizar essas sub-redes em outro ISE.
>
> Se você excluir sua rede virtual, o Azure geralmente levará até duas horas antes de lançar suas sub-redes, mas essa operação poderá levar mais tempo. 
> Ao excluir redes virtuais, verifique se algum recurso ainda está conectado. 
> Confira [Excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Cabeçalho da solicitação

No cabeçalho da solicitação, inclua estas propriedades:

* `Content-type`: Defina esse valor de propriedade como `application/json` .

* `Authorization`: Defina esse valor de propriedade para o token de portador para o cliente que tem acesso à assinatura do Azure ou ao grupo de recursos que você deseja usar.

### <a name="request-body"></a>Corpo da solicitação

No corpo da solicitação, habilite o suporte para esses itens adicionais fornecendo suas informações em sua definição de ISE:

* A identidade gerenciada que seu ISE usa para acessar o cofre de chaves
* O cofre de chaves e a chave gerenciada pelo cliente que você deseja usar

#### <a name="request-body-syntax"></a>Sintaxe de corpo da solicitação

Aqui está a sintaxe do corpo da solicitação, que descreve as propriedades a serem usadas ao criar o ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Exemplo de corpo de solicitação

Este exemplo de corpo de solicitação mostra os valores de exemplo:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves

Embora o intervalo seja diferente com base na identidade gerenciada que você usa, você deve [fornecer acesso ao cofre de chaves para a identidade gerenciada do ISE](#identity-access-to-key-vault).

* **Identidade gerenciada atribuída pelo sistema**: em *30 minutos depois* de enviar a solicitação HTTPS Put que cria o ISE, você deve adicionar uma política de acesso ao cofre de chaves para a identidade gerenciada atribuída pelo sistema do ISE. Caso contrário, a criação de seu ISE falhará e você receberá um erro de permissões.

* **Identidade gerenciada atribuída pelo usuário**: antes de enviar a solicitação HTTPS Put que cria o ISE, adicione uma política de acesso ao cofre de chaves para a identidade gerenciada atribuída pelo usuário do ISE.

Para essa tarefa, você pode usar o comando Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) ou pode seguir estas etapas na portal do Azure:

1. No [portal do Azure](https://portal.azure.com), abra o cofre de chaves do Azure.

1. No menu do cofre de chaves, selecione **políticas de acesso**  >  **Adicionar política de acesso**, por exemplo:

   ![Adicionar política de acesso para identidade gerenciada atribuída pelo sistema](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Depois que o painel **Adicionar política de acesso** for aberto, siga estas etapas:

   1. Selecione estas opções:

      | Configuração | Valores |
      |---------|--------|
      | **Configurar a partir do modelo (opcional) lista** | Gerenciamento de chaves |
      | **Permissões de chave** | - **Operações de gerenciamento de chaves**: obter, listar <p><p>- **Operações criptográficas**: desencapsular chave, encapsular chave |
      |||

      ![Selecione "gerenciamento de chaves" > "permissões de chave"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Para **selecionar entidade de segurança**, selecione **nenhum selecionado**. Depois que o painel **principal** for aberto, na caixa de pesquisa, localize e selecione o ISE. Quando terminar **, escolha**  >  **Adicionar**.

      ![Selecione o ISE para usar como o principal](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Quando tiver terminado com o painel **políticas de acesso** , selecione **salvar**.

Para obter mais informações, consulte [como autenticar para Key Vault](../key-vault/general/authentication.md) e [atribuir uma política de acesso de Key Vault](../key-vault/general/assign-access-policy-portal.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Azure Key Vault](../key-vault/general/overview.md)
