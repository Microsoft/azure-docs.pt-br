---
title: Como programar indexadores - Azure Search
description: Programe indexadores do Azure Search para indexar o conteúdo periodicamente ou em horários específicos.
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 4bf931b19b7490a94f30afde49038cdc7573fab3
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302237"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Como agendar indexadores do Azure Search
Um indexador normalmente é executado uma vez, imediatamente após sua criação. Você pode executá-lo novamente sob demanda usando o portal, a API REST ou o SDK do .NET. Você também pode configurar um indexador para ser executado periodicamente em um agendamento.

Algumas situações em que o agendamento do indexador é útil:

* Fonte de dados será alterado ao longo do tempo, e você deseja que os indexadores de Azure Search para processar automaticamente os dados alterados.
* O índice será populado de várias fontes de dados e você deseja certificar-se de que os indexadores são executados em momentos diferentes para reduzir conflitos.
* Os dados de origem são muito grande e você deseja distribuir pelo indexador ao longo do tempo de processamento. Para obter mais informações sobre como indexar grandes volumes de dados, consulte [como indexar grandes conjuntos de dados no Azure Search](search-howto-large-index.md).

O Agendador é um recurso interno do Azure Search. Você não pode usar um agendador externo para controlar a indexadores de pesquisa.

## <a name="define-schedule-properties"></a>Definir propriedades da agenda

Uma agenda do indexador tem duas propriedades:
* **Intervalo de**, que define a quantidade de tempo entre execuções do indexador de agendada. O menor intervalo permitido é de 5 minutos e o maior é de 24 horas.
* **Iniciar o UTC (tempo)** , que indica a primeira vez em que o indexador deve ser executado.

Você pode especificar um agendamento ao criar pela primeira vez o indexador ou atualizando propriedades do indexador mais tarde. Agendas do indexador podem ser definidas usando o [portal](#portal), o [API REST](#restApi), ou o [SDK do .NET](#dotNetSdk).

Só é possível ocorrer uma execução de um indexador por vez. Se um indexador já está em execução quando sua próxima execução está agendada, essa execução é adiada até a próxima hora agendada.

Vamos considerar um exemplo para tornar isso mais concreto. Suponha que podemos configurar uma agenda de indexador com um **intervalo** de cada hora e uma **Start Time** de 1 de junho de 2019 às 8:00:00 AM UTC. Aqui está o que poderia acontecer quando a execução de um indexador demora mais do que uma hora:

* A primeira execução do indexador começa em, ou em torno de 1 de junho de 2019 às 8:00 AM UTC. Vamos supor que essa execução demore 20 minutos (ou menos de uma hora).
* A segunda execução começa em, ou em torno de 1 de junho de 2019 9:00 AM UTC. Suponha que essa execução demore 70 minutos - mais de uma hora – e não será concluído até 10 10h UTC.
* A terceira execução está agendada para iniciar às 10:00 AM UTC, mas no momento a execução anterior ainda está em execução. Isso agendada execução, em seguida, é ignorada. A próxima execução do indexador não será iniciado até 11 horas UTC.

> [!NOTE]
> Se um indexador é definido para uma determinada agenda mas repetidamente falhar no mesmo documenta repetidamente cada vez que ele é executado, o indexador será iniciada executando em um intervalo menos frequente (até o máximo de pelo menos uma vez a cada 24 horas) com êxito até que ele faz aga de andamento no.  Se você acredita que corrigir qualquer problema que fazia com que o indexador ficar preso em um determinado ponto, você pode executar um em demanda tempo de execução do indexador, e se isso faz com êxito o andamento, o indexador irá retornar de seu intervalo de agendamento do conjunto novamente.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Defina uma agenda no portal

O Assistente de importação de dados no portal permite definir a agenda para um indexador no momento da criação. A configuração de agenda padrão é **por hora**, que significa que o indexador é executado uma vez depois que ele é criado e executa novamente a cada hora posteriormente.

Você pode alterar a configuração de agenda para **após** se não quiser que o indexador para ser executado novamente automaticamente, ou como **diária** para executar uma vez por dia. Defina-o como **personalizado** se você quiser especificar um intervalo diferente ou uma hora de início futuro específico.

Quando você definir a agenda como **personalizado**, campos são exibidos permitir que você especifique a **intervalo** e o **hora de início (UTC)** . Menor tempo de intervalo permitido é 5 minutos e o mais longo é 1440 minutos (24 horas).

   ![Agenda do indexador de configuração no Assistente de importação de dados](media/search-howto-schedule-indexers/schedule-import-data.png "agenda do indexador de configuração no Assistente de importação de dados")

Depois de um indexador tiver sido criado, você pode alterar as configurações de agendamento usando o painel de edição do indexador. Os campos de agendamento são as mesmas o Assistente de importação de dados.

   ![Definir a agenda no painel de edição do indexador](media/search-howto-schedule-indexers/schedule-edit.png "definir a agenda no painel de edição do indexador")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Defina uma agenda usando a API REST

Você pode definir a agenda para um indexador usando a API REST. Para fazer isso, inclua o **agendamento** propriedade ao criar ou atualizar o indexador. O exemplo a seguir mostra uma solicitação PUT para atualizar um indexador existente:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

O parâmetro **interval** é necessário. O intervalo refere-se ao tempo entre o início de duas execuções consecutivas do indexador. O menor intervalo permitido é de cinco minutos, e o maior é de um dia. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

Opcional **startTime** indica quando as execuções agendadas devem começar. Se for omitido, a hora UTC atual será usada. Esse tempo pode estar no passado, nesse caso, a primeira execução é agendada como se o indexador de execução contínua desde o original **startTime**.

Você também pode executar um indexador sob demanda a qualquer momento usando a chamada executar indexador. Para obter mais informações sobre como executar os indexadores e definição de agendas do indexador, consulte [executar indexador](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [obter indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer), e [atualizar indexador](https://docs.microsoft.com/rest/api/searchservice/update-indexer) na referência da API REST.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Defina uma agenda usando o SDK do .NET

Você pode definir a agenda para um indexador usando o SDK do .NET do Azure Search. Para fazer isso, inclua o **agendamento** propriedade ao criar ou atualizar um indexador.

O seguinte C# exemplo cria um indexador, usando uma fonte de dados predefinido e um índice e define sua agenda seja executada uma vez por dia a partir de 30 minutos a partir de agora:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Se o **agendamento** parâmetro for omitido, o indexador só será executado uma vez imediatamente após sua criação.

O **startTime** parâmetro pode ser definido em uma hora no passado. Nesse caso, a primeira execução é agendada como se o indexador de execução contínua desde o determinado **startTime**.

A agenda é definida usando o [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) classe. O **IndexingSchedule** construtor requer um **intervalo** parâmetro especificado usando um **TimeSpan** objeto. O menor valor de intervalo permitido é de 5 minutos e o maior é de 24 horas. A segunda **startTime** parâmetro, especificado como um **DateTimeOffset** de objeto, é opcional.

O SDK do .NET permite que você controle as operações do indexador usando a [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) classe e seu [indexadores](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) propriedade, que implementa os métodos do **IIndexersOperations**interface. 

Você pode executar um indexador sob demanda a qualquer momento usando um dos [executados](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync), ou [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) métodos.

Para obter mais informações sobre como criar, atualizar e executando indexadores, consulte [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
