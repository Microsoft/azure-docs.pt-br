---
title: Analisar dados com o Azure Machine Learning | Microsoft Docs
description: Use o Azure Machine Learning para compilar um modelo de aprendizado de máquina preditivo com base nos dados armazenados no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 03/22/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 7f9500adc6871c4c9f81c32bf456bc36cf91db4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439846"
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
2. Clique em **+ novo** na parte inferior esquerda da tela e selecione **experimento em branco**.
3. Digite um nome para o seu experimento: Marketing Direcionado.
4. Arraste o **importar dados** módulo sob **dados de entrada e saída** do painel de módulos na tela.
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

1. Arraste o **selecionar colunas no conjunto de dados** módulo sob **transformação de dados < manipulação** na tela. Conectar-se esse módulo para o **importação de dados** módulo.
2. Clique em **Iniciar seletor de colunas** no painel Propriedades para especificar quais colunas você deseja remover.
   ![Colunas do Projeto][4]
3. Exclua duas colunas: CustomerAlternateKey e GeographyKey.
   ![Remover colunas desnecessárias][5]

## <a name="3-build-the-model"></a>3. Compilar o modelo
Dividiremos os dados em partes de 80 e 20%: 80% para treinar um modelo de machine learning e 20% para testá-lo. Usaremos os algoritmos de “Duas Classes” para esse problema de classificação binária.

1. Arraste o módulo **Divisão** na tela.
2. No painel Propriedades, insira 0.8 para fração de linhas no primeiro conjunto de dados de saída.
   ![Dividir os dados em conjuntos de treinamento e teste][6]
3. Arraste o módulo **Árvore de Decisão Aumentada de duas classes** na tela.
4. Arraste o **treinar modelo** módulo na tela e especificar entradas ao conectá-lo para o **árvore de decisão aumentada duas classes** (algoritmo de ML) e **divisão** (dados para treinar o módulos de algoritmo em). 
     ![Conectar o módulo Treinar Modelo][7]
5. Depois, clique em **Iniciar seletor de coluna** no painel de Propriedades. Selecione a coluna **BikeBuyer** como a coluna a ser prevista.
   ![Selecionar a Coluna a prever][8]

## <a name="4-score-the-model"></a>4. Pontuar o modelo
Agora, testaremos o desempenho do modelo nos dados de teste. Vamos comparar o algoritmo de nossa escolha com um algoritmo diferente para ver que tem um desempenho melhor.

1. Arraste **modelo de pontuação** módulo na tela e conectá-lo ao **modelo de treinamento** e **dividir dados** módulos.
   ![O modelo de pontuação][9]
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
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2-visualize-new.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
