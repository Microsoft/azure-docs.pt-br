---
title: Adicionar uma entidade de serviço à função de administrador do Azure Analysis Services | Microsoft Docs
description: Saiba como adicionar uma entidade de serviço de automação à função de administrador do servidor do Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b1d5f8ec628245756c53e4dfeeecdeb3a4bebc2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100721"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Adicionar uma entidade de serviço à função de administrador do servidor 

 Para automatizar tarefas do PowerShell autônomas, uma entidade de serviço deve ter privilégios de **administrador do servidor** no servidor do Analysis Services sendo gerenciado. Este artigo descreve como adicionar uma entidade de serviço à função de administradores do servidor em um servidor do AS do Azure. Você pode fazer isso usando o SQL Server Management Studio ou um modelo do Resource Manager. 

> [!NOTE]
> As entidades de serviço devem ser adicionadas diretamente à função de administrador do servidor. Não há suporte para a adição de uma entidade de serviço a um grupo de segurança e a adição desse grupo de segurança à função de administrador do servidor. 

## <a name="before-you-begin"></a>Antes de começar
Antes de concluir essa tarefa, você deverá ter uma entidade de serviço registrado no Azure Active Directory.

[Criar entidade de serviço - Portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar entidade de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Como usar o SQL Server Management Studio.

Você pode configurar administradores de servidor usando o SSMS (SQL Server Management Studio). Para concluir essa tarefa, você deve ter permissões de [administrador do servidor](analysis-services-server-admins.md) no servidor do AS do Azure. 

1. No SSMS, conecte o servidor do AS do Azure.
2. Em **Propriedades do Servidor** > **Segurança**, clique em **Adicionar**.
3. Em **Selecionar Usuário ou Grupo**, procure o aplicativo registrado pelo nome, selecione e clique em **Adicionar**.

    ![Pesquise a conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifique o ID da conta da entidade do serviço e clique em **OK**.
    
    ![Captura de tela que mostra a ID da conta da entidade de serviço e realça o botão OK.](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Usar um modelo do Resource Manager

Você também pode configurar os administradores de servidor implantando o servidor do Analysis Services usando um modelo do Azure Resource Manager. A identidade que executa a implantação deve pertencer à função de **colaborador** para o recurso no [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md).

> [!IMPORTANT]
> A entidade de serviço deve ser adicionada usando o formato `app:{service-principal-client-id}@{azure-ad-tenant-id}`.

O seguinte modelo do Resource Manager implanta um servidor do Analysis Services com uma entidade de serviço especificada adicionada à função de administrador do Analysis Services:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Como usar identidades gerenciadas

Uma identidade gerenciada também pode ser adicionada à lista de administradores do Analysis Services. Por exemplo, você pode ter um [Aplicativo lógico com uma identidade gerenciada atribuída pelo sistema](../logic-apps/create-managed-service-identity.md) e querer conceder a ele a capacidade de administrar seu servidor do Analysis Services.

Na maioria das partes das APIs e do portal do Azure, as identidades gerenciadas são identificadas usando sua ID de objeto de entidade de serviço. No entanto, o Analysis Services exige que eles sejam identificados usando sua ID do cliente. Para obter a ID do cliente para uma entidade de serviço, você pode usar a CLI do Azure:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Como alternativa, você pode usar o PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Você pode usar essa ID de cliente em conjunto com a ID de locatário para adicionar a identidade gerenciada à lista de administradores do Analysis Services, conforme descrito acima.

## <a name="related-information"></a>Informações relacionadas

* [Baixar o módulo do PowerShell do SQL Server](/sql/ssms/download-sql-server-ps-module)   
* [Baixar o SSMS](/sql/ssms/download-sql-server-management-studio-ssms)