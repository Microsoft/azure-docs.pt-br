---
title: Analisar dados com o Azure Machine Learning | Microsoft Docs
description: "Use o Azure Machine Learning para compilar um modelo de aprendizado de máquina preditivo com base nos dados armazenados no SQL Data Warehouse do Azure."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 95635460-150f-4a50-be9c-5ddc5797f8a9
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 03/02/2017
ms.author: kevin;barbkess
ms.openlocfilehash: 3197948e32fe5c95b111fe5495a0e5f85966a24b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analisar dados com o Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Este tutorial usa o Azure Machine Learning para compilar um modelo de aprendizado de máquina preditivo com base nos dados armazenados no SQL Data Warehouse do Azure. Especificamente, isso compila uma campanha de marketing direcionado da Adventure Works, uma loja de bicicletas, prevendo se um cliente tem probabilidade de comprar uma bicicleta ou não.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para acompanhar este tutorial, você precisará:

* Um SQL Data Warehouse pré-carregado com os dados de exemplo do AdventureWorksDW. Para provisionar isso, consulte [Criar um SQL Data Warehouse][Create a SQL Data Warehouse] e opte por carregar os dados de exemplo. Se você já tiver um data warehouse, mas não tiver dados de exemplo, poderá [carregar dados de exemplo manualmente][load sample data manually].

## <a name="1-get-the-data"></a>1. Obter os dados
Os dados estão na exibição dbo.vTargetMail no banco de dados AdventureWorksDW. Para ler esses dados:

1. Entre no [Azure Machine Learning Studio][Azure Machine Learning studio] e clique em Meus Testes.
2. Clique em **+NOVO** e selecione **Teste em Branco**.
3. Insira um nome para o seu teste: Marketing Direcionado.
4. Arraste o módulo **Leitor** do painel de módulos na tela.
5. Especifique os detalhes do seu banco de dados do SQL Data Warehouse no painel Propriedades.
6. Especifique a **consulta** do banco de dados para ler os dados de interesse.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Execute o teste clicando em **Executar** na tela do teste.
![Executar o teste][1]

Após o teste terminar de ser executado com êxito, clique na porta de saída na parte inferior do módulo Leitor e selecione **Visualizar** para ver os dados importados.
![Exibir dados importados][3]

## <a name="2-clean-the-data"></a>2. Limpar os dados
Para limpar os dados, remova algumas colunas que não são relevantes para o modelo. Para fazer isso:

1. Arraste o módulo **Colunas do Projeto** na tela.
2. Clique em **Iniciar seletor de colunas** no painel Propriedades para especificar quais colunas você deseja remover.
   ![Colunas do Projeto][4]
3. Exclua duas colunas: CustomerAlternateKey e GeographyKey.
   ![Remover colunas desnecessárias][5]

## <a name="3-build-the-model"></a>3. Compilar o modelo
Dividiremos os dados 80-20: 80% para treinar um modelo de aprendizado de máquina e 20% para testar o modelo. Usaremos os algoritmos de “Duas Classes” para esse problema de classificação binária.

1. Arraste o módulo **Divisão** na tela.
2. Insira 0.8 para a Fração de linhas no primeiro conjunto de dados de saída no painel Propriedades.
   ![Dividir os dados em conjuntos de treinamento e teste][6]
3. Arraste o módulo **Árvore de Decisão Aumentada de duas classes** na tela.
4. Arraste o módulo **Modelo de Treinamento** na tela e especifique as entradas. Depois, clique em **Iniciar seletor de coluna** no painel de Propriedades.
   * Primeira entrada: algoritmo de ML.
   * Segunda entrada: dados para treinar o algoritmo.
     ![Conectar o módulo Treinar Modelo][7]
5. Selecione a coluna **BikeBuyer** como a coluna a ser prevista.
   ![Selecionar a Coluna a prever][8]

## <a name="4-score-the-model"></a>4. Pontuar o modelo
Agora, testaremos o desempenho do modelo nos dados de teste. Vamos comparar o algoritmo de nossa escolha com um algoritmo diferente para ver que tem um desempenho melhor.

1. Arraste o módulo **Modelo de Pontuação** na tela.
    Primeira entrada: modelo treinado Segunda entrada: dados de teste ![Pontuar o modelo][9]
2. Arraste o **Computador de Ponto de Bayes de Duas Classes** na tela de teste. Vamos comparar o desempenho desse algoritmo em comparação com a Árvore de Decisão Aumentada de Duas Classes.
3. Copie e cole os módulos Modelo de Treinamento e Modelo de Pontuação na tela.
4. Arraste o módulo **Avaliar Modelo** na tela para comparar os dois algoritmos.
5. **Execute** o teste.
   ![Executar o teste][10]
6. Clique na porta de saída na parte inferior do módulo Avaliar Modelo e clique em Visualizar.
   ![Visualizar os resultados de avaliação][11]

As métricas fornecidas são a curva ROC, o diagrama de comparação de precisão e recolhimento e a curva de comparação de precisão. Ao examinar essas métricas, podemos ver que o primeiro modelo teve um desempenho melhor do que o segundo. Para ver o que o primeiro modelo previu, clique na porta de saída do Modelo de Pontuação e clique em Visualizar.
![Visualizar os resultados da pontuação][12]

Você verá duas ou mais colunas adicionadas ao seu conjunto de dados de teste.

* Probabilidades Pontuadas: a probabilidade de que um cliente é um comprador de bicicleta.
* Rótulos Pontuados: a classificação feita pelo modelo – comprador de bicicleta (1) ou não (0). Esse limite de probabilidade para a rotulagem é definido como 50% e pode ser ajustado.

Ao comparar a coluna BikeBuyer (real) com os Rótulos Pontuados (previsão), é possível ver o desempenho do modelo. Como as próximas etapas, você pode usar esse modelo para fazer previsões para novos clientes e publicar esse modelo como um serviço Web ou gravar os resultados de volta no SQL Data Warehouse.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a criação de modelos de aprendizado de máquina de previsão, consulte [Introdução ao Machine Learning no Azure][Introduction to Machine Learning on Azure].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
