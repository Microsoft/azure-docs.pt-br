---
title: Guia de solução de problemas para Serviço do Azure SignalR
description: Saiba como solucionar problemas comuns
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: bdda89483661eb6f6d006c3d8ea42b46d162de05
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201647"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Guia de solução de problemas comuns do serviço Signaler do Azure

Este guia é para fornecer um guia de solução de problemas útil com base nos problemas comuns que os clientes cumpriram e resolveram nos últimos anos.

## <a name="access-token-too-long"></a>Token de acesso muito longo

### <a name="possible-errors"></a>Possíveis erros:

* No lado do cliente `ERR_CONNECTION_`
* 414 URI muito longo
* O conteúdo 413 é muito grande
* O token de acesso não deve ser maior que 4K. Solicitação 413 entidade muito grande

### <a name="root-cause"></a>Causa raiz:

Para HTTP/2, o comprimento máximo de um único cabeçalho é **4 K**. portanto, se usar o navegador para acessar o serviço do Azure, haverá um erro `ERR_CONNECTION_` para essa limitação.

Para clientes HTTP/1.1 ou C#, o comprimento máximo do URI é **12 k**, o comprimento máximo do cabeçalho é **16 k**.

Com o SDK versão **1.0.6** ou superior, `/negotiate` o gerará `413 Payload Too Large` quando o token de acesso gerado for maior que **4 K**.

### <a name="solution"></a>Solução:

Por padrão, as declarações de `context.User.Claims` são incluídas ao gerar **o** token de acesso JWT para **ASRS**(ZURE **s** ignal **R** **s** nom), para que as declarações sejam preservadas e possam ser passadas de **ASRS** para `Hub` quando o cliente se conectar ao `Hub` .

Em alguns casos, `context.User.Claims` são aproveitados para armazenar muitas informações para o servidor de aplicativos, a maioria das quais não são usadas por `Hub` s, mas por outros componentes.

O token de acesso gerado é transmitido pela rede e para conexões WebSocket/SSE, os tokens de acesso são passados por meio de cadeias de caracteres de consulta. Como prática recomendada, sugerimos apenas passar as declarações **necessárias** do cliente por meio de **ASRS** para seu servidor de aplicativos quando o Hub precisar.

Há um `ClaimsProvider` para você personalizar as declarações passando para **ASRS** dentro do token de acesso.

Por ASP.NET Core:
```cs
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

Para ASP.NET:
```cs
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tls-12-required"></a>TLS 1,2 necessário

### <a name="possible-errors"></a>Possíveis erros:

* Erro de ASP.NET "nenhum servidor disponível" [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "a conexão não está ativa, os dados não podem ser enviados para o serviço." erro [#324](https://github.com/Azure/azure-signalr/issues/324)
* "Ocorreu um erro ao fazer a solicitação HTTP para https:// <API endpoint> . Esse erro pode ser devido ao fato de que o certificado do servidor não está configurado corretamente com HTTP.SYS no caso HTTPS. Esse erro também pode ser causado por uma incompatibilidade da Associação de segurança entre o cliente e o servidor. "

### <a name="root-cause"></a>Causa raiz:

O serviço do Azure dá suporte apenas a TLS 1.2 para questões de segurança. Com o .NET Framework, é possível que o TLS 1.2 não seja o protocolo padrão. Como resultado, as conexões do servidor com o ASRS não podem ser estabelecidas com êxito.

### <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

1. Se esse erro puder ser reproduzido localmente, desmarque *apenas meu código* e lance todas as exceções CLR e depure o servidor de aplicativos localmente para ver qual exceção é lançada.
    * Desmarque *apenas meu código*

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="Desmarque Apenas Meu Código":::

    * Gerar exceções CLR

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="Gerar exceções CLR":::

    * Consulte as exceções lançadas ao depurar o código do lado do servidor do aplicativo:
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="A exceção é lançada":::

2. Para ASP.NET, você também pode adicionar o código a seguir ao seu `Startup.cs` para habilitar o rastreamento detalhado e ver os erros do log.
```cs
app.MapAzureSignalR(this.GetType().FullName);
// Make sure this switch is called after MapAzureSignalR
GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
```

### <a name="solution"></a>Solução:

Adicione o seguinte código à sua inicialização:
```cs
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="400-bad-request-returned-for-client-requests"></a>400 solicitação inadequada retornada para solicitações do cliente

### <a name="root-cause"></a>Causa raiz

Verifique se a solicitação do cliente tem várias `hub` cadeias de caracteres de consulta. `hub` é um parâmetro de consulta preservado e 400 gerará se o serviço detectar mais de um `hub` na consulta.

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="401-unauthorized-returned-for-client-requests"></a>401 não autorizado retornado para solicitações de cliente

### <a name="root-cause"></a>Causa raiz

Atualmente, o valor padrão do tempo de vida do token JWT é de 1 hora.

Para ASP.NET Core Signalr, quando ele está usando o tipo de transporte WebSocket, ele está OK.

Para ASP.NET Core outro tipo de transporte, SSE e sondagem longa, isso significa, por padrão, que a conexão pode, no máximo, persistir por 1 hora.

Para o Signalr ASP.NET, o cliente envia uma `/ping` solicitação de KeepAlive ao serviço de tempos em tempos, quando a `/ping` falha, o cliente **anula** a conexão e nunca se reconecta. Isso significa que, para o Signalr ASP.NET, o tempo de vida do token padrão faz com que a conexão dure **no máximo** 1 hora para todo o tipo de transporte.

### <a name="solution"></a>Solução

Para questões de segurança, o tempo de vida estendido não é incentivado. Sugerimos adicionar lógica de reconexão do cliente para reiniciar a conexão quando tal 401 ocorrer. Quando o cliente reinicia a conexão, ele negociará com o servidor de aplicativos para obter o token JWT novamente e obterá um token renovado.

Confira [aqui](#restart_connection) para saber como reiniciar as conexões de cliente.

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-client-requests"></a>404 retornado para solicitações de cliente

Para uma conexão persistente do Signalr, primeiro `/negotiate` para o serviço do Azure signalr e, em seguida, estabelece a conexão real com o serviço de signaler do Azure.

### <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

* Seguindo [como exibir as solicitações de saída](#view_request) para obter a solicitação do cliente para o serviço.
* Verifique a URL da solicitação quando 404 ocorrer. Se a URL estiver direcionando para seu aplicativo Web e, semelhante a `{your_web_app}/hubs/{hubName}` , verifique se o cliente `SkipNegotiation` é `true` . Ao usar o Signalr do Azure, o cliente recebe a URL de redirecionamento quando negocia pela primeira vez com o servidor de aplicativos. O cliente **não** deve ignorar a negociação ao usar o Signalr do Azure.
* Outro 404 pode acontecer quando a solicitação de conexão é tratada mais de **5** segundos após `/negotiate` ser chamada. Verifique o carimbo de data/hora da solicitação do cliente e abra um problema para nós se a solicitação para o serviço tiver uma resposta lenta.

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>404 retornado para solicitação de reconexão do Signalr ASP.NET

Para o Signalr ASP.NET, quando a [conexão do cliente cai](#client_connection_drop), ela se reconecta usando o mesmo `connectionId` por três vezes antes de parar a conexão. `/reconnect` pode ajudar se a conexão for descartada devido a problemas intermitentes de rede que `/reconnect` podem restabelecer a conexão persistente com êxito. Em outras circunstâncias, por exemplo, a conexão do cliente é descartada devido à conexão do servidor roteado ser descartada, ou o serviço de sinalização tem alguns erros internos como reinicialização/failover/implantação da instância, a conexão não existe mais, portanto `/reconnect` retorna `404` . É o comportamento esperado para `/reconnect` e depois de três vezes que a conexão é interrompida. Sugerimos ter lógica de [reinicialização de conexão](#restart_connection) quando a conexão for interrompida.

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (muitas solicitações) retornadas para solicitações do cliente

Existem dois casos.

### <a name="concurrent-connection-count-exceeds-limit"></a>A contagem de conexões **simultâneas** excede o limite.

Para instâncias **gratuitas** , o limite de contagem de conexões **simultâneas** é de 20 para instâncias **padrão** , o limite de contagem de conexões **simultâneas** **por unidade** é 1 K, o que significa que o Unit100 permite conexões simultâneas de 100-K.

As conexões incluem conexões de cliente e de servidor. Verifique [aqui](./signalr-concept-messages-and-connections.md#how-connections-are-counted) como as conexões são contadas.

### <a name="too-many-negotiate-requests-at-the-same-time"></a>Muitas solicitações Negotiate ao mesmo tempo.

Sugerimos ter um atraso aleatório antes da reconexão, consulte [aqui](#restart_connection) para obter amostras de novas tentativas.

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>erro 500 ao negociar: o serviço de Signaler do Azure ainda não está conectado. tente novamente mais tarde.

### <a name="root-cause"></a>Causa raiz

Esse erro é relatado quando não há nenhuma conexão de servidor com o serviço de Signaler do Azure conectada.

### <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

Habilite o rastreamento do lado do servidor para descobrir os detalhes do erro quando o servidor tenta se conectar ao serviço de Signaler do Azure.

#### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Habilitar o registro em log do lado do servidor para ASP.NET Core Signalr

O registro em log do lado do servidor para ASP.NET Core sinalizador se integra com o `ILogger` [registro em log](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) com base fornecido no ASP.NET Core Framework. Você pode habilitar o registro em log do lado do servidor usando `ConfigureLogging` o, um exemplo de uso da seguinte maneira:
```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```
As categorias de agente para o Signalr do Azure sempre começam com `Microsoft.Azure.SignalR` . Para habilitar logs detalhados do Signalr do Azure, configure os prefixos anteriores para `Debug` redistribuir em seu **appsettings.jsno** arquivo, como abaixo:
```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Habilitar rastreamentos no lado do servidor para o Signalr ASP.NET

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

<a name="client_connection_drop"></a>

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-drops"></a>Quedas de conexão de cliente

Quando o cliente está conectado ao Signalr do Azure, a conexão persistente entre o cliente e o vinculador do Azure pode, às vezes, ser descartada por diferentes motivos. Esta seção descreve várias possibilidades que causam tal queda de conexão e fornece algumas diretrizes sobre como identificar a causa raiz.

### <a name="possible-errors-seen-from-the-client-side"></a>Possíveis erros vistos do lado do cliente

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>Causa raiz:

As conexões de cliente podem ser descartadas em várias circunstâncias:
* Quando `Hub` o gera exceções com a solicitação de entrada.
* Quando a conexão do servidor, que o cliente encaminhada, descarta, consulte a seção abaixo para obter detalhes sobre [quedas de conexão do servidor](#server_connection_drop).
* Quando ocorre um problema de conectividade de rede entre o cliente e o serviço de Signalr.
* Quando o serviço de sinalização tem alguns erros internos, como reinicialização de instância, failover, implantação e assim por diante.

### <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

1. Abra o log do lado do servidor de aplicativo para ver se algo anormal ocorreu
2. Verifique o log de eventos do servidor do aplicativo para ver se o servidor de aplicativos foi reiniciado
3. Crie um problema para fornecer o período de tempo e envie por email o nome do recurso para nós

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-increases-constantly"></a>A conexão do cliente aumenta constantemente

Pode ser causado pelo uso impróprio da conexão do cliente. Se alguém esquecer de parar/descartar o cliente do Signalr, a conexão permanecerá aberta.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Possíveis erros vistos a partir das métricas do Signalr que estão na seção monitoramento do menu portal do Azure recurso

As conexões de cliente crescem constantemente por muito tempo nas métricas do Azure Signalr.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="Conexão de cliente aumentando constantemente":::

### <a name="root-cause"></a>Causa raiz:

A conexão do cliente signalr `DisposeAsync` nunca é chamada, a conexão permanece aberta.

### <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

1. Verifique se o cliente do Signalr **nunca** fecha.

### <a name="solution"></a>Solução

Verifique se você fechou a conexão. Chame manualmente `HubConnection.DisposeAsync()` para parar a conexão depois de usá-la.

Por exemplo:

```C#
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>Uso de conexão de cliente inadequado comum

#### <a name="azure-function-example"></a>Exemplo de função do Azure 

Esse problema geralmente ocorre quando alguém estabelece a conexão de cliente do Signalr no método do Azure Function em vez de torná-lo um membro estático para sua classe de função. Você pode esperar que apenas uma conexão de cliente seja estabelecida, mas a contagem de conexões do cliente aumenta constantemente em métricas que estão na seção monitoramento do menu portal do Azure recurso, todas essas conexões são descartadas somente após a reinicialização do Azure Function ou do serviço de Signaler do Azure. Isso ocorre porque, para **cada** solicitação, o Azure function cria **uma** conexão de cliente, se você não parar a conexão do cliente no método Function, o cliente manterá as conexões ativas para o serviço de signaler do Azure.

#### <a name="solution"></a>Solução

* Lembre-se de fechar a conexão do cliente se você usar clientes do Signalr no Azure Function ou usar o cliente do Signalr como um singleton.
* Em vez de usar clientes do Signalr no Azure Function, você pode criar clientes do Signalr em qualquer outro lugar e usar [associações de Azure Functions para o serviço de signaler do Azure](https://github.com/Azure/azure-functions-signalrservice-extension) para [negociar](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) o cliente para o signalr do Azure. E você também pode utilizar a associação para [enviar mensagens](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40). Exemplos para negociar clientes e enviar mensagens podem ser encontrados [aqui](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples). Mais informações podem ser encontradas [aqui](https://github.com/Azure/azure-functions-signalrservice-extension).
* Quando você usa clientes do Signalr no Azure Function, pode haver uma melhor arquitetura para seu cenário. Verifique se você designou uma arquitetura sem servidor adequada. Você pode consultar [aplicativos sem servidor em tempo real com as associações de serviço de sinalização no Azure Functions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService).

<a name="server_connection_drop"></a>

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="server-connection-drops"></a>Quedas de conexão do servidor

Quando o servidor de aplicativos é iniciado, em segundo plano, o SDK do Azure começa a iniciar as conexões do servidor com o Signaler remoto do Azure. Conforme descrito em [interno do serviço de signaler do Azure](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md), o signalr do Azure encaminha os tráfegos de entrada do cliente para essas conexões de servidor. Depois que uma conexão de servidor é descartada, todas as conexões de cliente que ele serve também serão fechadas.

Como as conexões entre o servidor de aplicativos e o serviço de sinalização são conexões persistentes, elas podem enfrentar problemas de conectividade de rede. No SDK do servidor, **sempre reconectamos** a estratégia a conexões do servidor. Como a melhor prática, também incentivamos os usuários a adicionar lógica de reconexão contínua aos clientes com um tempo de atraso aleatório para evitar solicitações simultâneas maciças ao servidor.

Regularmente, há novas versões de versão para o serviço de Signaler do Azure e, às vezes, os patches ou atualizações do sistema operacional de todo o Azure, ou ocasionalmente interrupções de nossos serviços dependentes. Isso pode trazer um curto período de interrupção do serviço, mas, desde que o lado do cliente tenha o mecanismo de desconexão/reconexão, o impacto é mínimo como qualquer do lado do cliente causou o desconexão-reconectar.

Esta seção descreve várias possibilidades que levam à queda da conexão do servidor e fornece algumas diretrizes sobre como identificar a causa raiz.

### <a name="possible-errors-seen-from-server-side"></a>Possíveis erros vistos a partir do lado do servidor:

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>Causa raiz:

**A conexão** do servidor-serviço é fechada por **ASRS**(ZURE **s** ignal **R** **s** nom).

### <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

1. Abra o log do lado do servidor de aplicativo para ver se algo anormal ocorreu
2. Verifique o log de eventos do servidor do aplicativo para ver se o servidor de aplicativos foi reiniciado
3. Crie um problema para fornecer o período de tempo e envie por email o nome do recurso para nós

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tips"></a>Dicas

<a name="view_request"></a>

* Como exibir a solicitação de saída do cliente?
Considere ASP.NET Core um, por exemplo (ASP.NET um é semelhante):
    * Do navegador:

        Veja o Chrome como exemplo, você pode usar **F12** para abrir a janela do console e alternar para a guia **rede** . Talvez seja necessário atualizar a página usando **F5** para capturar a rede desde o início.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Rede de exibição do Chrome":::

    * Do cliente C#:

        Você pode exibir os tráfegos da Web locais usando o [Fiddler](https://www.telerik.com/fiddler). Os tráfegos de WebSocket têm suporte desde o Fiddler 4,5.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Rede de exibição Fiddler" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* Como reiniciar a conexão do cliente?
    
    Aqui estão os [códigos de exemplo](https://github.com/Azure/azure-signalr/tree/dev/samples) que contêm a reinicialização da lógica de conexão com a estratégia *Always Retry* :

    * [ASP.NET Core cliente C#](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [ASP.NET Core cliente JavaScript](https://github.com/Azure/azure-signalr/blob/release/1.0.0-preview1/samples/ChatSample/wwwroot/index.html#L164)

    * [Cliente ASP.NET C#](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [Cliente ASP.NET JavaScript](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

[Está tendo problemas ou comentários sobre a solução de problemas? Informe-nos.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu sobre como lidar com os problemas comuns. Você também pode aprender mais métodos genéricos de solução de problemas. 

> [!div class="nextstepaction"]
> [Como solucionar problemas de conectividade e entrega de mensagens](./signalr-howto-troubleshoot-method.md)