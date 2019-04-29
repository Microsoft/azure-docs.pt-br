---
title: Adicionar uma entidade de serviço à função de administrador do servidor do Azure Analysis Services | Microsoft Docs
description: Saiba como adicionar uma entidade de serviço de automação à função de administrador do servidor
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 701be795ca217c4a2dc5a7dbaa3a3717d16c85bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61024588"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Adicionar uma entidade de serviço à função de administrador do servidor 

 Para automatizar tarefas do PowerShell autônomas, uma entidade de serviço deve ter privilégios de **administrador do servidor** no servidor do Analysis Services sendo gerenciado. Este artigo descreve como adicionar uma entidade de serviço à função de administradores do servidor em um servidor do AS do Azure.

## <a name="before-you-begin"></a>Antes de começar
Antes de concluir essa tarefa, você deverá ter uma entidade de serviço registrado no Azure Active Directory.

[Criar entidade de serviço - Portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Criar entidade de serviço - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Permissões necessárias
Para concluir essa tarefa, você deve ter permissões de [administrador do servidor](analysis-services-server-admins.md) no servidor do AS do Azure. 

## <a name="add-service-principal-to-server-administrators-role"></a>Adicionar entidade de serviço à função de administradores do servidor

1. No SSMS, conecte o servidor do AS do Azure.
2. Em **Propriedades do Servidor** > **Segurança**, clique em **Adicionar**.
3. Em **Selecionar Usuário ou Grupo**, procure o aplicativo registrado pelo nome, selecione e clique em**Adicionar**.

    ![Pesquise a conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifique o ID da conta da entidade do serviço e clique em **OK**.
    
    ![Pesquise a conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Para operações de servidor usando cmdlets do PowerShell do Azure, o Agendador em execução do serviço principal também deve pertencer à **proprietário** função para o recurso no [controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Informações relacionadas

* [Baixar o módulo do PowerShell do SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


