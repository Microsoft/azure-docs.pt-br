---
title: Registro em log e diagnóstico no âncoras espacial do Azure | Microsoft Docs
description: Explicação detalhada sobre como gerar e recuperar o registro em log e diagnóstico no âncoras espacial do Azure.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 134023c0884ce3a402b99806f1bf19dcb59ecc32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564860"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Registro em log e diagnóstico no âncoras espacial do Azure

Âncoras espacial do Azure fornece um mecanismo de registro em log padrão que é útil para desenvolvimento de aplicativos. O modo de log de diagnóstico âncoras espacial é útil quando você precisa de mais informações para depuração. Log de diagnóstico armazena imagens do ambiente.

## <a name="standard-logging"></a>Registro em log padrão
Na API do âncoras espacial, você pode assinar o mecanismo de log para obter logs úteis para desenvolvimento de aplicativos e a depuração. As APIs de log padrão não armazenam imagens do ambiente no disco do dispositivo. O SDK fornece esses logs como retornos de chamada do evento. Cabe a você para integrar esses logs de mecanismo de log do aplicativo.

### <a name="configuration-of-log-messages"></a>Configuração de mensagens de log
Há dois retornos de chamada de interesse para o usuário. O exemplo a seguir mostra como configurar a sessão.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Propriedades e eventos

Esses retornos de chamada do evento são fornecidos para processar os logs e erros da sessão:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Especifica o nível de detalhe para os eventos a serem recebidos do tempo de execução.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Fornece eventos de log de depuração padrão.
- [Erro](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Fornece eventos de log que o tempo de execução considera serem erros.

## <a name="diagnostics-logging"></a>Log de diagnósticos

O modo padrão de operação para registro em log, além de âncoras espaciais também tem um modo de diagnóstico. Modo de diagnóstico captura de imagens do ambiente e registra em log no disco. Você pode usar esse modo para determinados tipos de problemas, como falha de maneira previsível localizar uma âncora de depuração. Habilite o log de diagnóstico somente para reproduzir um problema específico. Em seguida, desabilitá-lo. Não habilite o diagnóstico, quando você estiver executando seus aplicativos normalmente.

Durante uma interação de suporte com a Microsoft, um representante da Microsoft pode perguntar se você estiver disposto a enviar um pacote de diagnóstico para uma investigação adicional. Nesse caso, você pode decidir habilitar o diagnóstico e reproduza o problema para que você poder enviar o pacote de diagnóstico. 

Se você enviar o log de diagnóstico à Microsoft sem confirmação anterior de um representante da Microsoft, o envio será ficar sem resposta.

As seções a seguir mostram como habilitar o modo de diagnóstico e também como enviar logs de diagnóstico para a Microsoft.

### <a name="enable-diagnostics-logging"></a>Habilitar o registro em log de diagnóstico

Quando você habilita uma sessão para o log de diagnóstico, todas as operações na sessão têm correspondente log de diagnóstico no sistema de arquivos local. Durante o registro em log, as imagens do ambiente são salvas no disco.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Enviar o pacote de diagnóstico

O trecho de código a seguir mostra como enviar um pacote de diagnóstico à Microsoft. Este pacote inclui imagens do ambiente capturado pela sessão depois que você habilitar o diagnóstico. 

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Partes de um pacote de diagnóstico
O pacote de diagnóstico pode conter as seguintes informações:

- **Imagens de quadro-chave**: Imagens do ambiente capturada durante a sessão, enquanto o diagnóstico foi habilitado.
- **Logs**: Log de eventos registrados pelo tempo de execução.
- **Metadados de sessão**: Metadados que identifica a sessão.
