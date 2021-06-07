---
title: 'Tutorial: Introdução à análise de dados com pools de SQL dedicados'
description: Neste tutorial, você usará os dados de exemplo de táxi de NYC para explorar as funcionalidades analíticas do pool de SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: a1f15330a912c8a8a93fe1f74e88ef8d117441c2
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047891"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analisar dados com pools de SQL dedicados

Neste tutorial, você usará os dados de táxis de Nova York para explorar as funcionalidades do pool de SQL dedicado.

## <a name="create-a-dedicated-sql-pool"></a>Criar um pool de SQL dedicado

1. No Synapse Studio, no painel do lado esquerdo, selecione **Gerenciar** > **Pools de SQL**.
1. Selecione **Novo**
1. Em **Nome do pool de SQL**, selecione **SQLPOOL1**
1. Em **Nível de desempenho**, escolha **DW100C**
1. Selecione **Examinar + criar** > **Criar**. Seu pool de SQL dedicado estará pronto em alguns minutos. 

O pool de SQL dedicado é associado a um Banco de Dados SQL também chamado **SQLPOOL1**.
1. Acesse **Dados** > **Workspace**.
1. Você deverá ver um banco de dados chamado **SQLPOOL1**. Se você não vir o comando, clique em **Atualizar**.

Um pool de SQL dedicado consome recursos faturáveis desde que ele esteja ativo. Você pode pausar o pool posteriormente para reduzir custos.

> [!NOTE] 
> Quando você criar um pool de SQL dedicado (antigo SQL DW) em seu workspace, a página de provisionamento do pool de SQL dedicado será aberta. O provisionamento ocorrerá no SQL Server lógico.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Carregar os dados de Táxi de Nova York no SQLPOOL1

1. No Synapse Studio, procure o hub **Desenvolver**, clique no botão **+** para adicionar um novo recurso e crie um script SQL.
1. Selecione o pool 'SQLPOOL1' (pool criado na [ETAPA 1](./get-started-create-workspace.md) deste tutorial) na lista suspensa 'Conectar-se a' acima do script.
1. Insira o seguinte código:
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. Clique no botão Executar para executar o script.
1. Esse script será concluído em menos de 60 segundos. Ele carrega 2 milhões linhas de dados de Táxis de Nova York em uma tabela chamada **dbo.Trip**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Explorar os dados de táxis de Nova York no pool de SQL dedicado

1. No Synapse Studio, acesse o hub **Dados**.
1. Acesse **SQLPOOL1** > **Tabelas**. 
3. Clique com o botão direito do mouse na tabela **dbo.Trip** e selecione **Novo Script de SQL** > **Selecionar as Primeiras 100 Linhas**.
4. Aguarde enquanto um novo script SQL é criado e executado.
5. Observe na parte superior do script de SQL que **Conectar-se ao** é automaticamente definido como o pool de SQL chamado **SQLPOOL1**.
6. Substitua o texto do script de SQL por esse código e execute-o.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Essa consulta mostra como as distâncias totais de viagem e a distância média da viagem estão relacionadas ao número de passageiros.
1. Na janela de resultados do script de SQL, altere a opção **Exibição** para **Gráfico** para uma visualização dos resultados como um gráfico de linhas.
    
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analisar dados em uma conta do Armazenamento do Azure](get-started-analyze-storage.md)
