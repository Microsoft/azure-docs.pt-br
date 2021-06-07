---
title: Início Rápido – Enviar mensagens SMS nos Aplicativos Lógicos do Azure usando os Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste guia de início rápido, aprenda a enviar mensagens SMS nos fluxos de trabalho dos Aplicativos Lógicos do Azure usando o conector dos Serviços de Comunicação do Azure.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 11cce223a0802c20c5fc144eed681327f989a949
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103488373"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>Início Rápido: Enviar mensagens SMS nos Aplicativos Lógicos do Azure com os Serviços de Comunicação do Azure

Usando o conector de SMS dos [Serviços de Comunicação do Azure](../../overview.md) e os [Aplicativos Lógicos do Azure](../../../logic-apps/logic-apps-overview.md), você pode criar fluxos de trabalho automatizados, ou *aplicativos lógicos*, que podem enviar mensagens SMS. Este guia de início rápido mostra como enviar automaticamente mensagens de texto em resposta a um evento de gatilho, que é a primeira etapa em um fluxo de trabalho de aplicativo lógico. Um evento de gatilho pode ser uma mensagem de email de entrada, uma agenda de recorrência, um evento de recurso da [Grade de Eventos do Azure](../../../event-grid/overview.md) ou qualquer outro [gatilho compatível com os Aplicativos Lógicos do Azure](/connectors/connector-reference/connector-reference-logicapps-connectors).

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Captura de tela que mostra o portal do Azure, que está aberto no Designer de Aplicativo Lógico e mostra um exemplo de aplicativo lógico que usa a ação Enviar SMS para o conector dos Serviços de Comunicação do Azure.":::

Embora este guia de início rápido se concentre no uso do conector para responder a um gatilho, você também pode usar o conector para responder a outras ações, que são as etapas que seguem o gatilho em um fluxo de trabalho. Se não estiver familiarizado com os Aplicativos Lógicos, examine [O que são os Aplicativos Lógicos do Azure](../../../logic-apps/logic-apps-overview.md) antes de começar.

> [!NOTE]
> A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa ou [crie uma conta do Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Um recurso ativo dos Serviços de Comunicação do Azure ou [crie um recurso dos Serviços de Comunicação](../create-communication-resource.md).

- Um recurso dos Aplicativos Lógicos(aplicativo lógico), ou [crie um aplicativo lógico em branco, mas com o gatilho que você quer usar](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). No momento, o conector de SMS dos Serviços de Comunicação do Azure oferece somente ações, portanto seu aplicativo lógico requer um gatilho, no mínimo.

  Este guia de início rápido usa o gatilho **Quando um novo email chega**, que está disponível com o [conector do Outlook do Office 365](/connectors/office365/).

- Um número de telefone habilitado para SMS ou [obtenha um número de telefone](./get-phone-number.md).

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

## <a name="add-an-sms-action"></a>Adicionar uma ação de SMS

Para adicionar a ação **Enviar SMS** como uma nova etapa em seu fluxo de trabalho usando o conector de SMS dos Serviços de Comunicação do Azure, siga estas etapas no [portal do Azure](https://portal.azure.com) com o fluxo de trabalho de aplicativo lógico aberto no Designer de Aplicativo Lógico:

1. No designer, na etapa em que você deseja adicionar a nova ação, selecione **Nova etapa**. Como alternativa, para adicionar a nova ação entre as etapas, mova o ponteiro sobre a seta entre essas etapas, selecione o sinal de adição ( **+** ) e selecione **Adicionar uma ação**.

1. Na caixa de pesquisa **Escolha uma operação**, digite `Azure Communication Services`. Na lista de ações, selecione **Enviar SMS**.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Captura de tela que mostra o Designer de Aplicativo Lógico e o conector dos Serviços de Comunicação do Azure com a ação Enviar SMS selecionada.":::

1. Agora, crie uma conexão com o recurso de Serviços de Comunicação.

   1. Forneça um nome para a conexão.

   1. Selecione um recurso dos Serviços de Comunicação do Azure.

   1. Selecione **Criar**.

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="Captura de tela que mostra a configuração da ação Enviar SMS com informações de exemplo.":::

1. Na ação **Enviar SMS**, forneça as seguintes informações: 

   * Os números de telefone de origem e de destino. Para fins de teste, você pode usar o próprio número de telefone como o número de telefone de destino.

   * O conteúdo da mensagem que você deseja enviar, por exemplo, "Olá dos Aplicativos Lógicos!".

   Esta é uma ação **Enviar SMS** com informações de exemplo:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Captura de tela que mostra a ação Enviar SMS com informações de exemplo.":::

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

Agora, execute seu aplicativo lógico para o teste.

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para executar manualmente o aplicativo lógico, selecione **Executar** na barra de ferramentas do designer. Ou você pode aguardar até que seu aplicativo lógico seja disparado. Em ambos os casos, o aplicativo lógico deve enviar uma mensagem SMS para o número de telefone de destino especificado. Para obter mais informações sobre como executar seu aplicativo lógico, examine [como executar seu aplicativo lógico](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app)

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos dos Serviços de Comunicação. Excluir o grupo de recursos também exclui todos os recursos nesse grupo. Para obter mais informações, examine [como limpar os recursos dos Serviços de Comunicação](../create-communication-resource.md#clean-up-resources).

Para limpar o fluxo de trabalho do aplicativo lógico e os recursos relacionados, examine [como limpar os recursos dos Aplicativos Lógicos](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a enviar mensagens SMS usando os Aplicativos Lógicos do Azure e os Serviços de Comunicação do Azure. Para saber mais, prossiga para a assinatura de eventos de SMS:

> [!div class="nextstepaction"]
> [Assinar eventos de SMS](./handle-sms-events.md)

Para obter mais informações sobre o SMS nos Serviços de Comunicação do Azure, confira estes artigos:

- [Conceitos de SMS](../../concepts/telephony-sms/concepts.md)
- [Tipos de número de telefone](../../concepts/telephony-sms/plan-solution.md)
- [SDK do SMS](../../concepts/telephony-sms/sdk-features.md)
