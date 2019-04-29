---
title: Acesso Condicional – Banco de Dados SQL do Azure e Data Warehouse | Microsoft Doc
description: Saiba como configurar o Acesso Condicional para o Banco de Dados SQL do Azure e o Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 03/29/2019
ms.openlocfilehash: 79d15a46affb2a6b7159ba080d4235073c59919c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61413629"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Acesso Condicional (MFA) com o Banco de Dados SQL do Azure e o Data Warehouse  

Azure [banco de dados SQL](sql-database-technical-overview.md), [instância gerenciada](sql-database-managed-instance.md), e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) dão suporte ao acesso condicional do Microsoft. 

> [!NOTE]
> Este tópico aplica-se ao servidor SQL do Azure e aos bancos de dados SQL e SQL Data Warehouse criados no servidor do SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.

As próximas etapas mostram como configurar o Banco de Dados SQL para impor uma política de Acesso Condicional.  

## <a name="prerequisites"></a>Pré-requisitos  
- Você deve configurar o Banco de Dados SQL ou o SQL Data Warehouse para dar suporte à autenticação do Azure Active Directory. Para obter etapas específicas, consulte [Configurar e gerenciar a autenticação do Azure Active Directory com o Banco de Dados SQL ou o SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Quando a autenticação multifator estiver habilitada, você deverá se conectar com uma ferramenta com suporte, como o último SSMS. Para obter mais informações, consulte [Configurar a autenticação multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurar a AC para o BD SQL do Azure/DW  
1. Entre no Portal, selecione **Azure Active Directory** e, em seguida, selecione **Acesso condicional**. Para obter mais informações, consulte [Referência técnica do Acesso Condicional ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![folha do acesso condicional](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Na folha **Políticas de Acesso Condicional**, clique em **Nova política**, forneça um nome e, em seguida, clique em **Configurar regras**.  
3. Em **Atribuições**, selecione **Usuários e grupos**, marque a opção **Selecionar usuários e grupos** e, em seguida, selecione o usuário ou o grupo para o acesso condicional. Clique em **Selecionar** e, em seguida, em **Concluído** para aceitar a seleção.  
   ![selecionar usuários e grupos](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Selecione **Aplicativos na nuvem** e clique em **Selecionar aplicativos**. Você verá todos os aplicativos disponíveis para o acesso condicional. Selecione **Banco de Dados SQL do Azure**, na parte inferior clique em **Selecionar** e, em seguida, clique em **Concluído**.  
   ![selecionar Banco de Dados SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Se você não encontrar **banco de dados SQL** listados na seguinte captura de tela de terceiro, conclua as seguintes etapas:   
   - Entre na instância do BD SQL do Azure/DW usando o SSMS com uma conta do administrador do AAD.  
   - Execute `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Entre no AAD e verifique se o Banco de Dados SQL do Azure e o Data Warehouse estão listados nos aplicativos no AAD.  

5. Selecione **Controles de acesso**, selecione **Conceder** e, em seguida, marque a política que você deseja aplicar. Para este exemplo, selecionamos **Exigir autenticação multifator**.  
   ![selecionar conceder acesso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Resumo  
O aplicativo selecionado (Banco de Dados SQL do Azure), que permite se conectar ao BD SQL do Azure/DW usando o Azure AD Premium, agora impõe a política de Acesso Condicional selecionada, **Exigir autenticação multifator.**  
Em caso de dúvidas sobre o Banco de Dados SQL do Azure e o Data Warehouse com relação à autenticação multifator, contate MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Próximas etapas  

Para obter um tutorial, consulte [Proteger o Banco de Dados SQL do Azure](sql-database-security-tutorial.md).
