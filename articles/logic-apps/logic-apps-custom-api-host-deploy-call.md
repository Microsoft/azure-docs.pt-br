---
title: Chamar APIs Web personalizadas e APIs REST
description: Chamar suas próprias APIs Web e APIs REST dos Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83659778"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Implantar e chamar APIs personalizadas de fluxos de trabalho nos Aplicativos Lógicos do Azure

Depois de [criar suas próprias APIs](./logic-apps-create-api-app.md) para usar em seus fluxos de trabalho de aplicativo lógico, você precisará implantar essas APIs antes de poder chamá-las. Você pode implantar suas APIs como [aplicativos Web](../app-service/overview.md), mas considere implantar suas APIs como [aplicativos de API](../app-service/app-service-web-tutorial-rest-api.md), o que facilita o trabalho quando você criar, hospedar e consumir APIs locais e na nuvem. Você não precisa alterar o código em suas APIs; basta implantar seu código para um aplicativo de API. Você pode hospedar suas APIs no [Serviço de Aplicativo do Azure](../app-service/overview.md), uma oferta de PaaS (plataforma como serviço) que fornece uma hospedagem de API fácil e altamente escalonável.

Embora você possa chamar qualquer API de um aplicativo lógico, para a melhor experiência, adicione [metadados do Swagger](https://swagger.io/specification/) que descrevem as operações e os parâmetros de sua API. Este documento do Swagger ajuda sua API a se integrar mais facilmente e trabalhar melhor com aplicativos lógicos.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Implantar sua API como um aplicativo Web ou aplicativo de API

Antes de chamar sua API personalizada de um aplicativo lógico, implante sua API como um aplicativo Web ou aplicativo de API do Serviço de Aplicativo do Azure. Para tornar o documento do Swagger legível pelo Designer de Aplicativos Lógicos, configure as propriedades de definição da API e ative o [CORS (compartilhamento de recursos entre origens)](../app-service/overview.md) para seu aplicativo Web ou de API.

1. No [portal do Azure](https://portal.azure.com), selecione seu aplicativo Web ou aplicativo de API.

2. No menu de aplicativos que é aberto, em **API**, escolha **Definição da API**. Defina o **Localização da definição de API** como a URL de seu arquivo swagger.json.

   Normalmente, a URL é exibida neste formato: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Link para o documento Swagger da sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Em **API**, escolha **CORS**. Defina a política de CORS de **Origens permitidas** como **'*'** (permitir todos).

   Essa configuração permite solicitações do Designer de Aplicativo Lógico.

   ![Permitir solicitações do Designer de Aplicativo Lógico para sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Para obter mais informações, consulte [Hospedar uma API RESTful com CORS no Serviço de Aplicativo do Azure](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Chamar sua API personalizada de fluxos de trabalho de aplicativo lógico

Depois de configurar as propriedades de definição de API e os CORS, os gatilhos e ações da sua API personalizada devem ficar disponíveis para você incluir em seu fluxo de trabalho de aplicativo lógico. 

*  Para exibir sites que têm URLs do OpenAPI, você pode navegar pelos seus sites de assinatura no Designer de Aplicativos Lógicos.

*  Para exibir as ações e entradas disponíveis apontando para um documento do Swagger, use a [ação HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Para chamar qualquer API, incluindo APIs que não têm ou expõem um documento do Swagger, você sempre pode criar uma solicitação com a [ação HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do conector personalizado](../logic-apps/custom-connector-overview.md)
