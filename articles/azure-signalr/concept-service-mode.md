---
title: Modo de serviço no serviço de Signaler do Azure
description: Uma visão geral dos diferentes modos de serviço no serviço de Signaler do Azure, explique suas diferenças e cenários de usuário aplicáveis
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 60f1ab0440120cb9a96e6c05a4fc1987ead29188
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143255"
---
# <a name="service-mode-in-azure-signalr-service"></a>Modo de serviço no serviço de Signaler do Azure

O modo de serviço é um conceito importante no serviço de Signaler do Azure. Ao criar um novo recurso do Signalr, você será solicitado a especificar um modo de serviço:

:::image type="content" source="media/concept-service-mode/create.png" alt-text="Escolher modo de serviço ao criar":::

Você também pode alterá-lo posteriormente no menu configurações:

:::image type="content" source="media/concept-service-mode/update.png" alt-text="Escolher modo de serviço ao criar":::

O serviço do Azure Signalr atualmente dá suporte a três modos de serviço: **padrão**, sem **servidor** e **clássico**. O recurso do Signalr se comportará de forma diferente em modos diferentes. Neste artigo, você aprenderá suas diferenças e como escolher o modo de serviço correto com base em seu cenário.

## <a name="default-mode"></a>Modo padrão

O modo padrão é o valor padrão para o modo de serviço quando você cria um novo recurso de Signalr. Nesse modo, o aplicativo funciona como um aplicativo Signaler típico ASP.NET Core (ou ASP.NET), em que você tem um servidor Web que hospeda um Hub (chamado de servidor Hub doravante) e os clientes podem ter a comunicação duplex com o servidor Hub. A única diferença é, em vez de conectar o cliente e o servidor diretamente, o cliente e o servidor se conectam ao serviço do Signalr e usam o serviço como um proxy. Veja abaixo um diagrama que ilustra a estrutura típica do aplicativo no modo padrão:

:::image type="content" source="media/concept-service-mode/default.png" alt-text="Escolher modo de serviço ao criar":::

Portanto, se você tiver um aplicativo Signalr e quiser integrá-lo com o serviço Signalr, o modo padrão deverá ser a escolha certa para a maioria dos casos.

### <a name="connection-routing-in-default-mode"></a>Roteamento de conexão no modo padrão

No modo padrão, haverá conexões WebSocket entre o servidor de Hub e o serviço de sinalização (chamadas de conexões de servidor). Essas conexões são usadas para transferir mensagens entre o servidor e o cliente. Quando um novo cliente estiver conectado, o serviço de sinalização roteará o cliente para um servidor de Hub (Suponha que você tenha mais de um servidor) por meio de conexões de servidor existentes. Em seguida, a conexão do cliente irá para o mesmo servidor de Hub durante seu tempo de vida. Quando o cliente envia mensagens, eles sempre vão para o mesmo servidor de Hub. Com esse comportamento, você pode manter com segurança alguns Estados para conexões individuais no servidor de Hub. Por exemplo, se você quiser transmitir algo entre servidor e cliente, não será necessário considerar o caso em que os pacotes de dados vão para servidores diferentes.

> [!IMPORTANT]
> Isso também significa que, no modo padrão, o cliente não pode se conectar sem que o servidor esteja conectado primeiro. Se todos os servidores de Hub forem desconectados devido à interrupção da rede ou à reinicialização do servidor, a conexão do cliente receberá um erro informando que nenhum servidor está conectado. Portanto, é sua responsabilidade garantir a qualquer momento que haja pelo menos um servidor de Hub conectado ao serviço de sinalização (por exemplo, ter vários servidores de Hub e verificar se eles não ficarão offline ao mesmo tempo para coisas como manutenção).

Esse modelo de roteamento também significa que quando um servidor Hub fica offline, as conexões roteadas para esse servidor serão descartadas. Portanto, você deve esperar uma queda de conexão quando o servidor de Hub estiver offline para manutenção e manipular a reconexão corretamente para que ele não tenha impacto negativo em seu aplicativo.

## <a name="serverless-mode"></a>Modo sem servidor

O modo sem servidor, como o nome indica, é um modo em que não é possível ter nenhum servidor de Hub. Comparando com o modo padrão, neste modo, o cliente não requer que o servidor de Hub se conecte. Todas as conexões são conectadas ao serviço em um modo "sem servidor" e o serviço é responsável por manter as conexões de cliente como lidar com pings de cliente (no modo padrão, isso é tratado por servidores de Hub).

Além disso, não há nenhuma conexão de servidor nesse modo (se você tentar usar o SDK do serviço para estabelecer a conexão do servidor, receberá um erro). Portanto, também não há nenhum roteamento de conexão e de servidor (conforme descrito na seção modo padrão). Mas você ainda pode ter um aplicativo do lado do servidor para enviar mensagens por push aos clientes. Isso pode ser feito de duas maneiras: Use as [APIs REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) para envio único ou por meio de uma conexão WebSocket para que você possa enviar várias mensagens com mais eficiência (Observe que essa conexão WebSocket é diferente da conexão do servidor).

> [!NOTE]
> Ambos os modos de API REST e WebSocket têm suporte no [SDK de gerenciamento](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md)de serviço do signalr. Se você estiver usando um idioma diferente do .NET, também poderá invocar manualmente as APIs REST seguindo esta [especificação](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md).
>
> Se você estiver usando Azure Functions, poderá usar as [associações de serviço de sinalização para Azure Functions](../azure-functions/functions-bindings-signalr-service.md) (doravante chamado Associação de função) para enviar mensagens como uma associação de saída.

Também é possível que o aplicativo de servidor receba mensagens e eventos de conexão de clientes. O serviço fornecerá mensagens e eventos de conexão para pontos de extremidade pré-configurados (chamados upstream) usando WebHooks. Comparando ao modo padrão, não há nenhuma garantia de adesão e as solicitações HTTP podem ser menos eficientes do que as conexões WebSocket.

Para obter mais informações sobre como configurar upstream, consulte este [documento](./concept-upstream.md).

Veja abaixo um diagrama que ilustra como funciona o modo sem servidor:

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="Escolher modo de serviço ao criar":::

> [!NOTE]
> Observe que, no modo padrão, você também pode usar a associação de função/SDK de gerenciamento/API REST para enviar mensagens diretamente para o cliente se não quiser passar pelo servidor de Hub. Mas, no modo padrão, as conexões de cliente ainda são tratadas por servidores de Hub e upstream não funcionará nesse modo.

## <a name="classic-mode"></a>Modo clássico

O clássico é um modo misto de modo padrão e sem servidor. Nesse modo, o modo de conexão é decidido por se haver um servidor Hub conectado quando a conexão do cliente é estabelecida. Se houver um servidor de Hub, a conexão de cliente será roteada para um servidor de Hub. Caso contrário, ele entrará em um modo sem servidor em que a mensagem cliente para servidor não pode ser entregue ao servidor de Hub. Isso causará algumas discrepâncias, por exemplo, se todos os servidores de Hub estiverem indisponíveis por um curto período, todas as conexões de cliente criadas durante esse tempo estarão no modo sem servidor e não poderão enviar mensagens ao servidor de Hub.

> [!NOTE]
> O modo clássico é principalmente para compatibilidade com versões anteriores para os aplicativos criados antes de o modo padrão e sem servidor. É altamente recomendável não usar esse modo mais. Para novos aplicativos, escolha padrão ou sem servidor com base em seu cenário. Para aplicativos existentes, também é recomendável revisar seus casos de uso e escolher um modo de serviço adequado.

O modo clássico também não dá suporte a alguns novos recursos, como upstream no modo sem servidor.

## <a name="choose-the-right-service-mode"></a>Escolha o modo de serviço correto

Agora você deve entender as diferenças entre os modos de serviço e saber como escolher entre eles. Como você já aprendeu na seção anterior, o modo clássico não é incentivado e você deve escolher apenas entre padrão e sem servidor. Aqui estão algumas dicas que podem ajudá-lo a fazer a escolha certa para novos aplicativos e desativar o modo clássico para aplicativos existentes.

* Se você já estiver familiarizado com a forma como funciona a biblioteca do Signalr e deseja mudar de um Signalr auto-hospedado para usar o serviço de Signaler do Azure, escolha modo padrão. O modo padrão funciona exatamente da mesma forma que o Signalr auto-hospedado (e você pode usar o mesmo modelo de programação na biblioteca do Signalr), o serviço Signalr atua apenas como um proxy entre clientes e servidores de Hub.

* Se você estiver criando um novo aplicativo e não quiser manter as conexões servidor Hub e servidor, escolha modo sem servidor. Esse modo geralmente funciona junto com Azure Functions para que você não precise manter nenhum servidor. Você ainda pode ter comunicações duplex (com API REST/SDK de gerenciamento/Associação de função + upstream), mas o modelo de programação será diferente da biblioteca do Signalr.

* Se você tiver servidores de Hub para atender as conexões de cliente e o aplicativo de back-end para enviar mensagens por push diretamente aos clientes (por exemplo, por meio da API REST), você ainda deverá escolher o modo padrão. Tenha em mente que a principal diferença entre o modo padrão e sem servidor é se você tem servidores de Hub e como as conexões de cliente são roteadas. API REST/SDK de gerenciamento/Associação de função pode ser usada em ambos os modos.

* Se você realmente tem um cenário misto, por exemplo, tem dois hubs diferentes no mesmo recurso do Signalr, um usado como um Hub do Signalr tradicional e o outro usado com Azure Functions e não tem um servidor de Hub, você deve considerar realmente separá-los em dois recursos do Signalr, um no modo padrão e outro no modo sem servidor.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o modo padrão e sem servidor, leia os seguintes artigos:

* [Componentes internos do Serviço do Azure SignalR](signalr-concept-internals.md)

* [Desenvolvimento de funções do Azure e a configuração com o serviço do Azure SignalR](signalr-concept-serverless-development-config.md)