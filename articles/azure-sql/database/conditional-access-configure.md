---
title: Acesso Condicional
description: Saiba como configurar o acesso condicional para o banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse Analytics.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: c18d235977f1256a10e813fa8e02aa3590366fe1
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936406"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Acesso condicional com o banco de dados SQL do Azure e o Azure Synapse Analytics

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

O [banco de dados SQL do Azure](sql-database-paas-overview.md), o [Azure SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md)e o [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) dão suporte ao acesso condicional da Microsoft.

As etapas a seguir mostram como configurar o banco de dados SQL do Azure, o SQL Instância Gerenciada ou o Azure Synapse para impor uma política de acesso condicional.  

## <a name="prerequisites"></a>Pré-requisitos

- Você deve configurar o banco de dados SQL do Azure, o Azure SQL Instância Gerenciada ou o pool SQL dedicado no Azure Synapse para dar suporte à autenticação do Azure Active Directory (Azure AD). Para obter etapas específicas, consulte [configurar e gerenciar a autenticação de Azure Active Directory com o banco de dados SQL ou o Azure Synapse](authentication-aad-configure.md).  
- Quando a autenticação multifator está habilitada, você deve se conectar com uma ferramenta com suporte, como o SSMS (SQL Server Management Studio mais recente). Para obter mais informações, consulte [Configurar a autenticação multifator do Banco de Dados SQL do Azure para o SQL Server Management Studio](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Configurar o acesso condicional

> [!NOTE]
> O exemplo abaixo usa o banco de dados SQL do Azure, mas você deve selecionar o produto apropriado para o qual deseja configurar o acesso condicional.

1. Entre no portal do Azure, selecione **Azure Active Directory** e, em seguida, selecione **acesso condicional**. Para obter mais informações, consulte [Referência técnica do Acesso Condicional ao Azure Active Directory](../../active-directory/conditional-access/concept-conditional-access-conditions.md).  
   ![Folha acesso condicional](./media/conditional-access-configure/conditional-access-blade.png)

2. Na folha **Políticas de Acesso Condicional**, clique em **Nova política**, forneça um nome e, em seguida, clique em **Configurar regras**.  
3. Em **atribuições**, selecione **usuários e grupos**, marque **Selecionar usuários e grupos** e, em seguida, selecione o usuário ou grupo para acesso condicional. Clique em **Selecionar** e, em seguida, em **Concluído** para aceitar a seleção.  
   ![selecionar usuários e grupos](./media/conditional-access-configure/select-users-and-groups.png)  

4. Selecione **Aplicativos na nuvem** e clique em **Selecionar aplicativos**. Você verá todos os aplicativos disponíveis para acesso condicional. Selecione **Banco de Dados SQL do Azure**, na parte inferior clique em **Selecionar** e, em seguida, clique em **Concluído**.  
   ![selecionar Banco de Dados SQL](./media/conditional-access-configure/select-sql-database.png)  
   Se você não encontrar o **banco de dados SQL do Azure** listado na terceira captura de tela a seguir, conclua as seguintes etapas:
   - Conecte-se ao banco de dados no banco de dados SQL do Azure usando o SSMS com uma conta de administrador do Azure AD.  
   - Execute `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Entre no Azure AD e verifique se o banco de dados SQL do Azure, o SQL Instância Gerenciada ou o Azure Synapse estão listados nos aplicativos em sua instância do Azure AD.  

5. Selecione **Controles de acesso**, selecione **Conceder** e, em seguida, marque a política que você deseja aplicar. Para este exemplo, selecionamos **Exigir autenticação multifator**.  
   ![selecionar conceder acesso](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Resumo

O aplicativo selecionado (banco de dados SQL do Azure) usando Azure AD Premium, agora impõe a política de acesso condicional selecionada, a **autenticação multifator necessária.**

Para perguntas sobre o banco de dados SQL do Azure e o Azure Synapse sobre a autenticação multifator, entre em contato com <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>Próximas etapas  

Para obter um tutorial, consulte [proteger seu banco de dados no banco de dados SQL](secure-database-tutorial.md).