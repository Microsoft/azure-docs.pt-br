---
title: "Acesso Condicional – Banco de Dados SQL do Azure e Data Warehouse | Microsoft Doc"
description: Saiba como configurar o Acesso Condicional para o Banco de Dados SQL do Azure e o Data Warehouse.
services: sql-database
author: BYHAM
manager: jhubbard
ms.custom: security
ms.service: sql-database
ms.topic: article
ms.date: 06/07/2017
ms.author: rickbyh
ms.openlocfilehash: 0dcec61c03a84197e2c351761c743683caa98a06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Acesso Condicional (MFA) com o Banco de Dados SQL do Azure e o Data Warehouse  

O Banco de Dados SQL e o SQL Data Warehouse dão suporte ao Acesso Condicional da Microsoft. As próximas etapas mostram como configurar o Banco de Dados SQL para impor uma política de Acesso Condicional.  

## <a name="prerequisites"></a>Pré-requisitos  
- Você deve configurar o Banco de Dados SQL ou o SQL Data Warehouse para dar suporte à autenticação do Azure Active Directory. Para obter etapas específicas, consulte [Configurar e gerenciar a autenticação do Azure Active Directory com o Banco de Dados SQL ou o SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Quando a autenticação multifator estiver habilitada, você deverá se conectar com uma ferramenta com suporte, como o último SSMS. Para obter mais informações, consulte [Configurar a autenticação multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurar a AC para o BD SQL do Azure/DW  
1.  Entre no Portal, selecione **Azure Active Directory** e, em seguida, selecione **Acesso condicional**. Para obter mais informações, consulte [Referência técnica do Acesso Condicional ao Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![folha do acesso condicional](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  Na folha **Políticas de Acesso Condicional**, clique em **Nova política**, forneça um nome e, em seguida, clique em **Configurar regras**.  
3.  Em **Atribuições**, selecione **Usuários e grupos**, marque a opção **Selecionar usuários e grupos** e, em seguida, selecione o usuário ou o grupo para o acesso condicional. Clique em **Selecionar** e, em seguida, em **Concluído** para aceitar a seleção.  
  ![selecionar usuários e grupos](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Selecione **Aplicativos na nuvem** e clique em **Selecionar aplicativos**. Você verá todos os aplicativos disponíveis para o acesso condicional. Selecione **Banco de Dados SQL do Azure**, na parte inferior clique em **Selecionar** e, em seguida, clique em **Concluído**.  
  ![selecionar Banco de Dados SQL](./media/sql-database-conditional-access/select-sql-database.png)  
  Se você não encontrar o **Banco de Dados SQL do Azure** listado na terceira captura de tela a seguir, conclua as seguintes etapas:   
  - Entre na instância do BD SQL do Azure/DW usando o SSMS com uma conta do administrador do AAD.  
  - Execute `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Entre no AAD e verifique se o Banco de Dados SQL do Azure e o Data Warehouse estão listados nos aplicativos no AAD.  

5.  Selecione **Controles de acesso**, selecione **Conceder** e, em seguida, marque a política que você deseja aplicar. Para este exemplo, selecionamos **Exigir autenticação multifator**.  
  ![selecionar conceder acesso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Resumo  
O aplicativo selecionado (Banco de Dados SQL do Azure), que permite se conectar ao BD SQL do Azure/DW usando o Azure AD Premium, agora impõe a política de Acesso Condicional selecionada, **Exigir autenticação multifator.**  
Em caso de dúvidas sobre o Banco de Dados SQL do Azure e o Data Warehouse com relação à autenticação multifator, contate MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Próximas etapas  

Para obter um tutorial, consulte [Proteger o Banco de Dados SQL do Azure](sql-database-security-tutorial.md).
