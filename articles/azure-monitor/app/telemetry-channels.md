---
title: Canais de telemetria no Azure Application Insights | Microsoft Docs
description: Como personalizar os canais de telemetria em SDKs do Application Insights do Azure para .NET e .NET Core.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561357"
---
# <a name="telemetry-channels-in-application-insights"></a>Canais de telemetria no Application Insights

Canais de telemetria são parte integrante do [SDKs do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Eles gerenciam o armazenamento em buffer e transmissão de telemetria ao serviço Application Insights. As versões do .NET e .NET Core dos SDKs têm dois canais de telemetria interna: `InMemoryChannel` e `ServerTelemetryChannel`. Este artigo descreve cada canal detalhadamente, incluindo como personalizar o comportamento do canal.

## <a name="what-are-telemetry-channels"></a>O que são canais de telemetria?

Canais de telemetria são responsáveis por itens de telemetria de buffer e enviá-los ao serviço Application Insights, onde eles são armazenados para análise e consulta. Um canal de telemetria é qualquer classe que implementa o [ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) interface.

O `Send(ITelemetry item)` método de um canal de telemetria é chamado após todos os inicializadores de telemetria e os processadores de telemetria são chamados. Portanto, todos os itens removidos por um processador de telemetria não atingirá o canal. `Send()` normalmente não envia os itens para o back-end instantaneamente. Normalmente, ele armazena-os em buffer na memória e envia-os em lotes para transmissão eficiente.

[Live Stream métricas](live-stream.md) também tem um canal personalizado que capacita a transmissão ao vivo de telemetria. Esse canal é independente do canal de telemetria regular, e este documento não se aplica a ele.

## <a name="built-in-telemetry-channels"></a>Canais de telemetria interna

Os SDKs do .NET Core e .NET do Application Insights acompanham dois canais internos:

* `InMemoryChannel`: Um canal leve que armazena itens em buffer na memória até que eles são enviados. Os itens são armazenados em buffer na memória e liberados de uma vez a cada 30 segundos, ou sempre que 500 itens são armazenados em buffer. Esse canal oferece garantias de confiabilidade mínimo porque ele não tentará repetir o envio de telemetria após uma falha. Esse canal também não manter itens em disco, para todos os itens não enviados serão perdidos permanentemente após o desligamento do aplicativo (normal ou não). Esse canal implementa um `Flush()` método que pode ser usado para forçar liberação de todos os itens na memória telemetria sincronicamente. Esse canal é adequado para aplicativos de curta execução em que uma liberação síncrona é ideal.

    Esse canal é parte do pacote NuGet Microsoft. applicationinsights maior e é o canal padrão que o SDK usa quando nada mais é configurado.

* `ServerTelemetryChannel`: Um canal mais avançado com políticas de repetição e a capacidade de armazenar dados em um disco local. Esse canal de repetições de envio de telemetria se ocorrem erros transitórios. Esse canal também usa o armazenamento de disco local para manter os itens no disco durante interrupções de rede ou volumes de telemetria alta. Devido a esses mecanismos de repetição e o armazenamento de disco local, esse canal é considerada mais confiável e é recomendado para todos os cenários de produção. Esse canal é o padrão para [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplicativos que são configurados de acordo com a documentação oficial. Esse canal é otimizado para cenários de servidor com processos de longa execução. O [ `Flush()` ](#which-channel-should-i-use) método que é implementado por este canal não é síncrono.

    Esse canal é enviado como o pacote Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet e é adquirido automaticamente quando você usa o NuGet aspnetcore ou applicationinsights pacote.

## <a name="configure-a-telemetry-channel"></a>Configurar um canal de telemetria

Você pode configurar um canal de telemetria definindo-a para a configuração de telemetria do Active Directory. Para aplicativos ASP.NET, a configuração envolve a definição a instância de canal de telemetria `TelemetryConfiguration.Active`, ou modificando `ApplicationInsights.config`. Para aplicativos ASP.NET Core, a configuração envolve a adição do canal para o contêiner de injeção de dependência.

As seções a seguir mostram exemplos de configuração de `StorageFolder` configuração para o canal em vários tipos de aplicativos. `StorageFolder` é apenas uma das definições configuráveis. Para obter a lista completa de definições de configuração, consulte [a seção configurações](telemetry-channels.md#configurable-settings-in-channels) mais adiante neste artigo.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuração com o applicationinsights. config para aplicativos ASP.NET

A seguinte seção de [applicationinsights. config](configuration-with-applicationinsights-config.md) mostra o `ServerTelemetryChannel` canal configurado com `StorageFolder` definido como um local personalizado:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Configuração no código para aplicativos ASP.NET

O código a seguir define uma instância de 'ServerTelemetryChannel' com `StorageFolder` definido como um local personalizado. Adicione este código no início do aplicativo, normalmente em `Application_Start()` método em Global.aspx.cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuração no código para aplicativos ASP.NET Core

Modificar a `ConfigureServices` método da `Startup.cs` classe conforme mostrado aqui:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Configurando o canal usando `TelemetryConfiguration.Active` não é recomendado para aplicativos ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configuração no código para aplicativos de console Core.NET/.NET

Para aplicativos de console, o código é o mesmo para .NET e .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Detalhes operacionais de ServerTelemetryChannel

`ServerTelemetryChannel` repositórios que chegam de itens em um buffer na memória. Os itens são serializados, compactados e armazenados em um `Transmission` instância de uma vez a cada 30 segundos, ou quando 500 itens têm sido armazenado em buffer. Um único `Transmission` instância contém até 500 itens e representa um lote de telemetria é enviados por uma única chamada HTTPS para o serviço Application Insights.

Por padrão, um máximo de 10 `Transmission` instâncias podem ser enviadas em paralelo. Se a telemetria é que chegam com taxas mais rápidas, ou se a rede ou o Application Insights de volta final é lento, `Transmission` instâncias são armazenadas na memória. A capacidade padrão desse na memória `Transmission` buffer é de 5 MB. Quando a capacidade de memória foi excedida, `Transmission` instâncias são armazenadas no disco local até o limite de 50 MB. `Transmission` instâncias são armazenadas no disco local também quando há problemas de rede. Somente os itens que são armazenados em um disco local sobrevivem a uma falha de aplicativo. Elas são enviadas sempre que o aplicativo for iniciado novamente.

## <a name="configurable-settings-in-channels"></a>Definições configuráveis em canais

Para obter a lista completa das definições configuráveis para cada canal, consulte:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Aqui estão as definições mais comumente usadas para `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: A quantidade máxima de memória, em bytes, usada pelo canal para transmissões de buffer na memória. Quando essa capacidade é atingida, novos itens são armazenados diretamente no disco local. O valor padrão é 5 MB. Definindo um valor mais alto resulta em menos uso de disco, mas lembre-se de que os itens na memória serão perdidos se o aplicativo falhar.

1. `MaxTransmissionSenderCapacity`: O número máximo de `Transmission` instâncias que serão enviadas ao Application Insights ao mesmo tempo. O valor padrão é 10. Essa configuração pode ser definida como um número maior, que é recomendado quando um grande volume de telemetria é gerado. Alto volume normalmente ocorre durante o teste de carga ou quando a amostragem é desativada.

1. `StorageFolder`: A pasta que é usada pelo canal para armazenar itens em disco conforme necessário. No Windows, % LOCALAPPDATA % ou % TEMP % é usado se nenhum outro caminho for especificado explicitamente. Em ambientes que não sejam Windows, você deve especificar que um local válido ou a telemetria não armazenada no disco local.

## <a name="which-channel-should-i-use"></a>Qual canal deve usar?

`ServerTelemetryChannel` é recomendado para a maioria dos cenários de produção que envolvem a aplicativos de longa execução. O `Flush()` método implementado pelo `ServerTelemetryChannel` não é síncrono, e também não há garantia de envio de todos os itens pendentes da memória ou disco. Se você usar esse canal em cenários em que o aplicativo está prestes a desligar, é recomendável que você introduza algum atraso após a chamada `Flush()`. A quantidade exata de atraso que você pode exigir não é previsível. Ele depende de fatores como quantos itens ou `Transmission` instâncias estão na memória, quantos estão no disco, quantos estão sendo transmitidos para o back-end e se o canal está no meio de cenários de retirada exponenciais.

Se você precisar fazer uma liberação síncrona, é recomendável que você use `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>O canal do Application Insights garante a entrega de telemetria? Caso contrário, quais são os cenários em que a telemetria pode ser perdida?

A resposta curta é que nenhum dos canais internos oferecem uma garantia de tipo de transação de entrega de telemetria para o back-end. `ServerTelemetryChannel` é muito mais avançado em comparação com `InMemoryChannel` para entrega confiável, mas ele também faz apenas uma tentativa de melhor esforço para enviar telemetria. Telemetria ainda pode ser perdida em várias situações, incluindo esses cenários comuns:

1. Itens na memória são perdidos quando o aplicativo falhar.

1. Telemetria é perdida durante longos períodos de problemas de rede. Telemetria é armazenada no disco local durante interrupções de rede ou quando ocorrem problemas com o back-end do Application Insights. No entanto, os itens com mais de 24 horas serão descartadas.

1. Os locais de disco padrão para armazenar telemetria no Windows são % LOCALAPPDATA % ou % TEMP %. Esses locais são normalmente locais no computador. Se o aplicativo fisicamente migra de um local para outro, qualquer telemetria armazenada no local original é perdida.

1. Em aplicativos Web no Windows, o local de armazenamento em disco padrão é D:\local\LocalAppData. Esse local não é persistente. Forem apagado em reinicializações de aplicativo, transferências de escala e outras essas operações, levando à perda de qualquer telemetria armazenada ali. Você pode substituir o padrão e especificar armazenamento para um local persistente como d:\home. No entanto, esses locais persistentes são atendidas pelo armazenamento remoto e podem ser lentos.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel funciona em sistemas diferentes do Windows?

Embora o nome do seu pacote e o namespace inclui "Windows Server", esse canal é suportado em sistemas que não sejam Windows, com a seguinte exceção. Em sistemas diferentes do Windows, o canal não cria uma pasta de armazenamento local por padrão. Você deve criar uma pasta de armazenamento local e configurar o canal para usá-lo. Depois que o armazenamento local tiver sido configurado, o canal funciona da mesma forma em todos os sistemas.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>O SDK cria armazenamento local temporário? Os dados são criptografados no armazenamento?

O SDK armazena itens de telemetria no armazenamento local durante a problemas de rede ou de limitação. Esses dados não são criptografados localmente.

Para sistemas Windows, o SDK automaticamente cria uma pasta local temporária no diretório % LOCALAPPDATA % ou % TEMP % e restringe o acesso a administradores e o usuário atual.

Para sistemas que não sejam Windows, nenhum armazenamento local é criado automaticamente pelo SDK e, portanto, nenhum dado é armazenado localmente por padrão. Você pode criar um diretório de armazenamento por conta própria e configurar o canal para usá-lo. Nesse caso, você é responsável por garantir que o diretório está protegido.
Leia mais sobre [privacidade e proteção de dados](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK do código-fonte aberto
Como cada SDK do Application Insights, os canais são software livre. Ler e contribuir para o código ou relatar problemas na [repositório GitHub oficial](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Próximas etapas

* [Amostragem](../../azure-monitor/app/sampling.md)
* [Solução de problemas de SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
