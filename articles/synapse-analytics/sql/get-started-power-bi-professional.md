---
title: Conectar-se ao Synapse SQL com o Power BI Professional
description: Neste tutorial, percorreremos as etapas de como conectar o Power BI Desktop ao pool de SQL sem servidor.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: b33edb99109b7516e1792497a936031cf954bc15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451604"
---
# <a name="connect-to-serverless-sql-pool-with-power-bi-professional"></a>Conectar-se ao pool de SQL sem servidor com o Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

Neste tutorial, percorreremos as etapas para conectar o Power BI Desktop ao pool de SQL sem servidor.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa das seguintes ferramentas para emitir consultas:

- Cliente SQL de sua escolha:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI Desktop instalado

Parâmetros:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço do ponto de extremidade de serviço do pool de SQL sem servidor    | Será usado como o nome do servidor                                   |
| Região do ponto de extremidade de serviço do pool de SQL sem servidor     | Será usada para determinar qual armazenamento será usado nas amostras |
| Nome de usuário e senha para acesso de ponto de extremidade | Será usado para acessar o ponto de extremidade                               |
| Banco de dados que você usará para criar exibições       | Esse banco de dados será usado como ponto de partida em exemplos       |

## <a name="first-time-setup"></a>Configuração inicial

Há duas etapas antes de usar exemplos:

1. Criar um banco de dados para suas exibições
2. Criar credenciais a serem usadas pelo pool de SQL sem servidor para acessar arquivos no armazenamento

### <a name="create-database"></a>Criar banco de dados

Para este artigo de introdução, você deve criar um banco de dados próprio para utilizar como uma demonstração. Um banco de dados é necessário para a criação de exibições. Você usará esse banco de dados em algumas das consultas de exemplo desta documentação.

> [!NOTE]
> Bancos de dados são usados apenas para exibição de metadados, não para os dados reais.
>
> Anote o nome do banco de dados que você está usando, você precisará disso mais tarde.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Criar credenciais

Precisaremos criar credenciais antes que você execute consultas. Esta credencial será usada pelo serviço de pool de SQL sem servidor para acessar arquivos no armazenamento.

> [!NOTE]
> Você precisa criar credenciais de acesso à conta de armazenamento. Embora o pool de SQL sem servidor possa acessar armazenamentos de diferentes regiões, ter o armazenamento e o workspace do Azure Synapse na mesma região proporcionará uma experiência com melhor desempenho.

**Snippet de código sobre como criar credenciais para contêineres de dados do censo**, execute:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="create-a-power-bi-desktop-report"></a>Criar um relatório do Power BI Desktop

Abra o aplicativo Power BI Desktop e selecione a opção **Obter dados**.

![Abra o aplicativo Power BI Desktop e selecione Obter dados.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Etapa 1 – Selecionar fonte de dados

Selecione **Azure** no menu e, em seguida, **Banco de Dados SQL do Azure**.
![Selecione uma fonte de dados.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Etapa 2 – Selecionar banco de dados

Grave a URL do banco de dados e o nome do banco de dados em que a exibição reside.
![Selecione o banco de dados no ponto de extremidade.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Próximas etapas

Avance para [Arquivos de armazenamento de consulta](get-started-azure-data-studio.md) para saber como se conectar ao pool de SQL sem servidor usando o Azure Data Studio.