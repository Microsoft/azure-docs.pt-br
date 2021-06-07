---
title: Gerenciar administradores de servidor no Azure Analysis Services | Microsoft Docs
description: Este artigo descreve como gerenciar administradores de servidor para um Azure Analysis Services Server usando o portal do Azure, o PowerShell ou as APIs REST.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 2/4/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 62acb526a247362b17c4dfd4e26c52760deecd71
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99573476"
---
# <a name="manage-server-administrators"></a>Gerenciar administradores de servidor

Os administradores de servidor devem ser um usuário, uma entidade de serviço ou um grupo de segurança válido no Azure Active Directory (Azure AD) para o locatário no qual o servidor reside. É possível usar **Administradores do Analysis Services** para o servidor no portal do Azure, Propriedades do Servidor no SSMS, PowerShell ou API REST para gerenciar administradores do servidor. 

Ao adicionar um **grupo de segurança**, use `obj:groupid@tenantid` . Não há suporte para entidades de serviço em grupos de segurança adicionados à função de administrador do servidor.

Para saber mais sobre como adicionar uma entidade de serviço à função de administrador do servidor, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

Se o Firewall do servidor estiver habilitado, os endereços IP do computador cliente do administrador do servidor deverão ser incluídos em uma regra de firewall. Para saber mais, consulte [Configurar o Firewall do servidor](analysis-services-qs-firewall.md).

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Para adicionar administradores do servidor usando o Portal do Azure

1. No portal, para o seu servidor, clique em **Administradores do Analysis Services**.
2. **\<servername> Administradores Analysis Services**, clique em **Adicionar**.
3. Em **Adicionar Administradores de Servidor**, selecione contas de usuário do seu Azure Active Directory ou convide usuários externos por endereço de email.

    ![Administradores de servidor no portal do Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Para adicionar administradores do servidor usando o SSMS

1. Clique com botão direito do mouse em servidor > **Propriedades**.
2. Em **Propriedades do Analysis Server**, clique em **Segurança**.
3. Clique em **Adicionar** e, em seguida, insira o endereço de email de um usuário ou grupo em seu Azure AD.
   
    ![Adicionar administradores do servidor no SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Use o cmdlet [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) para especificar o parâmetro de administrador ao criar um novo servidor. <br>
Use o cmdlet [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) para modificar o parâmetro de administrador de um servidor existente.

## <a name="rest-api"></a>API REST

Use [Criar](/rest/api/analysisservices/servers/create) para especificar a propriedade asAdministrator ao criar um novo servidor. <br>
Use [Atualizar](/rest/api/analysisservices/servers/update) para especificar a propriedade asAdministrator ao modificar um servidor existente. <br>



## <a name="next-steps"></a>Próximas etapas 

[Autenticação e permissões de usuário](analysis-services-manage-users.md)  
[Gerenciar usuários e funções de banco de dados](analysis-services-database-users.md)  
[RBAC do Azure (controle de acesso baseado em função do Azure)](../role-based-access-control/overview.md)
