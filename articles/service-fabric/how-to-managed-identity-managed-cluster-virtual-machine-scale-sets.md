---
title: Adicionar uma identidade gerenciada a um Service Fabric tipo de nó de cluster gerenciado (versão prévia)
description: Este artigo mostra como adicionar uma identidade gerenciada a um Service Fabric tipo de nó de cluster gerenciado
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3ff5d66160ddbb037469378634826fd9eeae0c54
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651639"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>Adicionar uma identidade gerenciada a um Service Fabric tipo de nó de cluster gerenciado (versão prévia)

Cada tipo de nó em um Cluster Service Fabric gerenciado é apoiado por um conjunto de dimensionamento de máquinas virtuais. Para permitir que identidades gerenciadas sejam usadas com um tipo de nó de cluster gerenciado, uma propriedade foi `vmManagedIdentity` adicionada a definições de tipo de nó que contém uma lista de identidades que podem ser usadas, `userAssignedIdentities` . A funcionalidade espelha como as identidades gerenciadas podem ser usadas em clusters não gerenciados, como usar uma identidade gerenciada com a [extensão do conjunto de dimensionamento de máquinas virtuais Azure Key Vault](../virtual-machines/extensions/key-vault-windows.md).


Para obter um exemplo de uma implantação de cluster gerenciada Service Fabric que usa identidade gerenciada em um tipo de nó, consulte [este modelo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI). Para obter uma lista de regiões com suporte, consulte as [perguntas frequentes sobre o cluster gerenciado](./faq-managed-cluster.md#what-regions-are-supported-in-the-preview).

> [!NOTE]
> Atualmente, somente as identidades atribuídas ao usuário têm suporte para esse recurso.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
* Se você planeja usar o PowerShell, [Instale](/cli/azure/install-azure-cli) o CLI do Azure para executar comandos de referência da CLI.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário 

Uma identidade gerenciada atribuída pelo usuário pode ser definida na seção de recursos de um modelo de Azure Resource Manager (ARM) para criação na implantação:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

ou criado por meio do PowerShell:

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Adicionar uma atribuição de função com o provedor de recursos Service Fabric

Adicione uma atribuição de função à identidade gerenciada com o aplicativo provedor de recursos Service Fabric. Essa atribuição permite que Service Fabric provedor de recursos atribua a identidade ao conjunto de dimensionamento de máquinas virtuais do cluster gerenciado. 

Os valores a seguir devem ser usados quando aplicável:

|Nome|Valor do provedor de recursos de Service Fabric correspondente|
|----|-------------------------------------|
|ID do aplicativo|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|ID de objeto|fbc587f2-66f5-4459-a027-bcd908b9d278|


|Nome da definição de função|ID de definição de função|
|----|-------------------------------------|
|Operador de Identidade Gerenciada|f1a07417-d97a-45cb-824c-7a7467783830
|



Essa atribuição de função pode ser definida na seção de recursos usando a ID de objeto e a ID de definição de função:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

ou criado por meio do PowerShell usando a ID de aplicativo e a ID de definição de função:

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

ou ID de objeto e ID de definição de função:

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>Adicionar propriedades de identidade gerenciadas à definição de tipo de nó

Por fim, adicione `vmManagedIdentity` as `userAssignedIdentities` Propriedades e à definição de tipo de nó do cluster gerenciado. Certifique-se de usar **2021-01-01-Preview** ou posterior para o `apiVersion` .

```json

 {
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "2021-01-01-preview",
    ...
    "properties": {
        "isPrimary" : true,
        "vmInstanceCount": 5,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2_v2",
        "vmImagePublisher" : "MicrosoftWindowsServer",
        "vmImageOffer" : "WindowsServer",
        "vmImageSku" : "2019-Datacenter",
        "vmImageVersion" : "latest",
        "vmManagedIdentity": {
            "userAssignedIdentities": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
            ]
        }
    }
}
```

Após a implantação, a identidade gerenciada criada foi adicionada ao conjunto de dimensionamento de máquinas virtuais do tipo de nó designado e pode ser usada como esperado, assim como em qualquer cluster não gerenciado.

## <a name="troubleshooting"></a>Solução de problemas

A falha em adicionar uma atribuição de função corretamente será atendida com o seguinte erro na implantação:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Portal do Azure erro de implantação mostrando que o cliente com a ID de objeto/aplicativo do SFRP não tem permissão para executar a atividade de gerenciamento de identidade":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantar um aplicativo em um cluster gerenciado do Service Fabric](./tutorial-managed-cluster-deploy-app.md)