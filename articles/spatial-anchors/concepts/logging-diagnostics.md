---
title: Logon e diagnósticos
description: Explicação detalhada de como gerar e recuperar logs e diagnósticos em âncoras espaciais do Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: da8ffd7ff0b8473ce558943bb420b36f26c3fc32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95494634"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Registro em log e diagnósticos nas âncoras espaciais do Azure

As âncoras espaciais do Azure fornecem um mecanismo de registro em log padrão que é útil para o desenvolvimento de aplicativos. O modo de log de diagnóstico de âncoras espaciais é útil quando você precisa de mais informações para depuração. O log de diagnóstico armazena imagens do ambiente.

## <a name="standard-logging"></a>Log padrão
Na API de âncoras espaciais, você pode assinar o mecanismo de registro em log para obter logs úteis para desenvolvimento e depuração de aplicativos. As APIs de log padrão não armazenam fotos do ambiente no disco do dispositivo. O SDK fornece esses logs como retornos de chamada de evento. Cabe a você integrar esses logs ao mecanismo de registro em log do aplicativo.

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

### <a name="events-and-properties"></a>Eventos e propriedades

Esses retornos de chamada de evento são fornecidos para processar logs e erros da sessão:

- [LogLevel](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): especifica o nível de detalhes dos eventos a serem recebidos do tempo de execução.
- [OnLogDebug](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): fornece eventos de log de depuração padrão.
- [Erro](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): fornece eventos de log que o tempo de execução considera erros.

## <a name="diagnostics-logging"></a>Log de diagnósticos

Além do modo padrão de operação para registro em log, as âncoras espaciais também têm um modo de diagnóstico. O modo de diagnóstico captura imagens do ambiente e os registra no disco. Você pode usar esse modo para depurar determinados tipos de problemas, como falha para localizar uma âncora de forma previsível. Habilite o log de diagnóstico apenas para reproduzir um problema específico. Em seguida, desabilite-o. Não habilite o diagnóstico quando estiver executando seus aplicativos normalmente.

Durante uma interação de suporte com a Microsoft, um representante da Microsoft pode perguntar se você está disposto a enviar um pacote de diagnóstico para uma investigação mais aprofundada. Nesse caso, você pode optar por habilitar o diagnóstico e reproduzir o problema para que possa enviar o pacote de diagnóstico.

Se você enviar um log de diagnóstico à Microsoft sem confirmação prévia de um representante da Microsoft, o envio não será respondido.

As seções a seguir mostram como habilitar o modo de diagnóstico e também como enviar logs de diagnóstico para a Microsoft.

### <a name="enable-diagnostics-logging"></a>Habilitar o registro em log de diagnóstico

Quando você habilita uma sessão para o log de diagnóstico, todas as operações na sessão têm o registro em log de diagnóstico correspondente no sistema de arquivos local. Durante o registro em log, as imagens do ambiente são salvas no disco.

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

O trecho de código a seguir mostra como enviar um pacote de diagnóstico para a Microsoft. Esse pacote incluirá imagens do ambiente capturado pela sessão depois que você habilitar o diagnóstico.

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

- **Imagens de quadro-chave**: imagens do ambiente capturadas durante a sessão enquanto os diagnósticos foram habilitados.
- **Logs**: eventos de log registrados pelo tempo de execução.
- **Metadados de sessão**: metadados que identificam a sessão.