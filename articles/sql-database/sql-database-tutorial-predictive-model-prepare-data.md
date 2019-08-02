---
title: 'Tutorial: Preparar os dados para treinar um modelo preditivo no R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na primeira parte deste tutorial com três partes, você preparará os dados de um Banco de Dados SQL do Azure para treinar um modelo preditivo no R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/26/2019
ms.openlocfilehash: c1271d5b63fa796fe44b7a40c364953464a87539
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596675"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Preparar dados para treinar um modelo preditivo em R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Na primeira parte deste tutorial com três partes, você importará e preparará os dados de um Banco de Dados SQL do Azure usando o R. Mais adiante nesta série, você usará esses dados para treinar e implantar um modelo de machine learning no R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia).

Nesta série de tutoriais, imagine que você tem uma empresa de aluguel de esquis e quer prever a quantidade de aluguéis em uma data futura. Essas informações ajudarão a preparar seu estoque, a equipe e os recursos.

Na primeira e na segunda parte desta série, você desenvolverá alguns scripts do R no RStudio para preparar os dados e treinar um modelo de machine learning. Em seguida, na terceira parte, você executará esses scripts do R em um banco de dados SQL usando os procedimentos armazenados.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Importar um banco de dados de exemplo para um Banco de Dados SQL do Azure usando o R
> * Carregar os dados do Banco de Dados SQL do Azure em um dataframe do R
> * Preparar os dados no R identificando algumas colunas como categóricas

Na [parte 2](sql-database-tutorial-predictive-model-build-compare.md), você aprenderá a criar e treinar vários modelos de machine learning em R e, em seguida, escolher o mais preciso.

Na [parte três](sql-database-tutorial-predictive-model-deploy.md), você aprenderá a armazenar o modelo em um banco de dados e, em seguida, criar um procedimento armazenado dos scripts do R desenvolvidos nas partes anteriores. Os procedimentos armazenados serão executados em um banco de dados SQL para fazer previsões com base em novos dados.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – Caso você não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

* Servidor do Banco de Dados SQL do Azure com os Serviços do Machine Learning habilitados – Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

* Pacote de RevoScaleR – Confira [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) para ver opções de instalação local do pacote.

* IDE do R – Este tutorial usa o [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Ferramenta de consulta SQL – Este tutorial presume que você está usando o [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou o [SSMS](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SQL Server Management Studio).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importar o banco de dados de exemplo

O conjunto de dados de exemplo usado neste tutorial foi salvo em um arquivo de backup de banco de dados **.bacpac** para você baixar e usar.

1. Baixe o arquivo [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Siga as instruções em [Importar um arquivo BACPAC para criar um Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-import) usando estes detalhes:

   * Importar do arquivo **TutorialDB.bacpac** que você baixou
   * Durante a versão prévia pública, escolha a configuração **Gen5/vCore** para o novo banco de dados
   * Nomeie o novo banco de dados "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Carregue os dados em um dataframe

Para usar os dados no R, é necessário carregá-los do Banco de Dados SQL do Azure para o dataframe (`rentaldata`).

Crie um novo arquivo RScript no RStudio e execute o script a seguir. Substitua **Servidor**, **UID** e **PWD** com suas próprias informações de conexão.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

Você deverá ver resultados semelhantes ao seguinte.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>Preparar os dados

Neste banco de dados de exemplo, a maior parte da preparação já foi feita, mas você fará uma preparação a mais.
Use o seguinte script do R para identificar as três colunas como *categorias* alterando os tipos de dados para *fator*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Você deverá ver resultados semelhantes ao seguinte.

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

Os dados agora estão preparados para treinamento.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende continuar este tutorial, exclua o banco de dados TutorialDB do seu servidor do Banco de Dados SQL do Azure.

No portal do Azure, siga estas etapas:

1. No menu à esquerda no portal do Azure, clique em **Todos os recursos** ou **Bancos de dados SQL**.
1. No campo **Filtrar por nome…** , digite **TutorialDB** e selecione sua assinatura.
1. Selecione seu banco de dados TutorialDB.
1. Na página **Visão Geral**, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Na primeira parte desta série de tutoriais, você concluiu estas etapas:

* Importar um banco de dados de exemplo para um Banco de Dados SQL do Azure usando o R
* Carregar os dados do Banco de Dados SQL do Azure em um dataframe do R
* Preparar os dados no R identificando algumas colunas como categóricas

Para criar um modelo de machine learning que usa dados do banco de dados TutorialDB, siga a parte 2 desta série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Criar um modelo preditivo no R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-tutorial-predictive-model-build-compare.md)
