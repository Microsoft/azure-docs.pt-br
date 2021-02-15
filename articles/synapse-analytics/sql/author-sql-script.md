---
title: Scripts SQL no Synapse Studio
description: Introdução aos scripts do Synapse Studio SQL no Azure Synapse Analytics.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 4ed02901aa0d6948e9c6443e5bbcf4ebfbc872f7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118686"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>Scripts SQL do Synapse Studio no Azure Synapse Analytics 

O Synapse Studio fornece uma interface da Web de script SQL para que você crie consultas SQL. 

## <a name="begin-authoring-in-sql-script"></a>Iniciar a criação no script SQL 

Há várias maneiras de iniciar a experiência de criação no script SQL. Você pode criar um novo script SQL por meio de um dos métodos a seguir.

1. No menu desenvolver, selecione o ícone **"+"** e escolha **script SQL**.

2. No menu **ações** , escolha **novo script SQL**.

3. Escolha **importar** no menu **ações** em desenvolver scripts SQL. Selecione um script SQL existente do seu armazenamento local.
![novas ações do script SQL 3](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>Criar seu script SQL

1. Escolha um nome para o script SQL selecionando o botão de **Propriedade** e substituindo o nome padrão atribuído ao script SQL. 
![Nova renomeação de script SQL](media/author-sql-script/new-sql-script-rename.png)

2. Escolha o pool SQL dedicado específico ou o pool SQL sem servidor no menu suspenso **conectar-se a** . Ou, se necessário, escolha o banco de dados em **usar banco de dados**. 
![novo SQL escolher pool](media/author-sql-script/new-sql-choose-pool.png)

3. Comece a criar seu script SQL usando o recurso IntelliSense.

## <a name="run-your-sql-script"></a>Executar o script SQL

Selecione o botão **executar** para executar o script SQL. Os resultados são exibidos por padrão em uma tabela.

![nova tabela de resultados de script SQL](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>Exportar seus resultados

Você pode exportar os resultados para o armazenamento local em formatos diferentes (incluindo CSV, Excel, JSON, XML) selecionando "exportar resultados" e escolhendo a extensão.

Você também pode visualizar os resultados do script SQL em um gráfico selecionando o botão **gráfico** . Selecione o "tipo de gráfico" e a **coluna de categoria**. Você pode exportar o gráfico para uma imagem selecionando **salvar como imagem**. 

![novo gráfico de resultados de script SQL](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Explorar dados de um arquivo parquet

Você pode explorar os arquivos parquet em uma conta de armazenamento usando o script SQL para visualizar o conteúdo do arquivo.

![novo script sqlod parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabelas SQL, tabelas externas, exibições

Selecionando o menu **ações** em dados, você pode selecionar várias ações, como:

- Novo script SQL
- Selecione as 1000 principais linhas
- CREATE
- REMOVER e criar 
 
Explore o gesto disponível clicando com o botão direito do mouse nos nós de bancos de dados SQL.
 
![novo banco de dados de script](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>Criar pastas e mover scripts SQL para uma pasta

No menu Ações, em desenvolver scripts SQL, escolha "nova pasta" no menu "ações" em desenvolver scripts SQL. E digite o nome da nova pasta na janela pop-up. 

> [!div class="mx-imgBorder"] 
> ![Captura de tela que mostra um exemplo de um script SQL com ' nova pasta ' selecionada.](./media/author-sql-script/new-sql-script-create-folder.png)

Para mover um script SQL para uma pasta, você pode selecionar o script SQL e escolher "mover para" no menu ações. Em seguida, localize a pasta de destino na nova janela e mova o script SQL para a pasta selecionada. Você também pode arrastar rapidamente o script SQL e soltá-lo em uma pasta.  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como criar um script SQL, consulte [Azure Synapse Analytics](../index.yml).