---
title: Visualizar os dados do SQL Data Warehouse com o Power BI | Microsoft Azure
description: Visualizar os dados do SQL Data Warehouse com o Power BI
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b2377466ba830eed9fed0e9a4033b3eca3d351c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474610"
---
# <a name="visualize-data-with-power-bi"></a>Visualizar os dados com o Power BI
Este tutorial mostra como usar o Power BI para se conectar ao SQL Data Warehouse e criar algumas visualizações básicas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para acompanhar este tutorial, você precisará:

* Um SQL Data Warehouse pré-carregado com o banco de dados AdventureWorksDW. Para provisionar um data warehouse, consulte [Criar um SQL Data Warehouse](create-data-warehouse-portal.md) e opte por carregar os dados de exemplo. Se você já tiver um data warehouse, mas não tiver dados de exemplo, poderá [carregar WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Conectar-se ao seu banco de dados
Para abrir o Power BI e conectar-se ao banco de dados AdventureWorksDW:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **bancos de dados SQL** e escolha o banco de dados AdventureWorks do SQL Data Warehouse.
   
    ![Localize o banco de dados](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Clique no botão “Abrir no Power BI”.
   
    ![Botão Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Agora você deve ver a página de conexão do SQL Data Warehouse exibindo o endereço Web do seu banco de dados. Clique em Avançar.
   
    ![Conexão do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Digite o nome de usuário e a senha do servidor do Azure SQL.
   
    ![Entrada do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Para abrir o banco de dados, clique no conjunto de dados do AdventureWorksDW na folha esquerda.
   
    ![Abrir AdventureWorksDW no Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Criar um relatório
Agora você está pronto para usar o Power BI para analisar os dados de exemplo do AdventureWorksDW. Para executar a análise, o AdventureWorksDW possui uma exibição chamada AggregateSales. Essa exibição contém algumas das principais métricas para analisar as vendas da empresa.

1. Para criar um mapa do valor de vendas de acordo com o CEP, clique no painel de campos à direita, clique na exibição AggregateSales para expandi-la. Clique nas colunas PostalCode e SalesAmount para selecioná-las.
   
    ![O Power BI seleciona AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    O Power BI reconhece automaticamente os dados geográficos e os coloca em um mapa para você.
   
    ![Mapa do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Esta etapa cria um grafo de barras que mostra o valor de vendas por receita de cliente. Para criar o gráfico de barras, acesse a exibição AggregateSales expandida. Clique no campo SalesAmount. Arraste o campo Receita de Cliente para a esquerda e solte-o no Eixo.
   
    ![O Power BI seleciona o eixo](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    O gráfico de barras à esquerda.
   
    ![Barra do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Esta etapa cria um gráfico de linhas que mostra o valor de vendas por data do pedido. Para criar o gráfico de linhas, acesse a exibição AggregateSales expandida. Clique em SalesAmount e em OrderDate. Na coluna Visualizações, clique no ícone Gráfico de Linhas, que é o primeiro ícone na segunda linha nas visualizações.
   
    ![O Power BI selecione o gráfico de linhas](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Agora você tem um relatório que mostra três visualizações diferentes dos dados.
   
    ![Linha do Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Você pode salvar seu andamento a qualquer momento clicando em **Arquivo** e selecionando **Salvar**.

## <a name="using-direct-connect"></a>Usar a Conexão Direta
Assim como o Banco de Dados SQL do Azure, a Conexão Direta do SQL Data Warehouse permite uma aplicação lógica junto com os recursos analíticos do Power BI. Com a Conexão Direta, as consultas são enviadas de volta para o SQL Data Warehouse do Azure em tempo real enquanto você explora os dados.  Esse recurso, combinado com a escala do SQL Data Warehouse, permite aos usuários criar relatórios dinâmicos em minutos contra terabytes de dados. Além disso, a introdução do botão Abrir no Power BI permite que os usuários conectem o Power BI diretamente ao respectivo SQL Data Warehouse sem coletar informações de outras partes do Azure.

Ao usar a Conexão Direta:

* Especifique o nome do servidor totalmente qualificado ao conectar.
* Verifique se as regras de firewall para o banco de dados estão configuradas como Permitir acesso aos serviços do Azure.
* Cada ação, como selecionar uma coluna ou adicionar um filtro, consultará diretamente o data warehouse.
* Blocos são atualizados automaticamente e aproximadamente a cada 15 minutos.
* Perguntas e respostas não estão disponíveis para conjuntos de dados de Conexão Direta.
* As alterações de esquema são incorporadas automaticamente.
* Todas as consultas de Conexão Direta atingirão o tempo limite após 2 minutos.

Essas restrições e observações podem mudar à medida que aprimoramos as experiências.

## <a name="next-steps"></a>Próximas etapas
Agora que proporcionamos a você uma introdução aos dados de exemplo, veja como [desenvolver](sql-data-warehouse-overview-develop.md), [carregar](design-elt-data-loading.md) ou [migrar](sql-data-warehouse-overview-migrate.md). Ou então, confira o [Site do Power BI](https://www.powerbi.com/).
