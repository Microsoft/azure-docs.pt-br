---
title: 'Tutorial: Implantar um modelo preditivo no R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na terceira parte deste tutorial, você implantará um modelo preditivo no R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia).
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
ms.openlocfilehash: 9fa816b2a8e736f03c99b66b898f48bd2a483b31
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596781"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Implantar um modelo preditivo no R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Na terceira parte deste tutorial, você implantará um modelo preditivo desenvolvido no R em um banco de dados SQL com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia).

Você criará um procedimento armazenado com um script de R incorporado que faz previsões usando o modelo. Como seu modelo é executado no Banco de Dados SQL do Azure, ele pode ser facilmente treinado nos dados armazenados no banco de dados.

Neste artigo, usando os scripts R que você desenvolveu nas partes anteriores, você aprenderá a:

> [!div class="checklist"]
> * Criar um procedimento armazenado que gera o modelo de machine learning
> * Armazenar o modelo em uma tabela de banco de dados
> * Criar um procedimento armazenado que faz previsões usando o modelo
> * Executar o modelo com novos dados

Na [parte 1](sql-database-tutorial-predictive-model-prepare-data.md), você aprendeu a importar um banco de dados de exemplo e, em seguida, a preparar os dados para serem usados no treinamento de um modelo preditivo em R.

Na [parte 2](sql-database-tutorial-predictive-model-build-compare.md), você aprendeu a criar e treinar vários modelos de machine learning em R e, em seguida, escolheu o mais preciso.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Na parte 3 deste tutorial, assumimos que você concluiu a [**parte 1**](sql-database-tutorial-predictive-model-prepare-data.md) e a [**parte 2**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Criar um procedimento armazenado que gera o modelo

Na segunda parte desta série de tutoriais, você decidiu que um modelo de árvore de decisão (dtree) era o mais preciso. Agora, com os scripts R desenvolvidos, crie um procedimento armazenado (`generate_rental_rx_model`) que treina e gera o modelo dtree usando o rxDTree do pacote RevoScaleR.

Execute os seguintes comandos no Azure Data Studio ou no SSMS.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Armazenar o modelo em uma tabela de banco de dados

Crie uma tabela no banco de dados TutorialDB e salve o modelo na tabela.

1. Crie uma tabela (`rental_rx_models`) para armazenar o modelo.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Salve o modelo na tabela como um objeto binário, com o nome de modelo "rxDTree".

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Criar um procedimento armazenado que faz previsões

Crie um procedimento armazenado (`predict_rentalcount_new`) que faz previsões usando o modelo treinado e um conjunto de dados novos.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Executar o modelo com novos dados

Agora, é possível usar o procedimento armazenado `predict_rentalcount_new` para prever o valor do aluguel com os novos dados.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Você verá um resultado semelhante ao seguinte.

```results
RentalCount_Predicted
332.571428571429
```

Você criou, treinou e implantou com êxito um modelo em um Banco de Dados SQL do Azure. Em seguida, você usou esse modelo em um procedimento armazenado para prever valores com base em novos dados.

## <a name="clean-up-resources"></a>Limpar recursos

Ao terminar de usar o banco de dados TutorialDB, exclua-o do servidor do Banco de Dados SQL do Azure.

No portal do Azure, siga estas etapas:

1. No menu à esquerda no portal do Azure, clique em **Todos os recursos** ou **Bancos de dados SQL**.
1. No campo **Filtrar por nome…** , digite **TutorialDB** e selecione sua assinatura.
1. Selecione seu banco de dados TutorialDB.
1. Na página **Visão Geral**, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Na terceira parte desta série de tutoriais, você concluiu estas etapas:

* Criar um procedimento armazenado que gera o modelo de machine learning
* Armazenar o modelo em uma tabela de banco de dados
* Criar um procedimento armazenado que faz previsões usando o modelo
* Executar o modelo com novos dados

Para saber mais sobre como usar o R nos Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia), confira:

* [Escrever funções do R avançadas em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-functions.md)
* [Trabalhar usando dados do R e do SQL nos Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-data-issues.md)
* [Adicionar um pacote R aos Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-add-r-packages.md)
