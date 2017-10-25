---
title: "Modelo de dados do Azure Application Insights Telemetry – contexto de telemetria | Microsoft Docs"
description: Modelo de dados de contexto do Application Insights Telemetry
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/15/2017
ms.author: sergkanz
ms.openlocfilehash: d6a0cad8bda6ca68aa691867e84f540c5ac9f6f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="telemetry-context-application-insights-data-model"></a>Contexto de telemetria: modelo de dados do Application Insights

Cada item de telemetria pode ter campos de contexto fortemente tipados. Cada campo habilita um cenário de monitoramento específico. Use a coleção de propriedades personalizadas para armazenar informações contextuais personalizadas ou específicas do aplicativo.


##<a name="application-version"></a>Versão do aplicativo

As informações nos campos de contexto de aplicativo sempre são sobre o aplicativo que está enviando a telemetria. A versão do aplicativo é usada para analisar as alterações de tendências no comportamento do aplicativo e sua correlação com as implantações.

Comprimento máximo: 1024


##<a name="client-ip-address"></a>Endereço IP do cliente

O endereço IP do dispositivo cliente. Há suporte para IPv4 e IPv6. Quando a telemetria é enviada de um serviço, o contexto de local é sobre o usuário que iniciou a operação no serviço. O Application Insights extrai as informações de localização geográfica do IP do cliente e as trunca. Portanto, o IP do cliente por si só não pode ser usado como informações de identificação do usuário final. 

Comprimento máximo: 46


##<a name="device-type"></a>Tipo de dispositivo

Originalmente, esse campo era usado para indicar o tipo do dispositivo que o usuário final do aplicativo estava usando. Hoje, é usado principalmente para distinguir a telemetria JavaScript com o tipo de dispositivo 'Navegador' da telemetria do lado do servidor com o tipo de dispositivo 'PC'.

Comprimento máximo: 64


##<a name="operation-id"></a>ID da operação

Um identificador exclusivo da operação raiz. Esse identificador permite a telemetria de grupo em vários componentes. Confira [correlação de telemetria](application-insights-correlation.md) para obter detalhes. A id da operação é criada por uma solicitação ou uma exibição de página. Toda a outra telemetria define esse campo como o valor para a solicitação que a contém ou a exibição de página. 

Comprimento máximo: 128


##<a name="parent-operation-id"></a>ID de operação pai

O identificador exclusivo do pai imediato do item de telemetria. Confira [correlação de telemetria](application-insights-correlation.md) para obter detalhes.

Comprimento máximo: 128


##<a name="operation-name"></a>Nome da operação

O nome (grupo) da operação. O nome da operação é criado por uma solicitação ou uma exibição de página. Todos os outros itens de telemetria definem esse campo como o valor para a solicitação que a contém ou uma exibição de página. O nome da operação é usado para localizar todos os itens de telemetria de um grupo de operações (por exemplo 'GET Home/Index'). Essa propriedade de contexto é usada para responder a perguntas como "quais são as exceções típica lançadas nesta página".

Comprimento máximo: 1024


##<a name="synthetic-source-of-the-operation"></a>Origem sintética da operação

Nome da origem sintética. Parte da telemetria do aplicativo pode representar o tráfego sintético. Pode ser o rastreador da Web que está indexando o site, testes de disponibilidade de site ou rastreamentos de bibliotecas de diagnóstico como o SDK do Application Insights em si.

Comprimento máximo: 1024


##<a name="session-id"></a>Id da sessão

ID da sessão – a instância de interação do usuário com o aplicativo. As informações nos campos de contexto de sessão sempre são sobre o usuário final. Quando a telemetria é enviada de um serviço, o contexto de sessão é sobre o usuário que iniciou a operação no serviço.

Comprimento máximo: 64


##<a name="anonymous-user-id"></a>Id de usuário anônimo

Id de usuário anônimo. Representa o usuário final do aplicativo. Quando a telemetria é enviada de um serviço, o contexto de usuário é sobre o usuário que iniciou a operação no serviço.

A [Amostragem](app-insights-sampling.md) é uma das técnicas para minimizar a quantidade de telemetria coletada. O algoritmo de amostragem tenta fazer a amostragem de toda a telemetria correlacionada. A id de usuário anônimo é usado para a geração de pontuação de amostragem. A id de usuário anônimo deve ser um valor suficientemente aleatório. 

O uso da id de usuário anônimo para armazenar o nome de usuário é um uso indevido do campo. Usar id de usuário Autenticado.

Comprimento máximo: 128


##<a name="authenticated-user-id"></a>Id de usuário autenticado

Id de usuário autenticado. O oposto da id de usuário anônimo, esse campo representa o usuário com um nome amigável. As informações de PII não são coletadas por padrão pela maioria dos SDKs.

Comprimento máximo: 1024


##<a name="account-id"></a>Id de conta

Em aplicativos multilocatário, é a ID da conta ou o nome com o qual o usuário está agindo. Exemplos podem ser a ID da assinatura do Portal do Azure ou a plataforma de blogs de nome de blog.

Comprimento máximo: 1024


##<a name="cloud-role"></a>Função de nuvem

Nome da função da qual o aplicativo faz parte. É mapeada diretamente para o nome da função no Azure. Também pode ser usado para distinguir microsserviços, que fazem parte de um único aplicativo.

Comprimento máximo: 256


##<a name="cloud-role-instance"></a>Instância de função de nuvem

Nome da instância em que o aplicativo está sendo executado. Nome do computador para o nome de instância local do Azure.

Comprimento máximo: 256


##<a name="internal-sdk-version"></a>Interno: versão de SDK

Versão do SDK. Veja https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification para saber mais.

Comprimento máximo: 64


##<a name="internal-node-name"></a>Interno: nome do nó

Este campo representa o nome do nó usado para fins de cobrança. Usado para substituir a detecção padrão de nós.

Comprimento máximo: 256


## <a name="next-steps"></a>Próximas etapas

- Saiba como [estender e filtrar a telemetria](app-insights-api-filtering-sampling.md).
- Consulte [modelo de dados](application-insights-data-model.md) para modelo de dados e tipos do Application Insights.
- Confira a coleção de propriedades de contexto padrão [configuração](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
