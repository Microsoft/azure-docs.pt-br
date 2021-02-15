---
title: Como enviar eventos do Auth0 para o Azure usando a Grade de Eventos do Azure
description: Como enviar eventos do Auth0 para os serviços do Azure com a Grade de Eventos do Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: a66a60cb926b933a6b0628a67506d0d52ab7a905
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077856"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Integrar a Grade de Eventos do Azure com Auth0

Este artigo descreve como conectar suas contas do Auth0 e do Azure criando um tópico de parceiro de Grade de Eventos.

Veja os [códigos de tipo de evento Auth0](https://auth0.com/docs/logs/references/log-event-type-codes) para obter uma lista completa dos eventos aos quais o Auth0 oferece suporte

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Enviar eventos do Auth0 para a Grade de Eventos do Azure
Para enviar eventos do Auth0 para o Azure:

1. Habilitar provedor de recursos da Grade de Eventos
1. Configurar um tópico de parceiro do Auth0 no painel do Auth0
1. Ativar o tópico de parceiro no Azure
1. Assinar eventos do Auth0

Para saber mais sobre esses conceitos, confira [conceitos](concepts.md) da Grade de Eventos.

### <a name="enable-event-grid-resource-provider"></a>Habilitar provedor de recursos da Grade de Eventos
A menos que você tenha usado a Grade de Eventos antes, precisará registrar o provedor de recursos da Grade de Eventos. Se você tiver usado a Grade de Eventos antes, pule para a próxima seção.

No Portal do Azure:
1. Escolha Assinaturas no menu à esquerda
1. Escolha a assinatura que você está usando para a Grade de Eventos
1. No menu à esquerda, em Configurações, escolha Provedores de recursos
1. Encontre Microsoft.EventGrid
1. Escolha Registrar
1. Atualize para garantir que o status seja alterado para Registrado

### <a name="set-up-an-auth0-partner-topic"></a>Configurar um tópico de parceiro do Auth0
Parte do processo de integração é configurar o Auth0 para uso como uma origem de evento (essa etapa ocorre no [Painel do Auth0](https://manage.auth0.com/)).

1. Faça logon no [Painel do Auth0](https://manage.auth0.com/).
1. Navegue até Logs > Fluxos.
1. Clique em + Criar Fluxo.
1. Escolha Grade de Eventos do Azure e insira um nome exclusivo para o novo fluxo.
1. Crie a origem do evento informando sua ID de Assinatura do Azure, Região do Azure e um nome de Grupo de Recursos. 
1. Clique em Salvar.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Ativar o tópico de parceiro do Auth0 no Azure
Ativar o tópico Auth0 no Azure permite que os eventos fluam do Auth0 para o Azure.

1. Faça logon no Portal do Azure.
1. Pesquise `Partner Topics` na parte superior e clique em `Event Grid Partner Topics` em serviços.
1. Clique no tópico que corresponde ao fluxo que você criou no painel do Auth0.
1. Confirme se o campo `Source` corresponde à sua conta do Auth0.
1. Clique em Ativar.

### <a name="subscribe-to-auth0-events"></a>Assinar eventos do Auth0

#### <a name="create-an-event-handler"></a>Criar um manipulador de eventos
Para testar seu Tópico de Parceiro, você precisará de um manipulador de eventos. Acesse sua assinatura do Azure e crie um serviço que seja compatível como um [manipulador de eventos](event-handlers.md), como uma [Função do Azure](custom-event-to-function.md).

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Assine seu Tópico de Parceiro do Auth0
Assinar seu Tópico de Parceiro do Auth0 permite que você informe a Grade de Eventos em que deseja que seus eventos do Auth0 sejam acessados.

1. Na folha do Tópico de Parceiro da sua integração do Auth0, escolha + Assinatura de Evento na parte superior.
1. Na página Criar Assinatura de Evento:
    1. Insira um nome para a assinatura de evento.
    1. Selecione o serviço do Azure ou o webhook que você criou para o tipo de ponto de extremidade.
    1. Siga as instruções para o serviço específico.
    1. Clique em Criar.

## <a name="testing"></a>Testando
Sua integração com o Tópico de Parceiro Auth0 com o Azure deve estar pronta para uso.

### <a name="verify-the-integration"></a>Verificar a integração
Para verificar se a integração está funcionando conforme o esperado:

1. Faça logon no Painel do Auth0.
1. Navegue até Logs > Fluxos.
1. Clique no fluxo da Grade de Eventos.
1. Uma vez no fluxo, clique na guia Integridade. O fluxo deve estar ativo e, desde que você não veja nenhum erro, o fluxo está funcionando.

Tente [invocar qualquer uma das ações Auth0 que disparam um evento a ser publicado](https://auth0.com/docs/logs/references/log-event-type-codes) para ver o fluxo de eventos.

## <a name="delivery-attempts-and-retries"></a>Tentativas de entrega e novas tentativas
Os eventos Auth0 são entregues ao Azure por meio de um mecanismo de streaming. Cada evento é enviado conforme é disparado em Auth0. Se a grade de eventos não puder receber o evento, o Auth0 tentará entregar o evento até três vezes. Caso contrário, o Auth0 registrará em log a falha para entregar em seu sistema.

## <a name="next-steps"></a>Próximas etapas

- [Tópico de parceiro do Auth0](auth0-overview.md)
- [Visão geral dos tópicos de parceiros](partner-events-overview.md)
- [Torne-se um Parceiro da Grade de Eventos](partner-onboarding-overview.md)