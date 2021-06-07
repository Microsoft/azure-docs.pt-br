---
title: Perguntas frequentes sobre o Serviço do Azure SignalR
description: Veja as respostas para perguntas frequentes sobre o Serviço do Azure SignalR, incluindo solução de problemas e cenários de uso típicos.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d3b84756f390930be5124c6bda54d07078d29053
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166914"
---
# <a name="azure-signalr-service-faq"></a>Perguntas frequentes sobre o Serviço do Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>O Serviço do Azure SignalR está pronto para uso em produção?

Sim, o suporte para [ASP.NET Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) e [ASP.NET SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr) está em disponibilidade geral.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando há vários servidores de aplicativos, as mensagens de cliente são enviadas a todos os servidores ou apenas a um deles?

Há um mapeamento de um para um entre um cliente e um servidor de aplicativos. Mensagens de um cliente sempre são enviadas para o mesmo servidor de aplicativos.

O mapeamento é mantido até que o cliente ou o servidor de aplicativos desconecte.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se um dos meus servidores de aplicativos estiver inativo, como poderei encontrá-lo e ser notificado?

O Serviço do Azure SignalR monitora pulsações de servidores de aplicativos.
Se as pulsações não forem recebidas por um período de tempo especificado, o servidor de aplicativos será considerado offline. Todas as conexões de cliente mapeadas para esse servidor de aplicativos serão desconectadas.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Por que meu `IUserIdProvider` personalizado lança uma exceção quando eu alterno do SDK do ASP.NET Core SignalR para o SDK do Serviço do Azure SignalR?

O parâmetro `HubConnectionContext context` é diferente entre o SDK do ASP.NET Core SignalR e o SDK do Serviço do Azure SignalR quando `IUserIdProvider` é chamado.

No ASP.NET Core SignalR, `HubConnectionContext context` é o contexto da conexão de cliente física com os valores válidos para todas as propriedades.

No SDK de Serviço do Azure SignalR, `HubConnectionContext context` é o contexto de conexão de cliente lógica. O cliente físico está conectado à instância do Serviço do Azure SignalR, portanto, apenas um número limitado de propriedades é fornecido.

Por enquanto, apenas `HubConnectionContext.GetHttpContext()` e `HubConnectionContext.User` estão disponíveis para acesso.
Você pode [verificar o código-fonte](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Posso configurar os transportes disponíveis no Serviço do Azure SignalR no lado do servidor, com o ASP.NET Core SignalR? Por exemplo, posso desabilitar o transporte de WebSocket?

Não.

O Serviço do Azure SignalR fornece todos os três transportes compatíveis por padrão com o ASP.NET Core SignalR. Ele não é configurável. O Serviço do Azure SignalR cuidará das conexões e transportes para todas as conexões de cliente.

Você pode configurar os transportes do lado do cliente conforme documentado na [configuração do SignalR para ASP.NET Core](/aspnet/core/signalr/configuration#configure-allowed-transports-1).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Qual é o significado das métricas, como contagem de mensagens ou contagem de conexões, mostradas no portal do Azure? Qual tipo de agregação devo escolher?

Você pode encontrar detalhes sobre como calculamos essas métricas em [Mensagens e conexões no Serviço do Azure SignalR](signalr-concept-messages-and-connections.md).

No painel de visão geral dos recursos do Serviço do Azure SignalR, já escolhemos o tipo de agregação apropriado para você. Se você for até o painel de métricas, poderá usar o tipo de agregação de [Mensagens e conexões no Serviço do Azure SignalR](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr) como uma referência.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Qual é o significado dos modos de serviço `Default`, `Serverless` e `Classic`? Como faço para escolher?

Para novos aplicativos, apenas o modo padrão e sem servidor deve ser usado. A principal diferença é se você tem servidores de aplicativos que estabelecem conexões de servidor com o serviço (ou seja, usam `AddAzureSignalR()` para se conectar ao serviço). Se sim, use o modo padrão; caso contrário, use o modo sem servidor.

O modo clássico foi criado para oferecer compatibilidade com versões anteriores para aplicativos existentes, portanto não deve ser usado para novos aplicativos.

Para obter mais informações sobre o modo de serviço, confira [Modo de serviço no Serviço do Azure SignalR](concept-service-mode.md).

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>Posso enviar mensagens do cliente no modo sem servidor?

Você poderá enviar a mensagem do cliente se configurar upstream em sua instância do SignalR. Upstream é um conjunto de pontos de extremidade que pode receber mensagens e eventos de conexão do Serviço do SignalR. Se nenhum upstream estiver configurado, as mensagens do cliente serão ignoradas.

Para obter mais informações sobre upstream, confira [Configurações de upstream](concept-upstream.md).

O upstream está atualmente em versão prévia pública.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Há alguma diferença de recursos ao usar o Serviço do Azure SignalR ou o ASP.NET SignalR?

Quando você usa o Serviço do Azure SignalR, algumas APIs e recursos do ASP.NET SignalR não têm suporte:
- A capacidade de passar um estado arbitrário entre clientes e o hub (geralmente chamada de `HubState`) não terá suporte.
- Não há suporte para classe `PersistentConnection`.
- Não há suporte para o *transporte de Quadro Contínuo*.
- O Serviço do Azure SignalR não repete mais as mensagens enviadas ao cliente quando o cliente está offline.
- Quando você está usando o Serviço do Azure SignalR, o tráfego para uma conexão de cliente sempre é roteado (também chamado de *adesivo*) para uma instância do servidor de aplicativos durante a conexão.

O suporte para o ASP.NET SignalR concentra-se na compatibilidade. Portanto, nem todos os novos recursos do ASP.NET Core SignalR têm suporte. Por exemplo, *MessagePack* e *Streaming* estão disponíveis somente para aplicativos do ASP.NET Core SignalR.

Você pode configurar o Serviço do Azure SignalR para diferentes modos de serviço: `Classic`, `Default` e `Serverless`. O modo `Serverless` não tem suporte para o ASP.NET. A API REST do plano de dados também não tem suporte.

## <a name="where-does-my-data-reside"></a>Onde meus dados residem?

O Serviço do Azure SignalR funciona como um serviço processador de dados. Ele não armazena nenhum conteúdo do cliente e a residência de dados é incluída por design. Se você usar o Serviço do Azure SignalR junto com outros serviços do Azure, como o Armazenamento do Azure para diagnóstico, confira [este white paper](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) para obter diretrizes de como manter a residência de dados em regiões do Azure.
