---
title: Configurar identidades gerenciadas no conjunto de dimensionamento de máquinas virtuais do Azure usando REST – Azure AD
description: Instruções passo a passo para configurar um sistema e identidades gerenciadas atribuídas pelo usuário em um conjunto de dimensionamento de máquinas virtuais do Azure usando CURL para fazer chamadas à API REST.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 585e31ab566cc990af2819fcf9cdde0506560208
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780165"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando chamadas à API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade do sistema gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, usando CURL para fazer chamadas ao ponto de extremidade de REST do Azure Resource Manager, você aprenderá como executar as seguintes identidades gerenciadas para operações de recursos do Azure em um conjunto de dimensionamento de máquinas virtuais:

- Habilitar e desabilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais do Azure
- Adicionar e remover uma identidade gerenciada atribuída ao usuário em um conjunto de dimensionamento de máquinas virtuais do Azure

Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com as identidades gerenciadas dos recursos do Azure, confira [O que são as identidades gerenciadas dos recursos do Azure?](overview.md). Para saber mais sobre tipos de identidade gerenciada atribuída pelo sistema e pelo usuário, confira [Tipos de identidade gerenciada](overview.md#managed-identity-types).

- Para realizar as operações de gerenciamento deste artigo, a conta precisará das seguintes atribuições de função do Azure:

  - [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover identidade gerenciada atribuída ao usuário e/ou sistema de um conjunto de dimensionamento de máquinas virtuais.

  - [Função de Contratada de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) para criar uma identidade gerenciada atribuída pelo usuário.

  - Função de [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator) para atribuir e remover uma identidade atribuída ao usuário de e para um conjunto de dimensionamento de máquinas virtuais.

  > [!NOTE]
  > Não são necessárias atribuições de função do diretório adicionais do Azure AD.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais usando CURL para fazer chamadas ao ponto de extremidade de REST do Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Habilitar identidade gerenciada atribuída ao sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais

Para criar um conjunto de dimensionamento de máquinas virtuais com identidade gerenciada atribuída ao sistema habilitada, será necessário criar um conjunto de dimensionamento de máquinas virtuais e recuperar um token de acesso para usar CURL para chamar o ponto de extremidade do Resource Manager com o valor de tipo de identidade gerenciada atribuída ao sistema.

1. Crie um [grupo de recursos](../../azure-resource-manager/management/overview.md#terminology) para confinamento e implantação do conjunto de dimensionamento de máquinas virtuais e os recursos relacionados, usando [az group create](/cli/azure/group/#az_group_create). Ignore esta etapa, se você já tiver o grupo de recursos que deseja usar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic#az_network_nic_create) para seu conjunto de dimensionamento de máquinas virtuais:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso do Portador, que será usado na próxima etapa no cabeçalho de Autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Usando o Azure Cloud Shell, crie um conjunto de dimensionamento de máquinas virtuais usando o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais nomeado *myVMSS* no *myResourceGroup* com uma identidade gerenciada atribuída ao sistema, conforme identificado no corpo da solicitação pelo valor `"identity":{"type":"SystemAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Habilitar identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais existente

Para habilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais existente, você precisa adquirir um token de acesso e, em seguida, usar CURL para chamar o ponto de extremidade de REST do Resource Manager para atualizar o tipo de identidade.

1. Recupere um token de acesso do Portador, que será usado na próxima etapa no cabeçalho de Autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Use o seguinte comando CURL para chamar o ponto de extremidade de REST do Azure Resource Manager para habilitar a identidade gerenciada atribuída ao sistema no conjunto de dimensionamento de máquinas virtuais, identificada no corpo da solicitação pelo valor `{"identity":{"type":"SystemAssigned"}`, para um conjunto de dimensionamento de máquinas virtuais nomeado *myVMSS*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.
   
   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída ao usuário existente que esteja atribuída ao conjunto de dimensionamento de máquinas virtuais, será necessário listar as identidades gerenciadas atribuídas ao usuário, usando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se houver alguma identidade gerenciada atribuída ao usuário ao conjunto de dimensionamento de máquinas virtuais identificada no valor `identity`, passe para a etapa 3 que mostra como reter identidades gerenciadas atribuídas ao usuário ao habilitar a identidade gerenciada atribuída ao sistema no conjunto de dimensionamento de máquinas virtuais.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Para habilitar a identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais com identidades gerenciadas atribuídas ao usuário existentes, é necessário adicionar `SystemAssigned` ao valor `type`.  
   
   Por exemplo, se o conjunto de dimensionamento de máquinas virtuais tiver as identidades gerenciadas atribuídas ao usuário `ID1` e `ID2` atribuídas a ele, e você quer adicionar uma identidade gerenciada atribuída ao sistema ao conjunto de dimensionamento de máquinas virtuais, use a seguinte chamada de CURL. Substitua `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` pelos valores apropriados para seu ambiente.

   A versão da API `2018-06-01` armazena identidades gerenciadas designadas pelo usuário no valor `userAssignedIdentities` em um formato de dicionário, em oposição ao valor `identityIds` em um formato de matriz usado na versão da API `2017-12-01`.
   
   **API DE 2018 VERSÃO-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. |
 
   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```
   
   **VERSÃO DA API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Desabilitar identidade gerenciada atribuída ao sistema a partir de um conjunto de dimensionamento de máquinas virtuais

Para desabilitar uma identidade gerenciada atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais existente, você precisará adquirir um token de acesso e, em seguida, usar CURL para chamar o ponto de extremidade de REST do Resource Manager para atualizar o tipo de identidade para `None`.

1. Recupere um token de acesso do Portador, que será usado na próxima etapa no cabeçalho de Autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Atualize o conjunto de dimensionamento de máquinas virtuais usando CURL para chamar o ponto de extremidade de REST do Azure Resource Manager para desabilitar a identidade gerenciada atribuída ao sistema.  O exemplo a seguir desabilita a identidade gerenciada atribuída ao sistema, identificada no corpo da solicitação pelo valor `{"identity":{"type":"None"}}`, de um conjunto de dimensionamento de máquinas virtuais nomeado *myVMSS*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   > [!IMPORTANT]
   > Para garantir que você não exclua nenhuma identidade gerenciada atribuída ao usuário existente que esteja atribuída ao conjunto de dimensionamento de máquinas virtuais, será necessário listar as identidades gerenciadas atribuídas ao usuário, usando este comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se houver alguma identidade gerenciada atribuída ao usuário ao conjunto de dimensionamento de máquinas virtuais, passe para a etapa 3 que mostra como reter identidades gerenciadas atribuídas ao usuário, ao remover a identidade atribuída ao sistema do conjunto de dimensionamento de máquinas virtuais.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Para remover a identidade gerenciada atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais que tenha identidades gerenciadas atribuídas ao usuário, remova `SystemAssigned` do valor `{"identity":{"type:" "}}` enquanto mantém o valor `UserAssigned` e os valores do dicionário `userAssignedIdentities`, se estiver usando a **Versão da API 01-06-2018**. Se você estiver usando a **versão da API 2017-12-01** ou anterior, mantenha o array `identityIds`.

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade gerenciada atribuída ao usuário em um conjunto de dimensionamento de máquinas virtuais usando CURL para fazer chamadas ao ponto de extremidade de REST do Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída ao usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais

1. Recupere um token de acesso do Portador, que será usado na próxima etapa no cabeçalho de Autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Crie uma [interface de rede](/cli/azure/network/nic#az_network_nic_create) para seu conjunto de dimensionamento de máquinas virtuais:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recupere um token de acesso do Portador, que será usado na próxima etapa no cabeçalho de Autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Crie uma identidade gerenciada atribuída pelo usuário usando as instruções encontradas aqui: [Crie uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Crie um conjunto de dimensionamento de máquinas virtuais usando o CURL para chamar o ponto de extremidade REST do Azure Resource Manager. O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais nomeado *myVMSS* no grupo de recursos *myResourceGroup* com uma identidade gerenciada atribuída ao usuário `ID1`, identificada no corpo da solicitação pelo valor `"identity":{"type":"UserAssigned"}`. Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.
 
   **API DE 2018 VERSÃO-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **VERSÃO DA API 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. |
 
   **Corpo da solicitação**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada usuário atribuído a um conjunto de dimensionamento de máquina virtual do Azure existente

1. Recupere um token de acesso do Portador, que será usado na próxima etapa no cabeçalho de Autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Crie uma identidade gerenciada atribuída pelo usuário usando as instruções encontradas aqui, [Crie uma identidade gerenciada atribuída pelo usuário](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Para garantir que você não exclua nenhuma identidade gerenciada atribuída ao usuário ou ao sistema existente que esteja atribuída ao conjunto de dimensionamento de máquinas virtuais, será necessário listar os tipos de identidade atribuída ao conjunto de dimensionamento de máquinas virtuais, usando o seguinte comando CURL. Se houver identidades gerenciadas atribuídas ao conjunto de dimensionamento de máquinas virtuais, elas serão listadas no valor `identity`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. |   
 

4. Se não houver nenhuma identidade gerenciada atribuída ao usuário ou ao sistema ao conjunto de dimensionamento de máquinas virtuais, use o seguinte comando CURL para chamar o ponto de extremidade de REST do Azure Resource Manager para atribuir a primeira identidade gerenciada atribuída ao usuário ao conjunto de dimensionamento de máquinas virtuais.  Se houver alguma identidade gerenciada atribuída ao usuário ou ao sistema ao conjunto de dimensionamento de máquinas virtuais, passe para a etapa 5 que mostra como adicionar várias identidades gerenciadas atribuídas ao usuário a um conjunto de dimensionamento de máquinas virtuais, mantendo a identidade gerenciada atribuída ao sistema.

   O exemplo a seguir atribui uma identidade gerenciada atribuída ao usuário, `ID1`, a um conjunto de dimensionamento de máquinas virtuais nomeado *myVMSS* no grupo de recursos *myResourceGroup*.  Substitua `<ACCESS TOKEN>` pelo valor recebido na etapa anterior quando você solicitou um token de acesso de portador e o valor de `<SUBSCRIPTION ID>` apropriado para seu ambiente.

   **API DE 2018 VERSÃO-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **VERSÃO DA API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Se você tiver uma identidade gerenciada atribuída ao usuário ou atribuída ao sistema atribuída ao conjunto de dimensionamento de máquinas virtuais:
   
   **API DE 2018 VERSÃO-06-01**

   Adicionar a identidade atribuída pelo usuário gerenciada para o `userAssignedIdentities` valor do dicionário.

   Por exemplo, se você tiver uma identidade gerenciada atribuída ao sistema e a identidade atribuída ao usuário `ID1` atualmente atribuída ao conjunto de dimensionamento de máquinas virtuais e quiser adicionar a identidade gerenciada atribuída ao usuário `ID2`:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **VERSÃO DA API 2017-12-01**

   Reter as identidades gerenciadas atribuídas pelo usuário que você gostaria de manter no valor da matriz `identityIds` ao adicionar a nova identidade gerenciada atribuída pelo usuário.

   Por exemplo, se você tiver uma identidade atribuída ao sistema e a identidade gerenciada atribuída ao usuário `ID1` atualmente atribuída ao conjunto de dimensionamento de máquinas virtuais e quiser adicionar a identidade gerenciada atribuída ao usuário `ID2`:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Remover uma identidade gerenciada atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais

1. Recupere um token de acesso do Portador, que será usado na próxima etapa no cabeçalho de Autorização para criar o conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída ao sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Para garantir que você não exclua nenhuma identidade gerenciada atribuída ao usuário que gostaria de manter atribuída ao conjunto de dimensionamento de máquinas virtuais nem remover a identidade gerenciada atribuída ao sistema, é necessário listar as identidades gerenciadas usando os seguintes comandos CURL:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. |
   
   Se houver identidades gerenciadas atribuídas à VM, elas serão listadas na resposta no valor `identity`. 
    
   Por exemplo, se você tiver identidades gerenciadas atribuídas ao usuário `ID1` e `ID2` atribuídas ao conjunto de dimensionamento de máquinas virtuais e você somente quer manter `ID1` atribuído e reter a identidade gerenciada atribuída ao sistema:

   **API DE 2018 VERSÃO-06-01**

   Adicionar `null` para o usuário atribuído gerenciado identidade que você deseja remover:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **VERSÃO DA API 2017-12-01**

   Reter apenas as identidades gerenciadas atribuídas pelo usuário que você gostaria de manter na matriz `identityIds`:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Cabeçalhos de solicitação**

   |Cabeçalho da solicitação  |Descrição  |
   |---------|---------|
   |*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
   |*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

   **Corpo da solicitação**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Se o conjunto de dimensionamento de máquinas virtuais tiver identidades gerenciadas atribuídas ao sistema e atribuídas ao usuário, você poderá remover todas as identidades gerenciadas atribuídas ao usuário, alternando para usar somente atribuídas ao sistema usando o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos de solicitação**

|Cabeçalho da solicitação  |Descrição  |
|---------|---------|
|*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
|*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

**Corpo da solicitação**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Se o conjunto de dimensionamento de máquinas virtuais tiver apenas identidades gerenciadas atribuídas ao usuário e você quer remover todas elas, use o seguinte comando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Cabeçalhos de solicitação**

|Cabeçalho da solicitação  |Descrição  |
|---------|---------|
|*Content-Type*     | Obrigatórios. Defina como `application/json`.        |
|*Autorização*     | Obrigatórios. Defina como um `Bearer` token de acesso válido. | 

**Corpo da solicitação**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como criar, listar ou excluir identidades gerenciadas atribuídas pelo usuário usando o REST, consulte:

- [Criar, listar ou excluir uma identidade gerenciada atribuída ao usuário usando chamadas à API REST](how-to-manage-ua-identity-rest.md)
