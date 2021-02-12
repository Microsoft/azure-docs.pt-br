---
title: Canais de telemetria no Aplicativo Azure insights | Microsoft Docs
description: Como personalizar canais de telemetria nos SDKs do Aplicativo Azure insights para .NET e .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: a22a0d112671019d73eb4c9a3853462e4e9c8c75
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937351"
---
# <a name="telemetry-channels-in-application-insights"></a>Canais de telemetria no Application Insights

Os canais de telemetria são parte integrante dos [SDKs do aplicativo Azure insights](./app-insights-overview.md). Eles gerenciam o armazenamento em buffer e a transmissão de telemetria para o serviço de Application Insights. As versões .NET e .NET Core dos SDKs têm dois canais de telemetria internos: `InMemoryChannel` e `ServerTelemetryChannel` . Este artigo descreve cada canal em detalhes, incluindo como personalizar o comportamento do canal.

## <a name="what-are-telemetry-channels"></a>O que são canais de telemetria?

Os canais de telemetria são responsáveis por armazenar em buffer itens de telemetria e enviá-los para o serviço de Application Insights, onde são armazenados para consulta e análise. Um canal de telemetria é qualquer classe que implementa a [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel) interface.

O `Send(ITelemetry item)` método de um canal de telemetria é chamado depois que todos os inicializadores de telemetria e os processadores de telemetria são chamados. Assim, todos os itens descartados por um processador de telemetria não alcançarão o canal. `Send()` Normalmente, o não envia os itens para o back-end instantaneamente. Normalmente, ele os armazena em buffers na memória e os envia em lotes para transmissão eficiente.

[Live Metrics Stream](live-stream.md) também tem um canal personalizado que alimenta a transmissão ao vivo da telemetria. Esse canal é independente do canal de telemetria regular e este documento não se aplica a ele.

## <a name="built-in-telemetry-channels"></a>Canais internos de telemetria

Os SDKs Application Insights .NET e .NET Core são fornecidos com dois canais internos:

* `InMemoryChannel`: Um canal leve que armazena em buffer os itens na memória até que eles sejam enviados. Os itens são armazenados em buffer na memória e liberados uma vez a cada 30 segundos, ou sempre que 500 itens são armazenados em buffer. Esse canal oferece garantias de confiabilidade mínimas porque não tenta enviar novamente a telemetria após uma falha. Esse canal também não mantém itens em disco, portanto, todos os itens não enviados são perdidos permanentemente após o desligamento do aplicativo (normal ou não). Esse canal implementa um `Flush()` método que pode ser usado para forçar a liberação de todos os itens de telemetria na memória de forma síncrona. Esse canal é adequado para aplicativos de curta execução em que uma liberação síncrona é ideal.

    Esse canal faz parte do pacote NuGet Microsoft. ApplicationInsights maior e é o canal padrão que o SDK usa quando nada mais está configurado.

* `ServerTelemetryChannel`: Um canal mais avançado que tem políticas de repetição e a capacidade de armazenar dados em um disco local. Esse canal tentará enviar telemetria novamente se ocorrerem erros transitórios. Esse canal também usa o armazenamento em disco local para manter os itens em disco durante interrupções de rede ou grandes volumes de telemetria. Devido a esses mecanismos de repetição e armazenamento de disco local, esse canal é considerado mais confiável e é recomendado para todos os cenários de produção. Esse canal é o padrão para aplicativos [ASP.net](./asp-net.md) e [ASP.NET Core](./asp-net-core.md) configurados de acordo com a documentação oficial. Esse canal é otimizado para cenários de servidor com processos de execução longa. O [`Flush()`](#which-channel-should-i-use) método que é implementado por esse canal não é síncrono.

    Esse canal é fornecido como o pacote NuGet Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel e é adquirido automaticamente quando você usa o pacote NuGet Microsoft. ApplicationInsights. Web ou Microsoft. ApplicationInsights. AspNetCore.

## <a name="configure-a-telemetry-channel"></a>Configurar um canal de telemetria

Configure um canal de telemetria definindo-o como a configuração de telemetria ativa. Para aplicativos ASP.NET, a configuração envolve definir a instância de canal de telemetria para `TelemetryConfiguration.Active` ou modificando `ApplicationInsights.config` . Para aplicativos ASP.NET Core, a configuração envolve adicionar o canal ao contêiner de injeção de dependência.

As seções a seguir mostram exemplos de como definir a `StorageFolder` configuração para o canal em vários tipos de aplicativos. `StorageFolder` é apenas uma das configurações configuráveis. Para obter a lista completa de definições de configuração, consulte [a seção Configurações](#configurable-settings-in-channels) mais adiante neste artigo.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuração usando ApplicationInsights.config para aplicativos ASP.NET

A seção a seguir de [ApplicationInsights.config](configuration-with-applicationinsights-config.md) mostra o `ServerTelemetryChannel` canal configurado com `StorageFolder` definido como um local personalizado:

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

O código a seguir configura uma instância de ' ServerTelemetryChannel ' com `StorageFolder` definido como um local personalizado. Adicione esse código no início do aplicativo, normalmente no `Application_Start()` método em global.aspx.cs.

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

Modifique o `ConfigureServices` método da `Startup.cs` classe, conforme mostrado aqui:

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
> Não é recomendável configurar o canal usando o `TelemetryConfiguration.Active` não é recomendado para aplicativos ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configuração no código para aplicativos de console .NET/.NET Core

Para aplicativos de console, o código é o mesmo para .NET e .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Detalhes operacionais do ServerTelemetryChannel

`ServerTelemetryChannel` armazena itens de chegada em um buffer na memória. Os itens são serializados, compactados e armazenados em uma `Transmission` instância uma vez a cada 30 segundos ou quando 500 itens tiverem sido colocados em buffer. Uma única `Transmission` instância contém até 500 itens e representa um lote de telemetria enviado por uma única chamada HTTPS para o serviço Application insights.

Por padrão, um máximo de 10 `Transmission` instâncias pode ser enviado em paralelo. Se a telemetria estiver chegando a taxas mais rápidas ou se a rede ou o back-end de Application Insights estiver lento, as `Transmission` instâncias serão armazenadas na memória. A capacidade padrão desse buffer na memória `Transmission` é de 5 MB. Quando a capacidade na memória for excedida, as `Transmission` instâncias serão armazenadas no disco local até um limite de 50 MB. `Transmission` as instâncias são armazenadas no disco local também quando há problemas de rede. Somente os itens que são armazenados em um disco local sobrevivem a uma falha do aplicativo. Eles são enviados sempre que o aplicativo é iniciado novamente.

## <a name="configurable-settings-in-channels"></a>Configurações configuráveis em canais

Para obter a lista completa de configurações configuráveis para cada canal, consulte:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Aqui estão as configurações mais comumente usadas para `ServerTelemetryChannel` :

1. `MaxTransmissionBufferCapacity`: A quantidade máxima de memória, em bytes, usada pelo canal para transmissões de buffer na memória. Quando essa capacidade é atingida, novos itens são armazenados diretamente no disco local. O valor padrão é 5 MB. Definir um valor mais alto leva a menos uso do disco, mas lembre-se de que os itens na memória serão perdidos se o aplicativo falhar.

1. `MaxTransmissionSenderCapacity`: O número máximo de `Transmission` instâncias que serão enviadas para Application insights ao mesmo tempo. O valor padrão é 10. Essa configuração pode ser configurada para um número mais alto, que é recomendado quando um grande volume de telemetria é gerado. O alto volume geralmente ocorre durante o teste de carga ou quando a amostragem está desativada.

1. `StorageFolder`: A pasta usada pelo canal para armazenar itens no disco, conforme necessário. No Windows, o% LOCALAPPDATA% ou% TEMP% será usado se nenhum outro caminho for especificado explicitamente. Em ambientes diferentes do Windows, você deve especificar um local válido ou a telemetria não será armazenada no disco local.

## <a name="which-channel-should-i-use"></a>Qual canal devo usar?

`ServerTelemetryChannel` é recomendado para a maioria dos cenários de produção que envolvem aplicativos de execução longa. O `Flush()` método implementado por `ServerTelemetryChannel` não é síncrono e também não garante o envio de todos os itens pendentes da memória ou do disco. Se você usar esse canal em cenários em que o aplicativo está prestes a ser desligado, recomendamos que você introduza algum atraso após a chamada `Flush()` . A quantidade exata de atraso que você pode exigir não é previsível. Depende de fatores como quantos itens ou `Transmission` instâncias estão na memória, quantas estão no disco, quantos estão sendo transmitidos para o back-end e se o canal está no meio dos cenários de retirada exponencial.

Se você precisar fazer uma liberação síncrona, recomendamos o uso do `InMemoryChannel` .

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>O canal Application Insights garante a entrega de telemetria? Caso contrário, quais são os cenários em que a telemetria pode ser perdida?

A resposta curta é que nenhum dos canais internos oferece uma garantia de tipo de transação de entrega de telemetria para o back-end. `ServerTelemetryChannel` é mais avançado em comparação com a `InMemoryChannel` entrega confiável, mas também faz apenas uma tentativa de envio de telemetria por um melhor esforço. A telemetria ainda pode ser perdida em várias situações, incluindo estes cenários comuns:

1. Os itens na memória são perdidos quando o aplicativo falha.

1. A telemetria é perdida durante períodos estendidos de problemas de rede. A telemetria é armazenada no disco local durante interrupções de rede ou quando ocorrem problemas com o back-end de Application Insights. No entanto, itens com mais de 48 horas são descartados.

1. Os locais de disco padrão para armazenar telemetria no Windows são% LOCALAPPDATA% ou% TEMP%. Esses locais normalmente são locais para o computador. Se o aplicativo migrar fisicamente de um local para outro, qualquer telemetria armazenada no local original será perdida.

1. Em aplicativos Web no Windows, o local de armazenamento de disco padrão é D:\local\LocalAppData. Esse local não é persistente. Ele é apagado em reinicializações de aplicativo, escalações e outras operações, levando à perda de qualquer telemetria armazenada lá. Você pode substituir o padrão e especificar o armazenamento para um local persistente, como D:\home. No entanto, esses locais persistentes são atendidos pelo armazenamento remoto e, portanto, podem ser lentos.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>O ServerTelemetryChannel funciona em sistemas diferentes do Windows?

Embora o nome de seu pacote e namespace inclua "WindowsServer", esse canal tem suporte em sistemas diferentes do Windows, com a seguinte exceção. Em sistemas diferentes do Windows, o canal não cria uma pasta de armazenamento local por padrão. Você deve criar uma pasta de armazenamento local e configurar o canal para usá-la. Depois que o armazenamento local tiver sido configurado, o canal funcionará da mesma maneira em todos os sistemas.

> [!NOTE]
> Com a versão 2.15.0-Beta3 e o armazenamento local superior agora são criados automaticamente para Linux, Mac e Windows. Para sistemas não Windows, o SDK criará automaticamente uma pasta de armazenamento local com base na seguinte lógica:
> - `${TMPDIR}` -Se `${TMPDIR}` a variável de ambiente for definida, esse local será usado.
> - `/var/tmp` -Se o local anterior não existir, tentaremos `/var/tmp` .
> - `/tmp` -Se os dois locais anteriores não existirem, tentaremos `tmp` . 
> - Se nenhum desses locais existir, o armazenamento local não será criado e a configuração manual ainda será necessária. [Para obter detalhes sobre a implementação completa](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>O SDK cria armazenamento local temporário? Os dados são criptografados no armazenamento?

O SDK armazena itens de telemetria no armazenamento local durante problemas de rede ou durante a limitação. Esses dados não são criptografados localmente.

Para sistemas Windows, o SDK cria automaticamente uma pasta local temporária no diretório% TEMP% ou% LOCALAPPDATA% e restringe o acesso a administradores e somente ao usuário atual.

Para sistemas diferentes do Windows, nenhum armazenamento local é criado automaticamente pelo SDK e, portanto, nenhum dado é armazenado localmente por padrão.

> [!NOTE]
> Com a versão 2.15.0-Beta3 e o armazenamento local superior agora são criados automaticamente para Linux, Mac e Windows. 

 Você pode criar um diretório de armazenamento por conta própria e configurar o canal para usá-lo. Nesse caso, você é responsável por garantir que o diretório esteja protegido.
Leia mais sobre [proteção e privacidade de dados](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK do código-fonte aberto
Assim como todos os SDK para Application Insights, os canais são de software livre. Leia e contribua com o código ou relate problemas no [repositório GitHub oficial](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Próximas etapas

* [Amostragem](./sampling.md)
* [Solução de problemas do SDK](./asp-net-troubleshoot-no-data.md)

