---
title: Como dimensionar com várias instâncias de serviço do Azure SignalR
description: Em muitos cenários de colocação em escala, o cliente geralmente precisa provisionar várias instâncias e configurar para usá-las em conjunto, para criar uma implantação em larga escala. Por exemplo, a fragmentação requer que suportam a várias instâncias.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809140"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Como dimensionar o SignalR Service com várias instâncias?
O SDK mais recente do serviço SignalR dá suporte a vários pontos de extremidade para instâncias de SignalR Service. Você pode usar esse recurso para dimensionar as conexões simultâneas, ou usá-lo para mensagens entre regiões.

## <a name="for-aspnet-core"></a>Para o ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos de extremidade de configuração?

Configuração com a chave `Azure:SignalR:ConnectionString` ou `Azure:SignalR:ConnectionString:` para cadeia de conexão do SignalR Service.

Se a chave começa com `Azure:SignalR:ConnectionString:`, ele deve estar no formato `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, onde `Name` e `EndpointType` são propriedades do `ServiceEndpoint` de objeto e são acessíveis a partir do código.

Você pode adicionar várias cadeias de conexão da instância usando o seguinte `dotnet` comandos:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Como adicionar vários pontos de extremidade do código?

Um `ServicEndpoint` classe foi introduzida para descrever as propriedades de um ponto de extremidade de serviço do Azure SignalR.
Você pode configurar vários pontos de extremidade de instância ao usar o SDK de serviço do Azure SignalR por meio de:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Como personalizar o roteador de ponto de extremidade?

Por padrão, o SDK usa o [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) para pegar pontos de extremidade.

#### <a name="default-behavior"></a>Comportamento padrão 
1. Roteamento de solicitação do cliente

    Quando cliente `/negotiate` com o servidor de aplicativo. Por padrão, o SDK **seleciona aleatoriamente** um ponto de extremidade do conjunto de pontos de extremidade de serviço disponível.

2. Roteamento de mensagens do servidor

    Quando * enviar mensagem para um determinado * * conexão * * * e a conexão de destino será roteado para o servidor atual, a mensagem é enviada diretamente ao ponto de extremidade conectado. Caso contrário, as mensagens sejam transmitidas para cada ponto de extremidade do Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Personalizar o algoritmo de roteamento
Você pode criar seu próprio roteador quando você tem conhecimento especial para identificar quais as mensagens devem ir para os pontos de extremidade.

Um roteador personalizado está definido abaixo como exemplo quando grupos iniciando com `east-` sempre vá para o ponto de extremidade chamado `east`:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Comportamento de negociação de outro exemplo abaixo, que substitui o padrão Selecionar os pontos de extremidade depende de onde se encontra o servidor de aplicativo.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Não se esqueça de registrar o roteador para o contêiner de injeção de dependência usando:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Para o ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Como adicionar vários pontos de extremidade de configuração?

Configuração com a chave `Azure:SignalR:ConnectionString` ou `Azure:SignalR:ConnectionString:` para cadeia de conexão do SignalR Service.

Se a chave começa com `Azure:SignalR:ConnectionString:`, ele deve estar no formato `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, onde `Name` e `EndpointType` são propriedades do `ServiceEndpoint` de objeto e são acessíveis a partir do código.

Você pode adicionar várias cadeias de conexão de instância para `web.config`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Como adicionar vários pontos de extremidade do código?

Um `ServicEndpoint` classe foi introduzida para descrever as propriedades de um ponto de extremidade de serviço do Azure SignalR.
Você pode configurar vários pontos de extremidade de instância ao usar o SDK de serviço do Azure SignalR por meio de:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Como personalizar o roteador?

A única diferença entre o SignalR do ASP.NET e o SignalR do ASP.NET Core é o tipo de contexto http para `GetNegotiateEndpoint`. Para o SignalR do ASP.NET, é de [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) tipo.

Abaixo está personalizado negociar exemplo para ASP.NET SignalR:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Não se esqueça de registrar o roteador para o contêiner de injeção de dependência usando:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Configuração em cenários entre regiões

O `ServiceEndpoint` objeto tem uma `EndpointType` propriedade com o valor `primary` ou `secondary`.

`primary` pontos de extremidade são pontos de extremidade preferidos para receber o tráfego de cliente e são considerados como tendo mais confiáveis conexões de rede; `secondary` pontos de extremidade são considerados como tendo menos confiáveis conexões de rede e são usados somente para servidor levando ao tráfego de cliente, por exemplo, difundindo mensagens, não para cliente levando para o tráfego do servidor.

Em casos de região cruzada, a rede pode ser instável. Para o servidor de um aplicativo localizado em *Leste dos EUA*, o ponto de extremidade do SignalR Service localizada na mesma *Leste dos EUA* região pode ser configurada como `primary` e pontos de extremidade em outras regiões marcadas como `secondary`. Nessa configuração, os pontos de extremidade de serviço em outras regiões podem **receber** mensagens deste *Leste dos EUA* do servidor de aplicativo, mas haverá nenhum **entre regiões** clientes são roteadas para Esse servidor de aplicativo. A arquitetura é mostrada no diagrama a seguir:

![Cross-Geo Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Quando um cliente tenta `/negotiate` com o servidor de aplicativo, com o roteador padrão, o SDK **seleciona aleatoriamente** um ponto de extremidade do conjunto de disponível `primary` pontos de extremidade. Quando o ponto de extremidade está disponível, SDK, em seguida, **seleciona aleatoriamente** de todos disponíveis `secondary` pontos de extremidade. O ponto de extremidade está marcado como **disponível** quando a conexão entre o servidor e o ponto de extremidade de serviço está ativo.

No cenário entre regiões, quando um cliente tenta `/negotiate` com o servidor de aplicativo hospedado no *Leste dos EUA*, por padrão ele sempre retorna o `primary` ponto de extremidade localizados na mesma região. Quando todos os *Leste dos EUA* pontos de extremidade não estiverem disponíveis, o cliente é redirecionado para pontos de extremidade em outras regiões. Failover seção a seguir descreve o cenário detalhadamente.

![Negociar normal](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Faça o failover

Quando todos os `primary` pontos de extremidade não estão disponíveis, do cliente `/negotiate` adota do disponíveis `secondary` pontos de extremidade. Esse mecanismo de failover requer que cada ponto de extremidade deve servir como `primary` ponto de extremidade do servidor de pelo menos um aplicativo.

![Faça o failover](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu sobre como configurar várias instâncias no mesmo aplicativo para dimensionamento, a fragmentação e a cenários entre regiões.

Dá suporte a pontos de extremidade de várias também pode ser usada em cenários de recuperação de desastres e disponibilidade alta.

> [!div class="nextstepaction"]
> [Configurar SignalR Service para recuperação de desastres e alta disponibilidade](./signalr-concept-disaster-recovery.md)
