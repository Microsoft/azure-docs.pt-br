---
title: Início Rápido – manipular eventos de SMS para relatórios de entrega e mensagens de entrada
titleSuffix: An Azure Communication Services quickstart
description: Saiba como manipular eventos de SMS usando os Serviços de Comunicação do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7a88d8a0065ca7f5cb054d23295e5cfbc23489ca
ms.sourcegitcommit: 91361cbe8fff7c866ddc4835251dcbbe2621c055
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105728582"
---
# <a name="quickstart-handle-sms-events-for-delivery-reports-and-inbound-messages"></a>Início Rápido: manipular eventos de SMS para relatórios de entrega e mensagens de entrada

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Introdução aos Serviços de Comunicação do Azure usando a Grade de Eventos do Azure para manipular eventos de SMS dos Serviços de Comunicação.

## <a name="about-azure-event-grid"></a>Sobre a Grade de Eventos do Azure

A [Grade de Eventos do Azure](../../../event-grid/overview.md) é um serviço de eventos baseado em nuvem. Neste artigo, você aprenderá a assinar eventos para [eventos do serviço de comunicação](../../../event-grid/event-schema-communication-services.md) e disparar um evento para exibir o resultado. Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. Neste artigo, enviaremos os eventos para um aplicativo Web que coleta e exibe as mensagens.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um recurso do Serviço de Comunicação do Azure. Mais detalhes podem ser encontrados no início rápido [Criar um Recurso de Comunicação do Azure](../create-communication-resource.md).
- Um número de telefone habilitado para SMS. [Obter um número de telefone](./get-phone-number.md).

## <a name="setting-up"></a>Configurando

### <a name="enable-event-grid-resource-provider"></a>Habilitar provedor de recursos da Grade de Eventos

Caso você ainda não tenha usado a Grade de Eventos em sua assinatura do Azure, talvez seja necessário registrar o provedor de recursos da Grade de Eventos seguindo as etapas abaixo:

No Portal do Azure:

1. Selecione **Assinaturas** no menu à esquerda.
2. Selecione a assinatura que você está usando para a Grade de Eventos.
3. No menu à esquerda, em **Configurações**, selecione **Provedores de recursos**.
4. Encontre **Microsoft.EventGrid**.
5. Se não estiver registrado, selecione **Registrar**.

A conclusão do registro pode demorar um pouco. Selecione **Atualizar** para atualizar o status. Quando **Status** for **Registrado**, você está pronto para continuar.

### <a name="event-grid-viewer-deployment"></a>Implantação do Visualizador da Grade de Eventos

Para este guia de início rápido, usaremos o [Exemplo do Visualizador da Grade de Eventos do Azure](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) para exibir eventos quase em tempo real. Isso dará ao usuário a experiência de ter um feed em tempo real. Além disso, o conteúdo do evento também deve estar disponível para inspeção.

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>Assinar os eventos de SMS usando webhooks

No portal, navegue até o recurso dos Serviços de Comunicação do Azure criado. Dentro do recurso dos Serviços de Comunicação, selecione **Eventos** no menu à esquerda da página **Serviços de Comunicação**.

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="Captura de tela mostrando a seleção do botão de assinatura de evento na página de eventos de um recurso.":::

Pressione **Adicionar Assinatura de Evento** para entrar no assistente de criação.

Na página **Criar Assinatura de Evento**, insira um **nome** para a assinatura do evento.

Você pode assinar eventos específicos para informar à Grade de Eventos qual dos eventos de SMS você deseja acompanhar e aonde enviar os eventos. Selecione os eventos que você gostaria de assinar no menu suspenso. Para o SMS, você terá a opção de escolher `SMS Received` e `SMS Delivery Report Received`.

Se for solicitado que você forneça um **Nome do Tópico do Sistema**, fique à vontade para fornecer uma cadeia de caracteres exclusiva. O campo não tem impacto sobre sua experiência e é usado para fins de telemetria interna.

Confira a lista completa de [eventos com suporte dos Serviços de Comunicação do Azure](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services).

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="Captura de tela mostrando os tipos de evento de SMS Recebido e Relatório de Entrega de SMS Recebido sendo selecionados.":::

Selecione **Web Hook** para o **Tipo de ponto de extremidade**.

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="Captura de tela mostrando o campo Tipo de Ponto de Extremidade sendo definido como Web Hook.":::

Para o **Ponto de extremidade**, clique em **Selecionar um ponto de extremidade** e insira a URL do seu aplicativo Web.

Nesse caso, usaremos a URL do [Exemplo do Visualizador da Grade de Eventos do Azure](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) configurado anteriormente neste guia de início rápido. A URL do exemplo estará no formato: `https://{{site-name}}.azurewebsites.net/api/updates`

Em seguida, selecione **Confirmar Seleção**.

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="Captura de tela mostrando a confirmação de um Ponto de Extremidade de Web Hook.":::

## <a name="viewing-sms-events"></a>Exibir eventos de SMS

### <a name="triggering-sms-events"></a>Disparar eventos de SMS

Para exibir gatilhos de eventos, precisamos gerar eventos primeiro.

- Os eventos `SMS Received` são gerados quando o número de telefone dos Serviços de Comunicação recebe uma mensagem de texto. Para disparar um evento, basta enviar uma mensagem do seu telefone para o número de telefone anexado ao seu recurso dos Serviços de Comunicação.
- Os eventos `SMS Delivery Report Received` são gerados quando você envia um SMS para um usuário com um número de telefone dos Serviços de Comunicação. Para disparar um evento, você precisa habilitar `Delivery Report` nas opções do [SMS enviado](../telephony-sms/send.md). Tente enviar uma mensagem para seu telefone com `Delivery Report`. A realização dessa ação gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

Confira a lista completa de [eventos com suporte dos Serviços de Comunicação do Azure](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services).

### <a name="receiving-sms-events"></a>Receber eventos de SMS

Depois de realizar uma das ações acima, você observará que os eventos `SMS Received` e `SMS Delivery Report Received` são enviados para seu ponto de extremidade. Esses eventos aparecerão no [Exemplo do Visualizador da Grade de Eventos do Azure](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) configurado no início. Você pode pressionar o ícone de olho ao lado do evento para ver todo o conteúdo. Os eventos terão esta aparência:

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="Captura de tela mostrando o Esquema da Grade de Eventos para um Evento de SMS Recebido.":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="Captura de tela mostrando o Esquema de Grade de Eventos para um Evento de Relatório de Entrega de SMS.":::

Saiba mais sobre os [esquemas de evento e outros conceitos de eventos](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services).

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a consumir eventos de SMS. Você pode receber mensagens SMS criando uma assinatura da Grade de Eventos.

> [!div class="nextstepaction"]
> [Enviar SMS](../telephony-sms/send.md)

Você também pode querer:


 - [Saber mais sobre os conceitos de manipulação de eventos](../../../event-grid/event-schema-communication-services.md)
 - [Saber mais sobre a Grade de Eventos](../../../event-grid/overview.md)
