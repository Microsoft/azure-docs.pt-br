---
title: Prática de solução de problemas para o serviço de sinalizador do Azure
description: Saiba como solucionar problemas de conectividade e entrega de mensagens
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 2e22777b747ae24c3e643cbd43bfdb0604d453a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97707649"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>Como solucionar problemas de conectividade e entrega de mensagens

Este guia apresenta várias maneiras de ajudar a realizar o diagnóstico automático para localizar a causa raiz diretamente ou restringir o problema. O resultado de autodiagnóstico também é útil ao reportar-se para nós para uma investigação mais aprofundada.

Primeiro, você precisa verificar a portal do Azure qual [servicemode](./concept-service-mode.md) é o serviço de Signaler do Azure (também conhecido como **ASRS**) configurado para.

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="Servicemode":::

* Para o `Default` modo, consulte [solução de problemas do modo padrão](#default_mode_tsg)

* Para o `Serverless` modo, consulte [solução de problemas do modo sem servidor](#serverless_mode_tsg)

* Para o `Classic` modo, consulte [solução de problemas do modo clássico](#classic_mode_tsg)

<a name="default_mode_tsg"></a>

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="default-mode-troubleshooting"></a>Solução de problemas do modo padrão

Quando **ASRS** está no modo *padrão* , há **três** funções: *cliente*, *servidor* e *serviço*:

* *Cliente*: o *cliente* representa os clientes conectados ao **ASRS**. As conexões persistentes que conectam o cliente e o **ASRS** são chamadas de *conexões de cliente* nestas diretrizes.

* *Servidor*: *servidor* significa o servidor que atende à negociação do cliente e hospeda a lógica do signalr `Hub` . E as conexões persistentes entre *Server* e **ASRS** são chamadas de *conexões de servidor* nestas diretrizes.

* *Serviço*: *serviço* é o nome curto para **ASRS** nestas diretrizes.

Consulte os [elementos internos do serviço de signaler do Azure](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) para obter a introdução detalhada de toda a arquitetura e o fluxo de trabalho.

Há várias maneiras que podem ajudá-lo a restringir o problema. 

* Se o problema ocorrer logo no caminho ou se for reproduzido, a maneira direta é exibir o tráfego contínuo. 

* Se o problema for difícil de ser reproduzido, os rastreamentos e logs poderão ajudar.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>Como exibir o tráfego e restringir o problema

Capturar o tráfego contínuo é a maneira mais direta de restringir o problema. Você pode capturar os [rastreamentos de rede](/aspnet/core/signalr/diagnostics#network-traces) usando as opções descritas abaixo:

* [Coletar um rastreamento de rede com o Fiddler](/aspnet/core/signalr/diagnostics#network-traces)

* [Coletar um rastreamento de rede com tcpdump](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Coletar um rastreamento de rede no navegador](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>Solicitações de cliente

Para uma conexão persistente de Signalr, primeiro `/negotiate` para o servidor de aplicativo hospedado e, em seguida, Redirecionado para o serviço de signalr do Azure e, em seguida, estabelece a conexão persistente real com o serviço de signaler do Azure. Consulte os [elementos internos do serviço de signaler do Azure](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) para obter as etapas detalhadas.

Com o rastreamento de rede do lado do cliente em mãos, verifique qual solicitação falha com o código de status e qual resposta e procure soluções no [Guia de solução de problemas](./signalr-howto-troubleshoot-guide.md).

#### <a name="server-requests"></a>Solicitações do servidor

O *servidor* de sinalização mantém a *conexão do servidor* entre *servidor* e *serviço*. Quando o servidor de aplicativos é iniciado, ele inicia a conexão **WebSocket** com o serviço de Signaler do Azure. Todos os tráfegos do cliente são roteados por meio do serviço de Signaler do Azure para esses s *conexões de servidor* e, em seguida, enviados para o `Hub` . Quando uma *conexão de servidor* é descartada, os clientes roteados para essa *conexão de servidor* serão afetados. Nosso SDK do Signaler do Azure tem uma lógica "sempre repetir" para reconectar a *conexão do servidor* com no máximo 1 minuto de atraso para minimizar o impacto.

Os s de *conexão do servidor* podem ser descartados devido à instabilidade da rede ou à manutenção regular do serviço de signaler do Azure ou às atualizações/manutenções do servidor de aplicativos hospedados. Desde que o lado do cliente tenha o mecanismo de desconexão/reconexão, o impacto é mínimo, como qualquer do lado do cliente, provocava uma reconexão de desconexão.

Exiba o rastreamento de rede do lado do servidor para descobrir o código de status e detalhes do erro por que a *conexão do servidor* cai ou foi rejeitada pelo *serviço* e procure a causa raiz no guia de [solução de problemas](./signalr-howto-troubleshoot-guide.md).

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>Como adicionar logs

Os logs podem ser úteis para diagnosticar problemas e monitorar o status de execução.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>Como habilitar o log do lado do cliente

A experiência de registro no lado do cliente é exatamente a mesma que ao usar o Signalr auto-hospedado.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>Habilitar o log do lado do cliente para `ASP.NET Core SignalR`

* [Log de cliente JavaScript](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [Log de cliente .NET](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>Habilitar o log do lado do cliente para `ASP.NET SignalR`

* [Cliente .NET](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Habilitando o rastreamento em clientes Windows Phone 8](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [Habilitando o rastreamento no cliente JavaScript](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Como habilitar o log do lado do servidor

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Habilitar o log do lado do servidor para `ASP.NET Core SignalR`

O registro em log do lado do servidor do `ASP.NET Core SignalR` se integra ao `ILogger` [registro em log](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) com base fornecido na `ASP.NET Core` estrutura. Você pode habilitar o registro em log do lado do servidor usando `ConfigureLogging` o, um exemplo de uso da seguinte maneira:

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

As categorias de agente para o Signalr do Azure sempre começam com `Microsoft.Azure.SignalR` . Para habilitar logs detalhados do Signalr do Azure, configure os prefixos anteriores para `Information` redistribuir em seu **appsettings.jsno** arquivo, como abaixo:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Verifique se há algum log de aviso/erro anormal registrado. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Habilitar rastreamentos do lado do servidor para `ASP.NET SignalR`

Ao usar a versão >do SDK = `1.0.0` , você pode habilitar os rastreamentos adicionando o seguinte a `web.config` : ([detalhes](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))

```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

Verifique se há algum log de aviso/erro anormal registrado. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Como habilitar logs dentro do serviço de Signaler do Azure

Você também pode [habilitar os logs de diagnóstico](./signalr-howto-diagnostic-logs.md) para o serviço de Signaler do Azure, esses logs fornecem informações detalhadas de cada conexão conectada ao serviço de Signaler do Azure.

<a name="serverless_mode_tsg"></a>

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>Solução de problemas do modo sem servidor

Quando o **ASRS** está no modo sem *servidor* , somente **ASP.NET Core signalr** dá suporte ao `Serverless` modo e o **signalr ASP.net** **não dá** suporte a esse modo.

Para diagnosticar problemas de conectividade no `Serverless` modo, a maneira mais direta é [Exibir o tráfego do lado do cliente](#view_traffic_client). Habilitar [logs do lado do cliente](#add_logs_client) e [logs do lado do serviço](#add_logs_server) também podem ser úteis.

<a name="classic_mode_tsg"></a>

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>Solução de problemas do modo clássico

`Classic` o modo está obsoleto e não é incentivado a usar. Quando nesse modo, o serviço de Signalr do Azure usa as *conexões do servidor* conectado para determinar se o serviço atual está no `default` modo ou no `serverless` modo. Isso pode levar a alguns problemas de conectividade de cliente intermediários porque, quando há uma queda repentina de toda a *conexão de servidor* conectada, por exemplo, devido à instabilidade da rede, o Signaler do Azure acredita que agora ele está alternado para `serverless` o modo e os clientes conectados durante esse período nunca serão roteados para o servidor de aplicativos hospedado. Habilite [os logs do lado do serviço](#add_logs_server) e verifique se há clientes registrados como `ServerlessModeEntered` se você tiver o servidor de aplicativos hospedado, mas alguns clientes nunca alcançarão o lado do servidor de aplicativos. Se houver, [anule essas conexões de cliente](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) e permita que os clientes reiniciem pode ajudar.

Problemas `classic` de conectividade do modo de solução de problemas e de entrega de mensagens são semelhantes à solução de problemas do [modo padrão](#default_mode_tsg).

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>Integridade do serviço

Você pode verificar a integridade da API de integridade do serviço.

* Solicitação: obter `https://{instance_name}.service.signalr.net/api/v1/health`

* Código de status de resposta:
  * 200: íntegro.
  * 503: seu serviço não está íntegro. Você pode:
    * Aguarde alguns minutos para a recuperação automática.
    * Verifique se o endereço IP é o mesmo que o IP do Portal.
    * Ou reinicie a instância.
    * Se todas as opções acima não funcionarem, entre em contato conosco adicionando nova solicitação de suporte no portal do Azure.

Mais sobre a [recuperação de desastres](./signalr-concept-disaster-recovery.md).

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu sobre como solucionar problemas de conectividade e entrega de mensagens. Você também pode aprender a lidar com os problemas comuns. 

> [!div class="nextstepaction"]
> [Guia de Solução de problemas](./signalr-howto-troubleshoot-guide.md)