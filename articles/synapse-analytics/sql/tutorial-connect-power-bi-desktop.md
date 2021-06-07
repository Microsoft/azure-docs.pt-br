---
title: 'Tutorial: conectar o pool de SQL sem servidor ao Power BI Desktop e criar um relatório'
description: Neste tutorial, saiba como conectar o pool de SQL sem servidor no Azure Synapse Analytics ao Power BI Desktop e criar um relatório de demonstração com base em uma exibição.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8dd3edd25d21bfcd0fde1bc8b5f103877d968c8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119978"
---
# <a name="tutorial-use-serverless-sql-pool-with-power-bi-desktop--create-a-report"></a>Tutorial: usar o pool de SQL sem servidor com o Power BI Desktop e criar um relatório

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar um banco de dados de demonstração
> - Criar uma exibição usada para relatório
> - Conectar o Power BI Desktop ao pool SQL sem servidor
> - Criar um relatório com base na exibição

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- [Power BI Desktop](https://powerbi.microsoft.com/downloads/) – necessário para visualizar os dados e criar um relatório.
- [Workspace do Azure Synapse](../get-started-create-workspace.md) – necessário para criar um banco de dados, uma fonte de dados externa e uma exibição.

Opcional:

- Uma ferramenta de consulta SQL, como o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) ou o [SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms).

Valores para os seguintes parâmetros:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço do ponto de extremidade de serviço do pool de SQL sem servidor    | Usado como o nome do servidor                                   |
| Região do ponto de extremidade de serviço do pool de SQL sem servidor     | Usada para determinar o armazenamento usado nos exemplos |
| Nome de usuário e senha para acesso de ponto de extremidade | Usado para acessar o ponto de extremidade                               |
| Banco de dados que você usará para criar exibições     | O banco de dados usado como ponto de partida nas amostras       |

## <a name="1---create-database"></a>1 – Criar banco de dados

Para o ambiente de demonstração, crie seu banco de dados de demonstração. Você usa esse banco de dados para ver metadados, não para armazenar dados reais.

Crie o banco de dados de demonstração (e remova um banco de dados existente, se necessário) executando o seguinte script T-SQL (Transact-SQL):

```sql
-- Drop database if it exists
DROP DATABASE IF EXISTS Demo
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-data-source"></a>2 – Criar fonte de dados

Uma fonte de dados é necessária para que o serviço de pool de SQL sem servidor acesse arquivos no armazenamento. Crie a fonte de dados para uma conta de armazenamento que está localizada na mesma região que o seu ponto de extremidade. Embora o pool de SQL sem servidor possa acessar contas de armazenamento de diferentes regiões, ter o armazenamento e o ponto de extremidade na mesma região proporcionará um melhor desempenho.

Crie a fonte de dados executando o seguinte script T-SQL (Transact-SQL):

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a secret.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="3---prepare-view"></a>3 – Preparar exibição

Crie a exibição com base nos dados de demonstração externos para o Power BI consumir, executando o seguinte script T-SQL (Transact-SQL):

Crie a exibição `usPopulationView` dentro do banco de dados `Demo` com a seguinte consulta:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS uspv;
```

Os dados de demonstração contêm os seguintes conjuntos de dados:

A população dos EUA classificada por gênero e por raça para cada condado do país, obtida do censo decenal 2000 e 2010 no formato Parquet.

| Caminho da pasta                                                  | Descrição                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | Pasta pai para dados na conta de armazenamento de demonstração               |
| /release/us_population_county/                               | Arquivos de dados da população dos EUA no formato Parquet, particionados por ano, usando o esquema de particionamento do Hive/Hadoop. |

## <a name="4---create-power-bi-report"></a>4 – Criar relatório do Power BI

Crie o relatório do Power BI Desktop usando as seguintes etapas:

1. Abra o aplicativo Power BI Desktop e selecione **Obter dados**.

   ![Abra o aplicativo Power BI Desktop e selecione Obter dados.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Selecione **Azure** > **Banco de Dados SQL do Azure**. 

   ![Selecione uma fonte de dados.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Digite o nome do servidor no qual o banco de dados está localizado no campo **Servidor** e digite `Demo` no nome do banco de dados. Selecione a opção **Importar** e, em seguida, selecione **OK**. 

   ![Selecione o banco de dados no ponto de extremidade.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Selecione o método de autenticação preferido:

    - Exemplo para o AAD 
  
        ![Clique em Entrar.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Exemplo para logon do SQL – Digite seu nome de usuário e senha.

        ![Use o logon do SQL.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Selecione a exibição `usPopulationView` e, em seguida, selecione **Carregar**. 

   ![Selecione uma exibição no banco de dados selecionado.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Aguarde a conclusão da operação e, em seguida, um pop-up será exibido informando `There are pending changes in your queries that haven't been applied`. Selecione **Aplicar alterações**. 

   ![Clique em Aplicar alterações.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Aguarde até que a caixa de diálogo **Aplicar alterações de consulta** desapareça, o que pode levar alguns minutos. 

   ![Aguarde até que uma consulta seja concluída.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Quando o carregamento for concluído, selecione as seguintes colunas nesta ordem para criar o relatório:
   - countyName
   - população
   - stateName

   ![Selecione as colunas de interesse para gerar um relatório em formato de mapa.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar de usar este relatório, exclua os recursos realizando as seguintes etapas:

1. Excluir a credencial para a conta de armazenamento

   ```sql
   DROP EXTERNAL DATA SOURCE AzureOpenData
   ```

2. Excluir a exibição

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Remover o banco de dados

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Próximas etapas

Avance para [Consultar arquivos de armazenamento](develop-storage-files-overview.md) para saber como consultar arquivos de armazenamento usando o Synapse SQL.