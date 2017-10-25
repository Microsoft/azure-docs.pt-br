---
title: "Descrever APIs e conectores personalizados com Postman – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Criar coleções do Postman para descrever, agrupar e organizar suas APIs e seus conectores personalizados"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>Descrever as APIs personalizadas e conectores personalizados com Postman

Para tornar o desenvolvimento de [APIs personalizadas](../logic-apps/logic-apps-create-api-app.md) e [conectores personalizados](../logic-apps/custom-connector-overview.md) mais rápido e fácil, você pode criar coleções do [Postman](https://www.getpostman.com/), em vez de documentos OpenAPI, para descrever seu APIs e conectores. As coleções do Postman ajudam a organizar e agrupar solicitações de API relacionadas. Por exemplo, você pode usar o Postman ao criar conectores para os Aplicativos Lógicos do Azure, o Microsoft Flow ou o Microsoft PowerApps. 

Esse tutorial mostra como criar uma [coleção do Postman](https://www.getpostman.com/docs/postman/collections/creating_collections) usando a [API Detectar idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) na [Análise de Texto de Serviços Cognitivos do Azure](https://azure.microsoft.com/services/cognitive-services/text-analytics/) como o exemplo. Essa API identifica o idioma, o sentimento e frases-chave no texto que você passa para a API.

## <a name="prerequisites"></a>Pré-requisitos

* Se você for novo no Postman, [instale o aplicativo Postman](https://www.getpostman.com/apps).

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá iniciar com uma [conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, inscreva-se para uma [assinatura de Pagamento conforme o uso](https://azure.microsoft.com/pricing/purchase-options/).

* Se você tiver uma assinatura do Azure, inscreva-se para as APIs de análise de texto concluindo a [Tarefa 1 aqui](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md). 

## <a name="create-a-postman-collection"></a>Criar uma coleção de Postman

Antes de criar uma coleção, crie uma solicitação HTTP para o ponto de extremidade de API. 

### <a name="create-an-http-request-for-your-api"></a>Criar uma solicitação HTTP para sua API

1. Abra o aplicativo Postman para que você possa criar uma [solicitação HTTP](https://www.getpostman.com/docs/postman/sending_api_requests/requests) para seu ponto de extremidade de API. Para saber mais, confira a [Documentação de solicitações](https://www.getpostman.com/docs/postman/sending_api_requests/requests) do Postman.

   1. Na guia **Criador**, selecione o método HTTP, insira a URL de solicitação para o ponto de extremidade de API e selecione um protocolo de autorização, se houver. 
   Quando estiver pronto, escolha **Parâmetros**.

      Para este tutorial, você pode usar essas configurações neste exemplo:

      ![Criar solicitação: "Método HTTP", "URL de solicitação", "Autorização"](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | Parâmetro | Valor sugerido | 
      | --------- | --------------- | 
      | **Método HTTP** | POST | 
      | **URL de Solicitação** | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **Autorização** | "Sem autenticação" | | 
      ||| 

   2. Agora, você pode inserir os pares de chave e valor para usar como parâmetros de consulta ou caminho na URL da solicitação. O Postman combina esses itens em uma cadeia de caracteres de consulta.
   Quando terminar, escolha **Cabeçalhos**.

      ![Continuação de solicitação: "Parâmetros"](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | Parâmetro | Valor sugerido | 
      | --------- | --------------- | 
      | **Parâmetros** | **Chave**: "numberOfLanguagesToDetect" </br>**Valor**: "1" | 
      ||| 

   3. Digite os pares de chave e valor para o cabeçalho de solicitação. 
   Para o nome do cabeçalho, insira qualquer cadeia de caracteres que desejar. Para cabeçalhos HTTP comuns, você pode selecionar na lista suspensa. Quando terminar, escolha **Corpo**. 
   
      ![Continuação de solicitação: "Cabeçalhos"](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | Parâmetro | Valor | 
      | --------- | ----- | 
      | **Cabeçalhos** | **Chave**: "Ocp-Apim-assinatura-chave" </br>**Valor**: *your-API-subscription-key*, que você pode encontrar em sua conta de Serviços Cognitivos <p>**Chave**: "Content-Type" </br> **Valor**: "aplicação/json" | 
      ||| 

   4. Digite o conteúdo que deseja enviar no corpo da solicitação. 
   Para verificar se a solicitação funciona recebendo uma resposta de volta, escolha **Enviar**. 
   
      ![Continuação de solicitação: "Corpo"](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | Parâmetro | Valor sugerido | 
      | --------- | --------------- |    
      | **Corpo** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      O campo de resposta contém a resposta completa da API, incluindo o resultado ou um erro, se houver.

      ![Obter resposta de solicitação](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. Depois que você tiver verificado se a solicitação funciona, salve sua solicitação em uma coleção do Postman. 

   1. Escolha **Salvar**. 
      
      ![Escolha “Salvar”](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. Em **Salvar solicitação**, forneça um **Nome de solicitação** e, opcionalmente, uma **Descrição de solicitação**. 

      > [!NOTE]
      > O conector personalizado usa esses valores posteriormente. Portanto, certifique-se de que forneceu os mesmos valores que você usa mais tarde para resumo e descrição da operação da API personalizada.

   3. Escolha **+ Criar coleção** e forneça um nome de coleção. 
   Escolha a marca de seleção, que cria uma pasta de coleção, em seguida, escolha **Salvar em *your-Postman-collection-name***.

      ![Salvar solicitação](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>Salvar a resposta de solicitação

Depois de salvar sua solicitação, você pode salvar a resposta. Dessa forma, a resposta aparece como um exemplo quando você carrega a solicitação mais tarde.

1. Acima da janela de resposta, escolha **Salvar resposta**. 

   ![Salvar resposta](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   Conectores personalizados dão suporte a apenas uma resposta por solicitação. 
   Se você salvar várias respostas por solicitação, somente a primeira delas será usada.

2. Forneça um nome para o seu exemplo e escolha **Salvar exemplo**.

3. Continue criando sua coleção do Postman com respostas e solicitações adicionais.

### <a name="export-your-postman-collection"></a>Exportar sua coleção do Postman

1. Quando terminar, exporte sua coleção para um arquivo JSON.

   ![Exportar coleção](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. Escolha o formato de exportação **Coleção v1** e navegue até o local em que deseja salvar o arquivo JSON.

   > [!NOTE]
   > No momento, somente o V1 funciona para conectores personalizados.

   ![Escolher o formato de exportação: "Coleção v1"](./media/custom-connector-api-postman-collection/09-export-format.png)
   
Agora, você pode usar essa coleção do Postman para criar conectores e APIs personalizados. Depois de exportar a coleção, você pode importar o arquivo JSON em Aplicativos Lógicos, no Flow ou no PowerApps.

> [!IMPORTANT]
> Se você criar um conector personalizado de uma coleção do Postman, certifique-se de que removeu o cabeçalho `Content-type` de ações e gatilhos. O serviço de destino, por exemplo, o Flow, adiciona automaticamente esse cabeçalho. Além disso, remova os cabeçalhos de autenticação na seção `securityDefintions` de ações e gatilhos.

## <a name="next-steps"></a>Próximas etapas

* [Aplicativos Lógicos: registrar conectores personalizado](../logic-apps/logic-apps-custom-connector-register.md)
* [Fluxo: registrar seu conector](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: registrar seu conector](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [Perguntas frequentes do conector personalizado](../logic-apps/custom-connector-faq.md)
