---
title: Criar tarefas com base em eventos e fluxos de trabalho em aplicativos lógicos do Azure
description: Disparar, pausar e retomar as tarefas automatizadas, processos e fluxos de trabalho com base em eventos que ocorrem em um ponto de extremidade usando aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541400"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Automatizar tarefas com base em eventos e fluxos de trabalho por meio de webhooks HTTP em aplicativos lógicos do Azure

Com o [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector de HTTP Webhook interno, você pode automatizar fluxos de trabalho aguardem e executadas com base em eventos específicos que ocorrem em um ponto de extremidade HTTP ou HTTPS, criando aplicativos lógicos. Por exemplo, você pode criar um aplicativo lógico que monitora um ponto de extremidade de serviço, aguardando um evento específico antes de disparar o fluxo de trabalho e executar as ações especificadas, em vez de verificar regularmente ou *sondagem* esse ponto de extremidade.

Aqui estão alguns fluxos de trabalho baseado em evento de exemplo:

* Aguarde até que um item chegar de uma [Hub de eventos do Azure](https://github.com/logicappsio/EventHubAPI) antes de disparar um execução do aplicativo lógico.
* Aguarde uma aprovação antes de continuar um fluxo de trabalho.

## <a name="how-do-webhooks-work"></a>Como funcionam os webhooks?

Um gatilho de webhook HTTP é baseado em evento, que não depende de verificação ou regularmente de sondagem para novos itens. Quando você salvar um aplicativo lógico que começa com um gatilho de webhook ou quando você altera seu aplicativo lógico de desabilitado para habilitado, o gatilho de webhook *assina* a um serviço específico ou o ponto de extremidade, registrando um *deURLderetornodechamada* com o serviço ou ponto de extremidade. O gatilho espera, em seguida, para esse serviço ou ponto de extremidade para chamar a URL, que inicia a execução do aplicativo lógico. Semelhante do [gatilho de solicitação](connectors-native-reqres.md), o aplicativo lógico é acionado imediatamente quando o evento especificado ocorrer. O disparador *cancela a assinatura* do ponto de extremidade ou serviço se você remover o gatilho e salva seu aplicativo lógico, ou quando você altera seu aplicativo lógico do habilitado para desabilitado.

Uma ação de webhook HTTP também é baseado em evento e *assina* a um serviço específico ou o ponto de extremidade, registrando um *URL de retorno de chamada* com o serviço ou ponto de extremidade. A ação de webhook pausa o fluxo de trabalho do aplicativo lógico e aguarda até que o serviço ou o ponto de extremidade de URL antes da aplicativo lógico retoma a execução de chamadas. O aplicativo lógico de ação *cancela a assinatura* do serviço ou ponto de extremidade nesses casos:

* Quando a ação de webhook foi concluída com êxito
* Se a execução do aplicativo lógico for cancelado enquanto aguarda uma resposta
* Antes da lógica de aplicativo atinge o tempo limite

Por exemplo, Office 365 Outlook do conector [ **enviar email de aprovação** ](connectors-create-api-office365-outlook.md) ação é um exemplo de ação de webhook que segue este padrão. Você pode estender esse padrão em qualquer serviço usando a ação de webhook.

Para saber mais, consulte esses tópicos:

* [Parâmetros de gatilho de HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooks e assinaturas](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Criar APIs personalizadas que dão suporte a um webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A URL para um ponto de extremidade já implantado ou a API que dá suporte o webhook assinar e cancelar a assinatura padrão para [gatilhos de webhook em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) ou [ações de webhook em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-actions) conforme apropriado

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

* O aplicativo lógico no qual você deseja aguardar eventos específicos no ponto de extremidade de destino. Para iniciar com o gatilho de HTTP Webhook [criar um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação de HTTP Webhook, inicie seu aplicativo lógico com qualquer gatilho que você deseja. Este exemplo usa o gatilho HTTP como a primeira etapa.

## <a name="add-an-http-webhook-trigger"></a>Adicionar um gatilho de HTTP Webhook

Esse gatilho internos registra uma URL de retorno de chamada com o serviço especificado e espera que esse serviço enviar uma solicitação HTTP POST para a URL. Quando esse evento ocorrer, o gatilho é acionado e executa imediatamente o aplicativo lógico.

1. Entre no [Portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico em branco no Designer de aplicativo lógico.

1. No designer, na caixa de pesquisa, digite "webhook http" como filtro. Dos **disparadores** lista, selecione o **HTTP Webhook** gatilho.

   ![Selecione o gatilho HTTP Webhook](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Este exemplo renomeia o gatilho "Gatilho de HTTP Webhook", para que a etapa que tenha um nome mais descritivo. Além disso, mais tarde, o exemplo adiciona uma ação de HTTP Webhook, e ambos os nomes devem ser exclusivos.

1. Forneça os valores para o [parâmetros de gatilho de HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) que você deseja usar para a inscrever-se e cancelar a assinatura de chamadas, por exemplo:

   ![Insira os parâmetros de gatilho de HTTP Webhook](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Para adicionar outros parâmetros disponíveis, abra o **adicionar novo parâmetro** lista e, em seguida, selecione os parâmetros que você deseja.

   Para obter mais informações sobre tipos de autenticação disponíveis para o HTTP Webhook, consulte [autenticar HTTP gatilhos e ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Continue criando o fluxo de trabalho do aplicativo lógico com as ações que são executadas quando o gatilho é acionado.

1. Quando tiver terminado, feito, lembre-se de salvar seu aplicativo lógico. Na barra de ferramentas designer, selecione **salvar**.

   Salvar aplicativo lógico chama o ponto de extremidade de inscrever-se e registra a URL de retorno de chamada para disparar esse aplicativo lógico.

1. Agora, sempre que o serviço de destino envia um `HTTP POST` a solicitação para a URL de retorno de chamada, o aplicativo de lógica é disparado e inclui todos os dados que são passados por meio da solicitação.

## <a name="add-an-http-webhook-action"></a>Adicionar uma ação de HTTP Webhook

Essa ação interna registra uma URL de retorno de chamada com o serviço especificado, faz uma pausa o fluxo de trabalho do aplicativo lógico e aguarda até que o serviço envie uma solicitação HTTP POST para a URL. Quando esse evento ocorrer, a ação retoma a execução do aplicativo lógico.

1. Entre no [Portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico no Logic App Designer.

   Este exemplo usa o gatilho de HTTP Webhook como a primeira etapa.

1. Na etapa onde você deseja adicionar a ação de HTTP Webhook, selecione **nova etapa**.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **adicionar uma ação**.

1. No designer, na caixa de pesquisa, digite "webhook http" como filtro. Dos **ações** lista, selecione o **HTTP Webhook** ação.

   ![Selecione a ação de HTTP Webhook](./media/connectors-native-webhook/select-http-webhook-action.png)

   Este exemplo renomeia a ação a ser "Ação de HTTP Webhook", para que a etapa que tenha um nome mais descritivo.

1. Forneça os valores para o HTTP Webhook parâmetros de ação, que são semelhantes a [parâmetros de gatilho de HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) que você deseja usar para a inscrever-se e cancelar a assinatura de chamadas, por exemplo:

   ![Insira os parâmetros de ação de HTTP Webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Durante o tempo de execução, o aplicativo lógico chama o ponto de extremidade de inscrever-se ao executar esta ação. Seu aplicativo lógico, em seguida, pausa o fluxo de trabalho e aguarda o serviço de destino enviar um `HTTP POST` solicitação para a URL de retorno de chamada. Se a ação for concluída com êxito, a ação de cancelamento de assinatura do ponto de extremidade e seu aplicativo lógico continua executando o fluxo de trabalho.

1. Para adicionar outros parâmetros disponíveis, abra o **adicionar novo parâmetro** lista e, em seguida, selecione os parâmetros que você deseja.

   Para obter mais informações sobre tipos de autenticação disponíveis para o HTTP Webhook, consulte [autenticar HTTP gatilhos e ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Na barra de ferramentas designer, selecione **salvar**.

## <a name="connector-reference"></a>Referência de conector

Para obter mais informações sobre parâmetros de gatilho e ação, que são semelhantes entre si, consulte [parâmetros de HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Detalhes de saída

Aqui está a obter mais informações sobre as saídas de um gatilho de HTTP Webhook ou uma ação, que retorna essas informações:

| Nome da propriedade | Type | DESCRIÇÃO |
|---------------|------|-------------|
| headers | object | Os cabeçalhos da solicitação |
| body | object | Objeto JSON | O objeto com o conteúdo do corpo da solicitação |
| código de status | int | O código de status da solicitação |
|||

| Código de status | DESCRIÇÃO |
|-------------|-------------|
| 200 | OK |
| 202 | Aceita |
| 400 | Solicitação incorreta |
| 401 | Não Autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Ocorreu um erro desconhecido. |
|||

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
