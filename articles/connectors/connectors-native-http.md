---
title: Chamar pontos de extremidade de serviço usando HTTP ou HTTPS
description: Enviar solicitações HTTP ou HTTPS de saída para pontos de extremidade de serviço de aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/14/2020
tags: connectors
ms.openlocfilehash: f2835bda8ac7242b7a3ea4ea63401f26b9c8e426
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062988"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Chamar pontos de extremidade de serviço via HTTP ou HTTPS de Aplicativos Lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o gatilho ou ação http interno, você pode criar tarefas automatizadas e fluxos de trabalho que podem enviar solicitações de saída para pontos de extremidade em outros serviços e sistemas por http ou HTTPS. Para receber e responder a chamadas HTTPS de entrada, use o gatilho de solicitação interno [e a ação de resposta](../connectors/connectors-native-reqres.md).

Por exemplo, você pode monitorar um ponto de extremidade de serviço para seu site verificando esse ponto de extremidade em um agendamento específico. Quando o evento especificado ocorre nesse ponto de extremidade, como seu site ficar inativo, o evento dispara o fluxo de trabalho do aplicativo lógico e executa as ações nesse fluxo de trabalho.

* Para verificar ou *sondar* um ponto de extremidade em um agendamento recorrente, [adicione o gatilho http](#http-trigger) como a primeira etapa no fluxo de trabalho. Cada vez que o gatilho verifica o ponto de extremidade, o gatilho chama ou envia uma *solicitação* para o ponto de extremidade. A resposta do ponto de extremidade determina se o fluxo de trabalho do aplicativo lógico é executado. O gatilho passa qualquer conteúdo da resposta do ponto de extremidade para as ações em seu aplicativo lógico.

* Para chamar um ponto de extremidade de qualquer outro lugar no fluxo de trabalho, [adicione a ação http](#http-action). A resposta do ponto de extremidade determina como as ações restantes do fluxo de trabalho são executadas.

Este artigo mostra como usar o gatilho HTTP e a ação HTTP para que seu aplicativo lógico possa enviar chamadas de saída para outros serviços e sistemas.

Para obter informações sobre criptografia, segurança e autorização para chamadas de saída de seu aplicativo lógico, como [TLS (segurança de camada de transporte)](https://en.wikipedia.org/wiki/Transport_Layer_Security), anteriormente conhecido como SSL (protocolo SSL), certificados autoassinados ou [Azure Active Directory autenticação aberta (Azure ad OAuth)](../active-directory/develop/index.yml), consulte [acesso seguro e acesso a dados para chamadas de saída para outros serviços e sistemas](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A URL para o ponto de extremidade de destino que você deseja chamar

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md)?

* O aplicativo lógico do qual você deseja chamar o ponto de extremidade de destino. Para começar com o gatilho HTTP, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação HTTP, inicie seu aplicativo lógico com qualquer gatilho desejado. Este exemplo usa o gatilho HTTP como a primeira etapa.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Adicionar um gatilho HTTP

Esse gatilho interno faz uma chamada HTTP para a URL especificada para um ponto de extremidade e retorna uma resposta.

1. Entre no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico em branco no Designer de Aplicativos Lógicos.

1. Na caixa de pesquisa do designer, selecione **interno**. Na caixa de pesquisa, insira `http` como o filtro. Na lista de **gatilhos** , selecione o gatilho **http** .

   ![Selecionar o gatilho HTTP](./media/connectors-native-http/select-http-trigger.png)

   Este exemplo renomeia o gatilho para "gatilho HTTP" para que a etapa tenha um nome mais descritivo. Além disso, o exemplo posteriormente adiciona uma ação HTTP e ambos os nomes devem ser exclusivos.

1. Forneça os valores para os [parâmetros de gatilho http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) que você deseja incluir na chamada para o ponto de extremidade de destino. Configure a recorrência para a frequência com que você deseja que o gatilho Verifique o ponto de extremidade de destino.

   ![Insira os parâmetros do gatilho HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Se você selecionar um tipo de autenticação diferente de **nenhum**, as configurações de autenticação diferem com base na sua seleção. Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP, consulte estes tópicos:

   * [Adicionar autenticação a solicitações de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticar o acesso a recursos com identidades gerenciadas](../logic-apps/create-managed-service-identity.md)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados.

1. Continue criando o fluxo de trabalho do aplicativo lógico com as ações que são executadas quando o gatilho é acionado.

1. Quando terminar, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Adicionar uma ação HTTP

Essa ação interna faz uma chamada HTTP para a URL especificada para um ponto de extremidade e retorna uma resposta.

1. Entre no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico no Logic App Designer.

   Este exemplo usa o gatilho HTTP como a primeira etapa.

1. Na etapa em que você deseja adicionar a ação HTTP, selecione **nova etapa**.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira `http` como o filtro. Na lista **ações** , selecione a ação **http** .

   ![Selecionar a ação HTTP](./media/connectors-native-http/select-http-action.png)

   Este exemplo renomeia a ação como "ação HTTP" para que a etapa tenha um nome mais descritivo.

1. Forneça os valores para os [parâmetros de ação http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) que você deseja incluir na chamada para o ponto de extremidade de destino.

   ![Inserir os parâmetros da ação HTTP](./media/connectors-native-http/http-action-parameters.png)

   Se você selecionar um tipo de autenticação diferente de **nenhum**, as configurações de autenticação diferem com base na sua seleção. Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP, consulte estes tópicos:

   * [Adicionar autenticação a solicitações de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticar o acesso a recursos com identidades gerenciadas](../logic-apps/create-managed-service-identity.md)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados.

1. Quando terminar, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

## <a name="trigger-and-action-outputs"></a>Saídas de gatilho e ação

Aqui estão mais informações sobre as saídas de um gatilho ou ação HTTP, que retorna essas informações:

| Propriedade | Tipo | Descrição |
|----------|------|-------------|
| `headers` | Objeto JSON | Os cabeçalhos da solicitação |
| `body` | Objeto JSON | O objeto com o conteúdo do corpo da solicitação |
| `status code` | Integer | O código de status da solicitação |
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

## <a name="content-with-multipartform-data-type"></a>Conteúdo com tipo de dados de várias partes/formulário

Para lidar com o conteúdo que tem `multipart/form-data` o tipo em solicitações HTTP, você pode adicionar um objeto JSON que inclui os `$content-type` `$multipart` atributos e ao corpo da solicitação HTTP usando esse formato.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Por exemplo, suponha que você tenha um aplicativo lógico que envia uma solicitação HTTP POST para um arquivo do Excel para um site usando a API desse site, que dá suporte ao `multipart/form-data` tipo. Veja como essa ação pode parecer:

![Dados de formulário com várias partes](./media/connectors-native-http/http-action-multipart.png)

Aqui está o mesmo exemplo que mostra a definição de JSON da ação HTTP na definição de fluxo de trabalho subjacente:

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="content-with-applicationx-www-form-urlencoded-type"></a>Conteúdo com aplicativo/x-www-form-tipo urlencoded

Para fornecer dados urlencoded no corpo de uma solicitação HTTP, você precisa especificar que os dados têm o `application/x-www-form-urlencoded` tipo de conteúdo. Na ação ou gatilho HTTP, adicione o `content-type` cabeçalho. Defina o valor do cabeçalho como `application/x-www-form-urlencoded` .

Por exemplo, suponha que você tenha um aplicativo lógico que envia uma solicitação HTTP POST para um site, que dá suporte ao `application/x-www-form-urlencoded` tipo. Veja como essa ação pode parecer:

![Captura de tela que mostra uma solicitação HTTP com o cabeçalho ' Content-Type ' definido como ' application/x-www-form-urlencoded '](./media/connectors-native-http/http-action-urlencoded.png)

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>Comportamento assíncrono de solicitação-resposta

Por padrão, todas as ações baseadas em HTTP nos aplicativos lógicos do Azure seguem o [padrão de operação assíncrona](/azure/architecture/patterns/async-request-reply)padrão. Esse padrão especifica que depois que uma ação HTTP chama ou envia uma solicitação para um ponto de extremidade, serviço, sistema ou API, o receptor retorna imediatamente uma resposta ["202 aceito"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) . Esse código confirma que o receptor aceitou a solicitação, mas não concluiu o processamento. A resposta pode incluir um `location` cabeçalho que especifica a URL e uma ID de atualização que o chamador pode usar para sondar ou verificar o status da solicitação assíncrona até que o receptor pare de processar e retorne uma resposta de êxito ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) ou outra resposta diferente de 202. No entanto, o chamador não precisa esperar que a solicitação conclua o processamento e possa continuar executando a próxima ação. Para obter mais informações, consulte [integração assíncrona de microserviço impõe autonomia de microserviço](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* No designer do aplicativo lógico, a ação HTTP, mas não o gatilho, tem uma configuração **padrão assíncrona** , que é habilitada por padrão. Essa configuração especifica que o chamador não aguarda o processamento ser concluído e possa passar para a próxima ação, mas continua verificando o status até que o processamento seja interrompido. Se desabilitada, essa configuração especifica que o chamador aguarda o processamento ser concluído antes de passar para a próxima ação.

  Para localizar essa configuração, siga estas etapas:

  1. Na barra de título da ação HTTP, selecione o botão de reticências (**...**), que abre as configurações da ação.

  1. Localize a configuração **padrão assíncrono** .

     ![Configuração de "padrão assíncrono"](./media/connectors-native-http/asynchronous-pattern-setting.png)

* A definição de JavaScript Object Notation (JSON) subjacente da ação HTTP segue implicitamente o padrão de operação assíncrona.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>Desabilitar operações assíncronas

Às vezes, talvez você queira o comportamento assíncrono da ação HTTP em cenários específicos, por exemplo, quando desejar:

* [Evitar tempos limite de HTTP para tarefas de execução longa](#avoid-http-timeouts)
* [Desabilitar verificação de cabeçalhos de local](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>Desativar a configuração **padrão assíncrona**

1. No designer de aplicativo lógico, na barra de título da ação HTTP, selecione o botão de reticências (**...**), que abre as configurações da ação.

1. Localize a configuração **padrão assíncrona** , desative a configuração para **desativado** , se estiver habilitado, e selecione **concluído**.

   ![Desabilitar a configuração "padrão assíncrono"](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Desabilitar o padrão assíncrono na definição de JSON da ação

Na definição de JSON subjacente da ação HTTP, [adicione a `"DisableAsyncPattern"` opção de operação](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) à definição da ação para que a ação siga o padrão de operação síncrona em vez disso. Para obter mais informações, consulte também [executar ações em um padrão de operação síncrona](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern).

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>Evitar tempos limite de HTTP para tarefas de execução longa

As solicitações HTTP têm um [limite de tempo limite](../logic-apps/logic-apps-limits-and-config.md#http-limits). Se você tiver uma ação HTTP de execução longa que expira devido a esse limite, você terá estas opções:

* [Desabilite o padrão de operação assíncrona da ação http](#disable-asynchronous-operations) para que a ação não seja sondada continuamente ou verifique o status da solicitação. Em vez disso, a ação aguarda que o receptor responda com o status e os resultados depois que a solicitação termina o processamento.

* Substitua a ação HTTP pela [ação de webhook http](../connectors/connectors-native-webhook.md), que aguarda o receptor responder com o status e os resultados depois que a solicitação termina o processamento.

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>Desabilitar verificação de cabeçalhos de local

Alguns pontos de extremidade, serviços, sistemas ou APIs retornam uma resposta "202 aceito" que não tem um `location` cabeçalho. Para evitar que uma ação HTTP Verifique continuamente o status da solicitação quando o `location` cabeçalho não existe, você pode ter estas opções:

* [Desabilite o padrão de operação assíncrona da ação http](#disable-asynchronous-operations) para que a ação não seja sondada continuamente ou verifique o status da solicitação. Em vez disso, a ação aguarda que o receptor responda com o status e os resultados depois que a solicitação termina o processamento.

* Substitua a ação HTTP pela [ação de webhook http](../connectors/connectors-native-webhook.md), que aguarda o receptor responder com o status e os resultados depois que a solicitação termina o processamento.

## <a name="known-issues"></a>Problemas conhecidos

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>Cabeçalhos HTTP omitidos

Se um gatilho ou ação HTTP incluir esses cabeçalhos, os aplicativos lógicos removerão esses cabeçalhos da mensagem de solicitação gerada sem mostrar nenhum aviso ou erro:

* `Accept-*` cabeçalhos, exceto para `Accept-version`
* `Allow`
* `Content-*` cabeçalhos, exceto para `Content-Disposition` , `Content-Encoding` e `Content-Type` quando você usa operações post e put, mas não são incluídos para operações Get
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Embora os aplicativos lógicos não parem de salvar os aplicativos lógicos que usam um gatilho ou ação HTTP com esses cabeçalhos, os aplicativos lógicos ignoram esses cabeçalhos.

## <a name="connector-reference"></a>Referência de conector

Para obter mais informações sobre parâmetros de ação e gatilho, consulte estas seções:

* [Parâmetros de gatilho HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parâmetros de ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

## <a name="next-steps"></a>Próximas etapas

* [Acesso seguro e acesso a dados para chamadas de saída para outros serviços e sistemas](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)
* [Conectores para Aplicativos Lógicos](../connectors/apis-list.md)
