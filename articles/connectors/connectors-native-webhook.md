---
title: Aguardar e responder a eventos
description: Automatizar fluxos de trabalho que disparam, pausam e retomam com base em eventos em um ponto de extremidade de serviço usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: 7c6f3c4e3e4a2a29fe6a02c03043e3dfb81a2010
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89227892"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Criar e executar fluxos de trabalho baseados em eventos automatizados usando WebHooks HTTP em aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector interno de webhook http, você pode criar tarefas e fluxos de trabalho automatizados que se inscrevem em um ponto de extremidade de serviço, aguardar eventos específicos e executar com base nesses eventos, em vez de verificar ou *sondar* o ponto de extremidade regularmente.

Aqui estão alguns exemplos de fluxos de trabalho baseados em webhook:

* Aguarde até que um item chegue a partir de um [Hub de eventos do Azure](https://github.com/logicappsio/EventHubAPI) antes de disparar uma execução de aplicativo lógico.
* Aguarde uma aprovação antes de continuar um fluxo de trabalho.

Este artigo mostra como usar o gatilho webhook e a ação de webhook para que seu aplicativo lógico possa receber e responder a eventos em um ponto de extremidade de serviço.

## <a name="how-do-webhooks-work"></a>Como os WebHooks funcionam?

Um gatilho de webhook é baseado em evento, o que não depende da verificação ou sondagem regular de novos itens. Quando você salva um aplicativo lógico que começa com um gatilho de webhook ou quando você altera seu aplicativo lógico de desabilitado para habilitado, o gatilho de webhook *assina* o ponto de extremidade de serviço especificado registrando uma *URL de retorno de chamada* com esse ponto de extremidade. Em seguida, o gatilho aguarda esse ponto de extremidade de serviço chamar a URL, que começa a executar o aplicativo lógico. Semelhante ao [gatilho de solicitação](connectors-native-reqres.md), o aplicativo lógico é acionado imediatamente quando o evento especificado ocorre. O gatilho de webhook *cancela a assinatura* do ponto de extremidade de serviço se você remover o gatilho e salvar seu aplicativo lógico, ou quando você alterar seu aplicativo lógico de habilitado para desabilitado.

Uma ação de webhook também é baseada em evento e *assina* o ponto de extremidade de serviço especificado registrando uma *URL de retorno de chamada* com esse ponto de extremidade. A ação de webhook pausa o fluxo de trabalho do aplicativo lógico e aguarda até que o ponto de extremidade de serviço chame a URL antes que o aplicativo lógico reinicie a execução. A ação de webhook *cancela a assinatura* do ponto de extremidade de serviço nestes casos:

* Quando a ação de webhook for concluída com êxito
* Se a execução do aplicativo lógico for cancelada enquanto aguarda uma resposta
* Antes de o aplicativo lógico atingir o tempo limite

Por exemplo, a ação [**Enviar email de aprovação**](connectors-create-api-office365-outlook.md) do conector do Outlook do Office 365 é um exemplo de ação de webhook que segue esse padrão. Você pode estender esse padrão para qualquer serviço usando a ação de webhook.

Para saber mais, consulte esses tópicos:

* [Webhooks e assinaturas](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Criar APIs personalizadas que dão suporte a um webhook](../logic-apps/logic-apps-create-api-app.md)

Para obter informações sobre criptografia, segurança e autorização para chamadas de entrada para seu aplicativo lógico, como [TLS (segurança de camada de transporte)](https://en.wikipedia.org/wiki/Transport_Layer_Security), anteriormente conhecido como protocolo SSL (SSL) ou [Azure Active Directory autenticação aberta (OAuth do Azure AD)](../active-directory/develop/index.yml), consulte [acesso seguro e acesso a dados para chamadas de entrada para gatilhos baseados em solicitação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A URL de um ponto de extremidade ou API já implantado que dá suporte ao padrão de assinatura e cancelamento de assinante para [gatilhos de webhook em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) ou [ações de webhook em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-actions) , conforme apropriado

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

* O aplicativo lógico em que você deseja aguardar eventos específicos no ponto de extremidade de destino. Para começar com o gatilho de webhook HTTP, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação de webhook HTTP, inicie seu aplicativo lógico com qualquer gatilho desejado. Este exemplo usa o gatilho HTTP como a primeira etapa.

## <a name="add-an-http-webhook-trigger"></a>Adicionar um gatilho de webhook HTTP

Esse gatilho interno chama o ponto de extremidade de assinatura no serviço de destino e registra uma URL de retorno de chamada com o serviço de destino. Em seguida, seu aplicativo lógico aguarda o serviço de destino enviar uma `HTTP POST` solicitação para a URL de retorno de chamada. Quando esse evento acontece, o gatilho é acionado e passa todos os dados na solicitação ao fluxo de trabalho.

1. Entre no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico em branco no Designer de Aplicativos Lógicos.

1. Na caixa de pesquisa do designer, insira `http webhook` como seu filtro. Na lista de **gatilhos** , selecione o gatilho de **webhook http** .

   ![Selecionar gatilho de webhook HTTP](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Este exemplo renomeia o gatilho para para `HTTP Webhook trigger` que a etapa tenha um nome mais descritivo. Além disso, o exemplo posteriormente adiciona uma ação de webhook HTTP e ambos os nomes devem ser exclusivos.

1. Forneça os valores para os [parâmetros de gatilho de webhook http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) que você deseja usar para as chamadas assinar e cancelar assinatura.

   Neste exemplo, o gatilho inclui os métodos, URIs e corpos de mensagens a serem usados ao executar as operações de assinatura e cancelamento de assinatura.

   ![Inserir parâmetros de gatilho de webhook HTTP](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Assinatura-método** | Sim | O método a ser usado ao assinar o ponto de extremidade de destino |
   | **Assinatura-URI** | Sim | A URL a ser usada para assinar o ponto de extremidade de destino |
   | **Assinatura-corpo** | Não | Qualquer corpo de mensagem para incluir na solicitação de assinatura. Este exemplo inclui a URL de retorno de chamada que identifica exclusivamente o Assinante, que é seu aplicativo lógico, usando a `@listCallbackUrl()` expressão para recuperar a URL de retorno de chamada do aplicativo lógico. |
   | **Cancelar assinatura – método** | Não | O método a ser usado ao cancelar a do ponto de extremidade de destino |
   | **Cancelar assinatura-URI** | Não | A URL a ser usada para cancelar a cancelamento do ponto de extremidade de destino |
   | **Cancelar assinatura-corpo** | Não | Um corpo de mensagem opcional para incluir na solicitação de cancelamento de assinatura <p><p>**Observação**: essa propriedade não dá suporte ao uso da `listCallbackUrl()` função. No entanto, o gatilho inclui automaticamente e envia os cabeçalhos `x-ms-client-tracking-id` e `x-ms-workflow-operation-name` , que o serviço de destino pode usar para identificar exclusivamente o Assinante. |
   ||||

1. Para adicionar outras propriedades do disparador, abra a lista **Adicionar novo parâmetro** .

   ![Adicionar mais propriedades do gatilho](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Por exemplo, se você precisar usar a autenticação do, poderá adicionar as propriedades **inscrever-autenticação** e cancelar **assinatura-autenticação** . Para obter mais informações sobre os tipos de autenticação disponíveis para webhook HTTP, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Continue criando o fluxo de trabalho do aplicativo lógico com as ações que são executadas quando o gatilho é acionado.

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

   Salvar o aplicativo lógico chama o ponto de extremidade de assinatura no serviço de destino e registra a URL de retorno de chamada. Em seguida, seu aplicativo lógico aguarda o serviço de destino enviar uma `HTTP POST` solicitação para a URL de retorno de chamada. Quando esse evento acontece, o gatilho é acionado e passa todos os dados na solicitação ao fluxo de trabalho. Se essa operação for concluída com êxito, o gatilho cancelará a assinatura do ponto de extremidade e seu aplicativo lógico continuará o fluxo de trabalho restante.

## <a name="add-an-http-webhook-action"></a>Adicionar uma ação de webhook HTTP

Essa ação interna chama o ponto de extremidade de assinatura no serviço de destino e registra uma URL de retorno de chamada com o serviço de destino. O aplicativo lógico, em seguida, pausa e aguarda o serviço de destino enviar uma `HTTP POST` solicitação para a URL de retorno de chamada. Quando esse evento acontece, a ação passa todos os dados na solicitação ao fluxo de trabalho. Se a operação for concluída com êxito, a ação cancelará a assinatura do ponto de extremidade e seu aplicativo lógico continuará executando o fluxo de trabalho restante.

1. Entre no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico no Logic App Designer.

   Este exemplo usa o gatilho de webhook HTTP como a primeira etapa.

1. Na etapa em que você deseja adicionar a ação de webhook HTTP, selecione **nova etapa**.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa do designer, insira `http webhook` como seu filtro. Na lista **ações** , selecione a ação **webhook http** .

   ![Selecionar ação de webhook HTTP](./media/connectors-native-webhook/select-http-webhook-action.png)

   Este exemplo renomeia a ação como "ação de webhook HTTP" para que a etapa tenha um nome mais descritivo.

1. Forneça os valores para os parâmetros de ação de webhook HTTP, que são semelhantes aos [parâmetros de gatilho de webhook http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)que você deseja usar para as chamadas inscrever e cancelar assinatura.

   Neste exemplo, a ação inclui os métodos, URIs e corpos de mensagens a serem usados ao executar as operações de assinatura e cancelamento de assinatura.

   ![Inserir parâmetros de ação de webhook HTTP](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Assinatura-método** | Sim | O método a ser usado ao assinar o ponto de extremidade de destino |
   | **Assinatura-URI** | Sim | A URL a ser usada para assinar o ponto de extremidade de destino |
   | **Assinatura-corpo** | Não | Qualquer corpo de mensagem para incluir na solicitação de assinatura. Este exemplo inclui a URL de retorno de chamada que identifica exclusivamente o Assinante, que é seu aplicativo lógico, usando a `@listCallbackUrl()` expressão para recuperar a URL de retorno de chamada do aplicativo lógico. |
   | **Cancelar assinatura – método** | Não | O método a ser usado ao cancelar a do ponto de extremidade de destino |
   | **Cancelar assinatura-URI** | Não | A URL a ser usada para cancelar a cancelamento do ponto de extremidade de destino |
   | **Cancelar assinatura-corpo** | Não | Um corpo de mensagem opcional para incluir na solicitação de cancelamento de assinatura <p><p>**Observação**: essa propriedade não dá suporte ao uso da `listCallbackUrl()` função. No entanto, a ação inclui automaticamente e envia os cabeçalhos `x-ms-client-tracking-id` e `x-ms-workflow-operation-name` , que o serviço de destino pode usar para identificar exclusivamente o Assinante. |
   ||||

1. Para adicionar outras propriedades de ação, abra a lista **Adicionar novo parâmetro** .

   ![Adicionar mais propriedades de ação](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Por exemplo, se você precisar usar a autenticação do, poderá adicionar as propriedades **inscrever-autenticação** e cancelar **assinatura-autenticação** . Para obter mais informações sobre os tipos de autenticação disponíveis para webhook HTTP, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

   Agora, quando essa ação é executada, seu aplicativo lógico chama o ponto de extremidade de assinatura no serviço de destino e registra a URL de retorno de chamada. Em seguida, o aplicativo lógico pausa o fluxo de trabalho e aguarda o serviço de destino enviar uma `HTTP POST` solicitação para a URL de retorno de chamada. Quando esse evento acontece, a ação passa todos os dados na solicitação ao fluxo de trabalho. Se a operação for concluída com êxito, a ação cancelará a assinatura do ponto de extremidade e seu aplicativo lógico continuará executando o fluxo de trabalho restante.

## <a name="trigger-and-action-outputs"></a>Saídas de gatilho e ação

Aqui estão mais informações sobre as saídas de um gatilho ou ação de webhook HTTP, que retorna essas informações:

| Nome da propriedade | Type | Descrição |
|---------------|------|-------------|
| headers | objeto | Os cabeçalhos da solicitação |
| body | objeto | Objeto JSON | O objeto com o conteúdo do corpo da solicitação |
| código de status | INT | O código de status da solicitação |
|||

| Código de status | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceita |
| 400 | Solicitação incorreta |
| 401 | Não Autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Ocorreu um erro desconhecido. |
|||

## <a name="connector-reference"></a>Referência de conector

Para obter mais informações sobre parâmetros de ação e gatilho, que são semelhantes entre si, consulte [parâmetros de webhook http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

## <a name="next-steps"></a>Próximas etapas

* [Acesso seguro e acesso a dados para chamadas de entrada para gatilhos baseados em solicitação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Conectores para Aplicativos Lógicos](../connectors/apis-list.md)
