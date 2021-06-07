---
title: Conectar-se ao SQL do Synapse com o Azure Data Studio
description: Use o Azure Data Studio para se conectar ao SQL do Synapse e consultá-lo no Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 1529e45bd7f799a727a29c8c2e26f7ed77c4e2a0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565586"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio"></a>Conectar-se ao SQL do Synapse com o Azure Data Studio

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Use o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio?view=azure-sqldw-latest&preserve-view=true) para se conectar ao SQL do Synapse e consultá-lo no Azure Synapse Analytics. 

## <a name="connect"></a>Conectar

Para se conectar ao Synapse SQL, abra o Azure Data Studio e selecione **Nova Conexão**.

![Abrir o Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Escolha **Microsoft SQL Server** como o **Tipo de conexão**.

A conexão precisará dos seguintes parâmetros:

* **Servidor:** Servidor no formato `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net
* **Banco de dados:** Nome do banco de dados

> [!NOTE]
> Se você quiser usar o **pool de SQL sem servidor**, a URL deverá ser semelhante a:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Se você quiser usar o **pool de SQL dedicado**, a URL deverá ser semelhante a:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Escolha **Autenticação do Windows**, **Azure Active Directory** ou **Logon do SQL** como o **Tipo de Autenticação**.

Para usar **Logon do SQL** como o tipo de autenticação, adicione os parâmetros nome de usuário/senha:

* **Usuário:** Usuário do servidor no formato `<User>`
* **Senha:** Senha associada ao usuário

Para usar o Azure Active Directory, você precisa escolher o tipo de autenticação necessário.

![Autenticação do AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

A seguinte captura de tela mostra os **Detalhes da Conexão** da **Autenticação do Windows**:

![Autenticação do Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

A seguinte captura de tela mostra os **Detalhes da Conexão** usando o **Logon do SQL**:

![Logon do SQL](./media/get-started-azure-data-studio/2-database-details.png)

Após o logon bem-sucedido, você deverá ver um painel como este: ![Painel](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Consulta

Uma vez conectado, você pode consultar o Synapse SQL usando instruções [T-SQL (Transact-SQL)](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) compatíveis com a instância. Selecione **Nova consulta** na exibição do painel para começar.

![Nova Consulta](./media/get-started-azure-data-studio/5-new-query.png)

Por exemplo, você pode usar a seguinte instrução Transact-SQL para [consultar arquivos Parquet](query-parquet-files.md) usando o pool de SQL sem servidor:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Próximas etapas 
Explore outras maneiras de se conectar ao Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)

Acesse [Usar o Azure Data Studio para se conectar e consultar dados usando um pool de SQL dedicado no Azure Synapse Analytics](/sql/azure-data-studio/quickstart-sql-dw) para obter mais informações.
