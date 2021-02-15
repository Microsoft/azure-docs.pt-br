---
title: Processar feed de alterações no armazenamento de BLOBs do Azure | Microsoft Docs
description: Saiba como processar logs do feed de alterações em um aplicativo cliente .NET
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e89fdfba852fc056cf48efd1b92daabb272cf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568244"
---
# <a name="process-change-feed-in-azure-blob-storage"></a>Processar feed de alterações no armazenamento de BLOBs do Azure

O feed de alterações fornece logs de transações de todas as alterações que ocorrem nos BLOBs e nos metadados de BLOB em sua conta de armazenamento. Este artigo mostra como ler registros do feed de alterações usando a biblioteca do processador do feed de alterações de BLOB.

Para saber mais sobre o feed de alterações, consulte o [feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed.md).

## <a name="get-the-blob-change-feed-processor-library"></a>Obter a biblioteca do processador do feed de alterações de BLOB

1. Abra uma janela de comando (por exemplo: Windows PowerShell).
2. No diretório do projeto, instale o [pacote NuGet **Azure. Storage. BLOBs. changefeed** ](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/).

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```
## <a name="read-records"></a>Ler registros

> [!NOTE]
> O feed de alterações é uma entidade imutável e somente leitura em sua conta de armazenamento. Qualquer número de aplicativos pode ler e processar o feed de alterações simultaneamente e de forma independente a sua própria conveniência. Os registros não são removidos do feed de alterações quando um aplicativo os lê. O estado de leitura ou de iteração de cada leitor de consumo é independente e mantido somente pelo seu aplicativo.

Este exemplo itera por todos os registros no feed de alterações, adiciona-os a uma lista e, em seguida, retorna essa lista ao chamador.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Este exemplo imprime no console alguns valores de cada registro na lista. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Retomar a leitura de registros de uma posição salva

Você pode optar por salvar sua posição de leitura no feed de alterações e, em seguida, retomar a iteração pelos registros em um momento futuro. Você pode salvar a posição de leitura obtendo o cursor do feed de alterações. O cursor é uma **cadeia de caracteres** e seu aplicativo pode salvar essa cadeia de caracteres de qualquer forma que faça sentido para o design do seu aplicativo (por exemplo: para um arquivo ou banco de dados).

Este exemplo itera por todos os registros no feed de alterações, adiciona-os a uma lista e salva o cursor. A lista e o cursor são retornados para o chamador. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Fluxo de processamento de registros

Você pode optar por processar registros do feed de alterações à medida que eles são confirmados no feed de alterações. Consulte [especificações](storage-blob-change-feed.md#specifications). Os eventos de alteração são publicados no feed de alterações em um período de 60 segundos em média. É recomendável que você pesquise novas alterações com esse período em mente ao especificar o intervalo de sondagem.

Este exemplo sonda periodicamente as alterações.  Se houver registros de alteração, esse código processará esses registros e salvará o cursor do feed de alterações. Dessa forma, se o processo for interrompido e, em seguida, iniciado novamente, o aplicativo poderá usar o cursor para retomar os registros de processamento onde ele parou pela última vez. Este exemplo salva o cursor em um arquivo de configuração de aplicativo local, mas seu aplicativo pode salvá-lo em qualquer forma que faça mais sentido para seu cenário. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Lendo registros dentro de um intervalo de tempo

Você pode ler os registros que se enquadram em um intervalo de tempo específico. Este exemplo itera em todos os registros no feed de alterações que estão entre 3:00 PM em março de 2 2020 e 2:00 A.M. em 7 2020 de agosto, adiciona-os a uma lista e, em seguida, retorna essa lista ao chamador.

### <a name="selecting-segments-for-a-time-range"></a>Selecionando segmentos para um intervalo de tempo

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

A hora de início que você fornece é arredondada para a hora mais próxima e a hora de término é arredondada para a hora mais próxima. É possível que os usuários possam ver eventos que ocorreram antes da hora de início e após a hora de término. Também é possível que alguns eventos que ocorrem entre a hora de início e de término não apareçam. Isso ocorre porque os eventos podem ser registrados durante a hora anterior à hora de início ou durante a hora após a hora de término.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os logs do feed de alterações. Consulte o [feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed.md)
