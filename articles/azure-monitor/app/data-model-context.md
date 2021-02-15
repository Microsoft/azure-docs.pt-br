---
title: Modelo de dados do Azure Application Insights Telemetry – contexto de telemetria | Microsoft Docs
description: Modelo de dados de contexto do Application Insights Telemetry
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 76f3be510494a1f005b0080ee8f2390a3fbc3622
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767837"
---
# <a name="telemetry-context-application-insights-data-model"></a>Contexto de telemetria: modelo de dados do Application Insights

Cada item de telemetria pode ter campos de contexto fortemente tipados. Cada campo habilita um cenário de monitoramento específico. Use a coleção de propriedades personalizadas para armazenar informações contextuais personalizadas ou específicas do aplicativo.


## <a name="application-version"></a>Versão do aplicativo

As informações nos campos de contexto de aplicativo sempre são sobre o aplicativo que está enviando a telemetria. A versão do aplicativo é usada para analisar as alterações de tendências no comportamento do aplicativo e sua correlação com as implantações.

Comprimento máximo: 1024


## <a name="client-ip-address"></a>Endereço IP do cliente

O endereço IP do dispositivo cliente. Há suporte para IPv4 e IPv6. Quando a telemetria é enviada de um serviço, o contexto de local é sobre o usuário que iniciou a operação no serviço. O Application Insights extrai as informações de localização geográfica do IP do cliente e as trunca. Portanto, o IP do cliente por si só não pode ser usado como informações de identificação do usuário final. 

Comprimento máximo: 46


## <a name="device-type"></a>Tipo de dispositivo

Originalmente, esse campo era usado para indicar o tipo do dispositivo que o usuário final do aplicativo estava usando. Hoje, é usado principalmente para distinguir a telemetria JavaScript com o tipo de dispositivo 'Navegador' da telemetria do lado do servidor com o tipo de dispositivo 'PC'.

Comprimento máximo: 64


## <a name="operation-id"></a>ID da operação

Um identificador exclusivo da operação raiz. Esse identificador permite a telemetria de grupo em vários componentes. Confira [correlação de telemetria](./correlation.md) para obter detalhes. A id da operação é criada por uma solicitação ou uma exibição de página. Toda a outra telemetria define esse campo como o valor para a solicitação que a contém ou a exibição de página. 

Comprimento máximo: 128


## <a name="parent-operation-id"></a>ID de operação pai

O identificador exclusivo do pai imediato do item de telemetria. Confira [correlação de telemetria](./correlation.md) para obter detalhes.

Comprimento máximo: 128


## <a name="operation-name"></a>Nome da operação

O nome (grupo) da operação. O nome da operação é criado por uma solicitação ou uma exibição de página. Todos os outros itens de telemetria definem esse campo como o valor para a solicitação que a contém ou uma exibição de página. O nome da operação é usado para localizar todos os itens de telemetria de um grupo de operações (por exemplo 'GET Home/Index'). Essa propriedade de contexto é usada para responder a perguntas como "quais são as exceções típica lançadas nesta página".

Comprimento máximo: 1024


## <a name="synthetic-source-of-the-operation"></a>Origem sintética da operação

Nome da origem sintética. Parte da telemetria do aplicativo pode representar o tráfego sintético. Pode ser o rastreador da Web que está indexando o site, testes de disponibilidade de site ou rastreamentos de bibliotecas de diagnóstico como o SDK do Application Insights em si.

Comprimento máximo: 1024


## <a name="session-id"></a>Id da sessão

ID da sessão – a instância de interação do usuário com o aplicativo. As informações nos campos de contexto de sessão sempre são sobre o usuário final. Quando a telemetria é enviada de um serviço, o contexto de sessão é sobre o usuário que iniciou a operação no serviço.

Comprimento máximo: 64


## <a name="anonymous-user-id"></a>Id de usuário anônimo

ID de usuário anônimo. Representa o usuário final do aplicativo. Quando a telemetria é enviada de um serviço, o contexto de usuário é sobre o usuário que iniciou a operação no serviço.

A [Amostragem](./sampling.md) é uma das técnicas para minimizar a quantidade de telemetria coletada. O algoritmo de amostragem tenta fazer a amostragem de toda a telemetria correlacionada. A id de usuário anônimo é usado para a geração de pontuação de amostragem. A id de usuário anônimo deve ser um valor suficientemente aleatório. 

O uso da id de usuário anônimo para armazenar o nome de usuário é um uso indevido do campo. Usar id de usuário Autenticado.

Comprimento máximo: 128


## <a name="authenticated-user-id"></a>Id de usuário autenticado

ID de usuário autenticado. O oposto da ID de usuário anônimo, esse campo representa o usuário com um nome amigável. Isso só é coletado por padrão com o SDK do ASP.NET Framework [`AuthenticatedUserIdTelemetryInitializer`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/WEB/Src/Web/Web/AuthenticatedUserIdTelemetryInitializer.cs) .  

Comprimento máximo: 1024


## <a name="account-id"></a>Id de conta

Em aplicativos multilocatário, é a ID da conta ou o nome com o qual o usuário está agindo. Os exemplos podem ser a ID de assinatura para portal do Azure ou nome de blog para uma plataforma de blog.

Comprimento máximo: 1024


## <a name="cloud-role"></a>Função de nuvem

Nome da função da qual o aplicativo faz parte. É mapeada diretamente para o nome da função no Azure. Também pode ser usado para distinguir microsserviços, que fazem parte de um único aplicativo.

Comprimento máximo: 256


## <a name="cloud-role-instance"></a>Instância de função de nuvem

Nome da instância em que o aplicativo está sendo executado. Nome do computador para o nome de instância local do Azure.

Comprimento máximo: 256


## <a name="internal-sdk-version"></a>Interno: versão de SDK

Versão do SDK. Consulte [Este artigo](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) para obter informações.

Comprimento máximo: 64


## <a name="internal-node-name"></a>Interno: nome do nó

Este campo representa o nome do nó usado para fins de cobrança. Usado para substituir a detecção padrão de nós.

Comprimento máximo: 256


## <a name="next-steps"></a>Próximas etapas

- Saiba como [estender e filtrar a telemetria](./api-filtering-sampling.md).
- Consulte [modelo de dados](data-model.md) para modelo de dados e tipos do Application Insights.
- Confira a coleção de propriedades de contexto padrão [configuração](./configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).

