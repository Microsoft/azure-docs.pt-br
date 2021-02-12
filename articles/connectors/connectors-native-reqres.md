---
title: Receber e responder a chamadas usando HTTPS
description: Manipular solicitações HTTPS de entrada de serviços externos usando Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 83ffccb7bae4fabc10796c36e782e72c661bd346
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063005"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Receber e responder a solicitações HTTPS de entrada nos Aplicativos Lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e a ação de resposta e o gatilho de solicitação interna, você pode criar tarefas automatizadas e fluxos de trabalho que podem receber solicitações de entrada por HTTPS. Para enviar solicitações de saída em vez disso, use o [gatilho http interno ou a ação http](../connectors/connectors-native-http.md).

Por exemplo, você seu aplicativo lógico pode:

* Receber e responder a uma solicitação HTTPS para dados em um banco de dado local.

* Disparar um fluxo de trabalho quando ocorrer um evento de webhook externo.

* Receber e responder a uma chamada HTTPS de outro aplicativo lógico.

Este artigo mostra como usar o gatilho de solicitação e a ação de resposta para que seu aplicativo lógico possa receber e responder a chamadas de entrada.

Para obter mais informações sobre segurança, autorização e criptografia para chamadas de entrada para seu aplicativo lógico, como o protocolo [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security), anteriormente conhecido como protocolo SSL (SSL), [Azure Active Directory autenticação aberta (Azure ad OAuth)](../active-directory/develop/index.yml), expondo seu aplicativo lógico com o gerenciamento de API do Azure ou restringindo os endereços IP que originam chamadas de entrada, consulte [acesso seguro e acesso a dados para chamadas de entrada para gatilhos baseados em solicitação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md)?

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Adicionar gatilho de solicitação

Esse gatilho interno cria um ponto de extremidade que pode ser chamado manualmente, capaz de manipular *somente* solicitações de entrada por HTTPS. Quando um chamador envia uma solicitação para esse ponto de extremidade, o [gatilho de solicitação](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) é acionado e executa o aplicativo lógico. Para obter mais informações sobre como chamar esse gatilho, consulte [chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTPS em aplicativos lógicos do Azure](../logic-apps/logic-apps-http-endpoint.md).

Seu aplicativo lógico mantém uma solicitação de entrada aberta somente por um [período limitado](../logic-apps/logic-apps-limits-and-config.md#http-limits). Supondo que seu aplicativo lógico inclua uma [ação de resposta](#add-response), se seu aplicativo lógico não enviar uma resposta de volta ao chamador após esse tempo, seu aplicativo lógico retornará um `504 GATEWAY TIMEOUT` status para o chamador. Se seu aplicativo lógico não incluir uma ação de resposta, seu aplicativo lógico retornará imediatamente um `202 ACCEPTED` status para o chamador.

1. Entre no [portal do Azure](https://portal.azure.com). Criar um aplicativo lógico em branco.

1. Depois de abrir o Designer de Aplicativos Lógicos, na caixa de pesquisa, insira `http request` como o filtro. Na lista de gatilhos, selecione o gatilho **quando uma solicitação HTTP é recebida** .

   ![Selecionar gatilho de solicitação](./media/connectors-native-reqres/select-request-trigger.png)

   O gatilho de solicitação mostra essas propriedades:

   ![Gatilho de solicitação](./media/connectors-native-reqres/request-trigger.png)

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {none} | Sim | A URL do ponto de extremidade gerada depois que você salva o aplicativo lógico e usada para chamar seu aplicativo lógico |
   | **Esquema JSON do corpo da solicitação** | `schema` | Não | O esquema JSON que descreve as propriedades e os valores no corpo da solicitação de entrada |
   |||||

1. Na caixa de **Esquema JSON do Corpo da Solicitação**, opcionalmente, insira um esquema JSON que descreva o corpo na solicitação de entrada, por exemplo:

   ![Exemplo de esquema JSON](./media/connectors-native-reqres/provide-json-schema.png)

   O designer usa esse esquema para gerar tokens para as propriedades na solicitação. Dessa forma, seu aplicativo lógico pode analisar, consumir e passar dados da solicitação por meio do gatilho para seu fluxo de trabalho.

   Exemplo do esquema:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Quando você insere um esquema JSON, o designer mostra um lembrete para incluir o cabeçalho `Content-Type` em sua solicitação e definir esse valor de cabeçalho como `application/json`. Para saber mais, veja [Lidar com tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

   ![Lembrete para incluir o cabeçalho "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Veja a aparência desse cabeçalho no formato JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Para gerar um esquema JSON baseado no payload esperado (dados), você pode usar uma ferramenta como [JSONSchema.net](https://jsonschema.net), ou pode seguir estas etapas:

   1. No gatilho de Solicitação, selecione **Usar o conteúdo de amostra para gerar o esquema**.

      ![Captura de tela com "usar conteúdo de exemplo para gerar esquema" selecionado](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Insira o payload de exemplo e selecione **Concluído**.

      ![Insira o conteúdo de exemplo para gerar o esquema](./media/connectors-native-reqres/enter-payload.png)

      Segue o payload de exemplo:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": {
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Para verificar se a chamada de entrada tem um corpo de solicitação que corresponde ao esquema especificado, siga estas etapas:

   1. Na barra de título do gatilho de solicitação, selecione o botão de reticências (**...**).

   1. Nas configurações do gatilho, ative a **validação de esquema** e selecione **concluído**.

      Se o corpo da solicitação da chamada de entrada não corresponder ao seu esquema, o gatilho retornará um `HTTP 400 Bad Request` erro.

1. Para especificar propriedades adicionais, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros que você deseja adicionar.

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Método** | `method` | Não | O método que as solicitações de entrada devem usar para chamar seu aplicativo lógico |
   | **Caminho relativo** | `relativePath` | Não | O caminho relativo para o parâmetro que a URL do seu ponto de extremidade pode aceitar |
   |||||

   Este exemplo adiciona a propriedade **Método**:

   ![Adicionar parâmetro Método](./media/connectors-native-reqres/add-parameters.png)

   A propriedade **Método** aparece no gatilho para que você possa selecionar um método na lista.

   ![Selecione o método](./media/connectors-native-reqres/select-method.png)

1. Agora, adicione outra ação como a próxima etapa no fluxo de trabalho. No gatilho, selecione **Próxima etapa** para que você possa encontrar a ação que deseja adicionar.

   Por exemplo, você pode responder à solicitação [adicionando uma ação de resposta](#add-response), que pode ser usada para retornar uma resposta personalizada e é descrita posteriormente neste tópico.

   Seu aplicativo lógico mantém a solicitação de entrada aberta por [tempo limitado](../logic-apps/logic-apps-limits-and-config.md#http-limits). Supondo que o fluxo de trabalho do aplicativo lógico inclua uma ação de resposta, se o aplicativo lógico não retornar uma resposta durante esse tempo, seu aplicativo lógico retornará `504 GATEWAY TIMEOUT` ao chamador. Caso contrário, se seu aplicativo lógico não incluir uma ação de resposta, ele retornará imediatamente uma resposta `202 ACCEPTED` ao chamador.

1. Quando terminar, salve o aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

   Esta etapa gera a URL a ser usada para enviar a solicitação que dispara o aplicativo lógico. Para copiar essa URL, selecione o ícone de cópia ao lado da URL.

   ![URL para usar como gatilho do seu aplicativo lógico](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > Se você quiser incluir o hash ou símbolo de libra ( **#** ) no URI ao fazer uma chamada para o gatilho de solicitação, use esta versão codificada em vez disso: `%25%23`

1. Para testar seu aplicativo lógico, envie uma solicitação HTTP para a URL gerada.

   Por exemplo, você pode usar uma ferramenta como o [postmaster](https://www.getpostman.com/) para enviar a solicitação HTTP. Para obter mais informações sobre a definição JSON subjacente do gatilho e como chamar esse gatilho, consulte os tópicos [tipo de gatilho de solicitação](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTPS nos Aplicativos Lógicos do Azure](../logic-apps/logic-apps-http-endpoint.md).

Para obter mais informações sobre segurança, autorização e criptografia para chamadas de entrada para seu aplicativo lógico, como o protocolo [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security), anteriormente conhecido como protocolo SSL (SSL), [Azure Active Directory autenticação aberta (Azure ad OAuth)](../active-directory/develop/index.yml), expondo seu aplicativo lógico com o gerenciamento de API do Azure ou restringindo os endereços IP que originam chamadas de entrada, consulte [acesso seguro e acesso a dados para chamadas de entrada para gatilhos baseados em solicitação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="trigger-outputs"></a>Saídas do gatilho

Veja mais informações sobre as saídas do gatilho de Solicitação:

| Nome da propriedade JSON | Tipo de dados | Descrição |
|--------------------|-----------|-------------|
| `headers` | Objeto | Um objeto JSON que descreve os cabeçalhos da solicitação |
| `body` | Objeto | Um objeto JSON que descreve o conteúdo do corpo da solicitação |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Adicionar uma ação de resposta

Ao usar o gatilho de solicitação para lidar com solicitações de entrada, você pode modelar a resposta e enviar os resultados da carga de volta para o chamador usando a [ação de resposta](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)interna. Você pode usar a ação de resposta *somente* com o gatilho de solicitação. Essa combinação com o gatilho de solicitação e a ação de resposta cria o [padrão de solicitação-resposta](https://en.wikipedia.org/wiki/Request%E2%80%93response). Exceto para dentro de loops ForEach e until e ramificações paralelas, você pode adicionar a ação de resposta em qualquer lugar no fluxo de trabalho.

> [!IMPORTANT]
> Se uma ação de resposta incluir esses cabeçalhos, os Aplicativos Lógicos removerão esses cabeçalhos da mensagem de resposta gerada sem mostrar nenhum aviso ou erro:
>
> * `Allow`
> * `Content-*` cabeçalhos, exceto para `Content-Disposition` , `Content-Encoding` e `Content-Type` quando você usa operações post e put, mas não são incluídos para operações Get
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Embora os Aplicativos Lógicos não impeçam que você salve os aplicativos lógicos que têm uma ação de resposta com esses cabeçalhos, os Aplicativos Lógicos ignoram esses cabeçalhos.

1. No Designer do Aplicativo Lógico, na etapa em que você deseja adicionar uma ação de resposta, selecione **Nova etapa**.

   Por exemplo, usando o gatilho de Solicitação anterior:

   ![Adicionar nova etapa](./media/connectors-native-reqres/add-response.png)

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre essas etapas. Selecione o sinal mais ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, na caixa de pesquisa, insira `response` como seu filtro e selecione a ação de **resposta** .

   ![Selecionar a ação de resposta](./media/connectors-native-reqres/select-response-action.png)

   O gatilho de solicitação é recolhido neste exemplo para simplificar.

1. Adicione quaisquer valores necessários para a mensagem de resposta.

   Em alguns campos, clicar dentro de suas caixas abre a lista de conteúdo dinâmico. Em seguida, você pode selecionar tokens que representam as saídas disponíveis de etapas anteriores no fluxo de trabalho. As propriedades do esquema especificado no exemplo anterior agora aparecem na lista de conteúdo dinâmico.

   Por exemplo, para a caixa **Cabeçalhos**, inclua `Content-Type` como o nome da chave e defina o valor da chave como `application/json`, conforme mencionado anteriormente neste tópico. Para a caixa **Corpo**, você pode selecionar a saída do corpo do gatilho na lista de conteúdo dinâmico.

   ![Detalhes da ação de resposta](./media/connectors-native-reqres/response-details.png)

   Para exibir os cabeçalhos no formato JSON, selecione **Alternar para exibição de texto**.

   ![Cabeçalhos - Mudar para a exibição de texto](./media/connectors-native-reqres/switch-to-text-view.png)

   Para obter mais informações sobre as propriedades que podem ser definidas na ação de resposta.

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Código de status** | `statusCode` | Sim | O código de status a ser retornado na resposta |
   | **Cabeçalhos** | `headers` | Não | Um objeto JSON que descreve um ou mais cabeçalhos a serem incluídos na resposta |
   | **Corpo** | `body` | Não | O corpo da resposta |
   |||||

1. Para especificar propriedades adicionais, como um esquema JSON para o corpo da resposta, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros que você deseja adicionar.

1. Quando terminar, salve o aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

## <a name="next-steps"></a>Próximas etapas

* [Acesso seguro e acesso a dados para chamadas de entrada para gatilhos baseados em solicitação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Conectores para Aplicativos Lógicos](../connectors/apis-list.md)
