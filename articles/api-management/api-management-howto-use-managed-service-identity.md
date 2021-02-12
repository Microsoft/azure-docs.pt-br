---
title: Usar identidades gerenciadas no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como criar identidades atribuídas pelo usuário e atribuídas pelo sistema no gerenciamento de API usando o portal do Azure, o PowerShell e um modelo do Resource Manager.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 11/14/2020
ms.author: apimpm
ms.openlocfilehash: 8ec0f8cf090b3ae85a8602fb39cb07f03a417133
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605591"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Usar identidades gerenciadas no gerenciamento de API do Azure

Este artigo mostra como criar uma identidade gerenciada para uma instância de gerenciamento de API do Azure e como acessar outros recursos. Uma identidade gerenciada gerada pelo Azure Active Directory (AD do Azure) permite que sua instância de gerenciamento de API acesse com facilidade e segurança outros recursos protegidos pelo Azure AD, como Azure Key Vault. O Azure gerencia essa identidade para que você não precise provisionar ou girar nenhum segredo. Para obter mais informações sobre identidades gerenciadas, consulte [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Você pode conceder dois tipos de identidade para uma instância de gerenciamento de API:

- Uma *identidade atribuída pelo sistema* é vinculada ao seu serviço e é excluída se o serviço for excluído. O serviço pode ter apenas uma identidade atribuída pelo sistema.
- Uma *identidade atribuída pelo usuário* é um recurso autônomo do Azure que pode ser atribuído ao seu serviço. O serviço pode ter várias identidades atribuídas pelo usuário.

## <a name="create-a-system-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída pelo sistema

### <a name="azure-portal"></a>Portal do Azure

Para configurar uma identidade gerenciada no portal do Azure, primeiro você criará uma instância de gerenciamento de API e, em seguida, habilitará o recurso.

1. Crie uma instância de Gerenciamento de API no portal, como você faria normalmente. Navegue até ele no Portal.
2. Selecione **identidades gerenciadas**.
3. Na guia **atribuído pelo sistema** , alterne o **status** para **ativado**. Selecione **Salvar**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Seleções para habilitar uma identidade gerenciada atribuída pelo sistema" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As etapas a seguir orientarão você na criação de uma instância de gerenciamento de API e na atribuição de uma identidade usando Azure PowerShell. 

1. Se necessário, instale Azure PowerShell usando as instruções no guia de [Azure PowerShell](/powershell/azure/install-az-ps). Em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure.

2. Use o código a seguir para criar a instância. Para obter mais exemplos de como usar Azure PowerShell com uma instância de gerenciamento de API, consulte [exemplos do PowerShell de gerenciamento de API](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Atualize uma instância existente para criar a identidade:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Crie uma instância de Gerenciamento de API com uma identidade incluindo a seguinte propriedade na definição de recurso:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Essa propriedade faz com que o Azure crie e gerencie a identidade da instância de Gerenciamento de API.

Por exemplo, um modelo completo do Azure Resource Manager pode ter a seguinte aparência:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

Quando a instância é criada, ela tem as seguintes propriedades adicionais:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A `tenantId` propriedade identifica a qual locatário do Azure ad a identidade pertence. A `principalId` propriedade é um identificador exclusivo para a nova identidade da instância. No Azure AD, a entidade de serviço tem o mesmo nome que você atribuiu à sua instância de gerenciamento de API.

> [!NOTE]
> Uma instância de gerenciamento de API pode ter identidades atribuídas pelo sistema e atribuídas pelo usuário ao mesmo tempo. Nesse caso, a `type` propriedade seria `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Cenários com suporte usando a identidade atribuída pelo sistema

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Obter um certificado TLS/SSL personalizado para a instância de gerenciamento de API do Azure Key Vault
Você pode usar a identidade atribuída pelo sistema de uma instância de gerenciamento de API para recuperar certificados TLS/SSL personalizados armazenados no Azure Key Vault. Você pode atribuir esses certificados a domínios personalizados na instância de gerenciamento de API. Lembre-se do seguinte:

- O tipo de conteúdo do segredo deve ser *Application/x-PKCS12*.
- Use o ponto de extremidade do segredo do certificado Key Vault, que contém o segredo.

> [!Important]
> Se você não fornecer a versão do objeto do certificado, o gerenciamento de API obterá automaticamente a versão mais recente do certificado dentro de quatro horas após sua atualização no Key Vault.

O exemplo a seguir mostra um modelo do Azure Resource Manager que contém as seguintes etapas:

1. Crie uma instância de gerenciamento de API com uma identidade gerenciada.
2. Atualizar as políticas de acesso de uma instância do Azure Key Vault e permitir que a instância de Gerenciamento de API obtenha os segredos dele.
3. Atualizar a instância de Gerenciamento de API configurando um nome de domínio personalizado por meio de um certificado da instância do Key Vault.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Autenticar para o back-end usando uma identidade de gerenciamento de API

Você pode usar a identidade atribuída pelo sistema para se autenticar no back-end por meio da política de [identidade gerenciada por autenticação](api-management-authentication-policies.md#ManagedIdentity) .

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

> [!NOTE]
> Você pode associar uma instância de gerenciamento de API com até 10 identidades gerenciadas atribuídas pelo usuário.

### <a name="azure-portal"></a>Portal do Azure

Para configurar uma identidade gerenciada no portal, primeiro você criará uma instância de gerenciamento de API e, em seguida, habilitará o recurso.

1. Crie uma instância de Gerenciamento de API no portal, como você faria normalmente. Navegue até ele no Portal.
2. Selecione **identidades gerenciadas**.
3. Na guia **atribuído pelo usuário** , selecione **Adicionar**.
4. Pesquise a identidade que você criou anteriormente e selecione-a. Selecione **Adicionar**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Seleções para habilitar uma identidade gerenciada atribuída pelo usuário" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As etapas a seguir orientarão você na criação de uma instância de gerenciamento de API e na atribuição de uma identidade usando Azure PowerShell. 

1. Se necessário, instale o Azure PowerShell usando as instruções no guia de [Azure PowerShell](/powershell/azure/install-az-ps). Em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure.

2. Use o código a seguir para criar a instância. Para obter mais exemplos de como usar Azure PowerShell com uma instância de gerenciamento de API, consulte [exemplos do PowerShell de gerenciamento de API](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Atualize um serviço existente para atribuir uma identidade ao serviço:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Crie uma instância de Gerenciamento de API com uma identidade incluindo a seguinte propriedade na definição de recurso:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Adicionar o tipo atribuído pelo usuário informa ao Azure para usar a identidade atribuída pelo usuário especificada para sua instância.

Por exemplo, um modelo completo do Azure Resource Manager pode ter a seguinte aparência:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
         "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Quando o serviço é criado, ele tem as seguintes propriedades adicionais:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

A `principalId` propriedade é um identificador exclusivo para a identidade usada para a administração do Azure AD. A `clientId` propriedade é um identificador exclusivo para a nova identidade do aplicativo que é usada para especificar qual identidade usar durante chamadas de tempo de execução.

> [!NOTE]
> Uma instância de gerenciamento de API pode ter identidades atribuídas pelo sistema e atribuídas pelo usuário ao mesmo tempo. Nesse caso, a `type` propriedade seria `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Cenários com suporte usando a identidade gerenciada atribuída pelo usuário

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Obter um certificado TLS/SSL personalizado para a instância de gerenciamento de API do Azure Key Vault
Você pode usar qualquer identidade atribuída pelo usuário para estabelecer a confiança entre uma instância de gerenciamento de API e o keyvault. Essa relação de confiança pode ser usada para recuperar certificados TLS/SSL personalizados armazenados no Azure Key Vault. Você pode atribuir esses certificados a domínios personalizados na instância de gerenciamento de API. 

Lembre-se do seguinte:

- O tipo de conteúdo do segredo deve ser *Application/x-PKCS12*.
- Use o ponto de extremidade do segredo do certificado Key Vault, que contém o segredo.

> [!Important]
> Se você não fornecer a versão do objeto do certificado, o gerenciamento de API obterá automaticamente a versão mais recente do certificado dentro de quatro horas após sua atualização no Key Vault.

Para obter o modelo completo, consulte [Gerenciamento de API com SSL baseado em keyvault usando a identidade atribuída pelo usuário](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json).

Neste modelo, você implantará:

* Gerenciamento de API do Azure
* Identidade atribuída ao usuário gerenciado do Azure
* Azure keyvault para armazenar o certificado SSL/TLS

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Autenticar para o back-end usando uma identidade atribuída pelo usuário

Você pode usar a identidade atribuída pelo usuário para se autenticar no back-end por meio da política de [identidade gerenciada por autenticação](api-management-authentication-policies.md#ManagedIdentity) .

## <a name="remove-an-identity"></a><a name="remove"></a>Remover uma identidade

Você pode remover uma identidade atribuída pelo sistema desabilitando o recurso por meio do portal ou do modelo de Azure Resource Manager da mesma maneira que ele foi criado. As identidades atribuídas pelo usuário podem ser removidas individualmente. Para remover todas as identidades, defina o tipo de identidade como `"None"` .

Remover uma identidade atribuída pelo sistema dessa maneira também a excluirá do Azure Active Directory. As identidades atribuídas pelo sistema também são removidas automaticamente do Azure AD quando a instância do gerenciamento de API é excluída.

Para remover todas as identidades usando o modelo Azure Resource Manager, atualize esta seção:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Se uma instância de gerenciamento de API estiver configurada com um certificado SSL personalizado do Key Vault e você tentar desabilitar uma identidade gerenciada, a solicitação falhará.
>
> Você pode desbloqueá-lo alternando de um certificado Azure Key Vault para um certificado codificado embutido e, em seguida, desabilitando a identidade gerenciada. Para saber mais, confira [Configurar um nome de domínio personalizado](configure-custom-domain.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre identidades gerenciadas para recursos do Azure:

* [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
* [Modelos do Gerenciador de Recursos do Azure](https://github.com/Azure/azure-quickstart-templates)
* [Autenticar com uma identidade gerenciada em uma política](./api-management-authentication-policies.md#ManagedIdentity)
