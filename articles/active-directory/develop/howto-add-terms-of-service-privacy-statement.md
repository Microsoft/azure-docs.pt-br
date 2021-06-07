---
title: Termos de serviço e política de privacidade para aplicativos | Azure
description: Saiba como você pode configurar os termos de declaração de privacidade e de serviço de aplicativo registrado para usar o AD do Azure.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 311dfd976610c392909a0ec3d91fecaa4d733539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104188"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Como configurar os termos de serviço e a política de privacidade para um aplicativo

Os desenvolvedores que criam e gerenciar aplicativos que se integram ao Azure Active Directory (AD do Azure) e contas da Microsoft devem incluir links para os termos do aplicativo de serviço e declarações de privacidade. Os termos de serviço e a declaração de privacidade são revelados aos usuários por meio da experiência de consentimento do usuário. Eles ajudam seus usuários a saberem que podem confiar em seu aplicativo. Os termos da declaração de privacidade e de serviço são especialmente importantes para aplicativos de multilocação voltadas para o usuário – aplicativos que são usados por vários diretórios ou estão disponíveis para qualquer conta da Microsoft.

Você é responsável por criar os termos de serviço e a privacidade documentos de instrução para seu aplicativo e para fornecer as URLs a esses documentos. Para aplicativos multilocatários que falham ao fornecer esses links, a experiência de consentimento do usuário para seu aplicativo mostrará um alerta, o que pode evitar que usuários de consentimento ao seu aplicativo.

> [!NOTE]
> * Aplicativos de único locatário não exibirá um alerta.
> * Se um ou ambos os links estiverem faltando, seu aplicativo mostrará um alerta.

## <a name="user-consent-experience"></a>Experiência de consentimento do usuário

Os exemplos a seguir mostram o usuário experiência de consentimento quando as condições de declaração de privacidade e de serviço são configuradas e quando esses links não estão configurados.

![Capturas de tela com e sem uma declaração de privacidade e termos de serviço fornecido](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Links de formatação para os termos de serviço e a privacidade documentos de instrução

Antes de adicionar links aos termos de serviço e aos documentos da declaração de privacidade do seu aplicativo, verifique se os URLs seguem essas diretrizes.

| Diretriz     | Descrição                           |
|---------------|---------------------------------------|
| Formatar        | URL Válida                             |
| Esquemas válidos | HTTP e HTTPS<br/>Recomendamos HTTPS |
| Comprimento máximo    | 2048 caracteres                       |

Exemplos: `https://myapp.com/terms-of-service` e `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Adicionando links aos termos de serviço e declaração de privacidade

Quando os termos de serviço e a declaração de privacidade estiverem prontos, você poderá adicionar links para esses documentos no seu aplicativo usando um destes métodos:

* [ através do portal do Azure ](#azure-portal)
* [ Usando o objeto de aplicativo JSON ](#app-object-json)
* [Como usar a API do Microsoft Graph](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Usando o portal do Azure
Siga estas etapas no portal do Azure.

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a> e selecione o locatário correto do Azure AD (não B2C).
2. Acesse a seção **Registros de aplicativo** e escolha seu aplicativo.
3. Em **Gerenciar**, selecione **Identidade Visual**.
4. Preencha os campos da **URL de termos de serviço** e **Política de privacidade**.
5. Selecione **Salvar**.

    ![As propriedades do aplicativo contêm URLs de política de privacidade e termos de serviço](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a> Usando o objeto de aplicativo JSON 

Se preferir modificar diretamente o objeto JSON do aplicativo, você poderá usar o editor de manifesto no Portal do Azure ou no Portal de Registro do Aplicativo para incluir links para os termos de serviço e a declaração de privacidade do seu aplicativo.

1. Acesse a seção **Registros de Aplicativo** e escolha seu aplicativo.
2. Abra o painel **Manifesto**.
3. CTRL + F, pesquisa de "informationalUrls". Preencha as informações.
4. Salve suas alterações.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Como usar a API do Microsoft Graph

Para atualizar de modo programático todos os seus aplicativos, use a API do Microsoft Graph para atualizá-los e incluir links para os documentos de política de privacidade e termos de serviço.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Tenha cuidado para não sobrescrever quaisquer valores preexistentes atribuídos a qualquer um desses campos: `supportUrl`, `marketingUrl` e `logoUrl`
> * A API do Microsoft Graph só funciona quando você entra com uma conta do Azure AD. Contas pessoais da Microsoft não são suportadas.
