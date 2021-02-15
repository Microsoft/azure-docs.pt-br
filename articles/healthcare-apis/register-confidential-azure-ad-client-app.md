---
title: Registrar um aplicativo cliente confidencial no Azure AD – API do Azure para FHIR
description: Registrar um aplicativo cliente confidencial no Azure Active Directory que se autentica em nome de um usuário e solicita acesso a aplicativos de recursos.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8021fb3fa9f11ef895569f48a2ae21b3f7adcd36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826213"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrar um aplicativo cliente confidencial no Azure Active Directory

Neste tutorial, você aprenderá a registrar um aplicativo cliente confidencial no Azure Active Directory. 

Um registro de aplicativo cliente é uma representação Azure Active Directory de um aplicativo que pode ser usado para autenticar em nome de um usuário e solicitar acesso a [aplicativos de recursos](register-resource-azure-ad-client-app.md). Um aplicativo cliente confidencial é um aplicativo que pode ser confiável para manter um segredo e apresentar esse segredo ao solicitar tokens de acesso. Exemplos de aplicativos confidenciais são aplicativos do lado do servidor.

Para registrar um novo aplicativo confidencial no portal, siga estas etapas.

## <a name="register-a-new-application"></a>Registrar um novo aplicativo

1. Na [portal do Azure](https://portal.azure.com), navegue até **Azure Active Directory**.

1. Selecione **Registros do Aplicativo**.

    ![portal do Azure. Novo registro de aplicativo.](media/how-to-aad/portal-aad-new-app-registration.png)

1. Selecione **Novo registro**.

1. Dê ao aplicativo um nome de exibição.

1. Forneça uma URL de resposta. Esses detalhes podem ser alterados mais tarde, mas se você souber a URL de resposta do seu aplicativo, insira-o agora.

    ![Novo registro de aplicativo cliente confidencial.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. Selecione **Registrar**.

## <a name="api-permissions"></a>Permissões de API

Agora que você registrou seu aplicativo, precisará selecionar quais permissões de API esse aplicativo deve ser capaz de solicitar em nome dos usuários:

1. Selecione **Permissões de API**.

    ![Cliente confidencial. Permissões da API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. Selecione **Adicionar uma permissão**.

    Se estiver usando a API do Azure para FHIR, você adicionará uma permissão às APIs de assistência médica do Azure pesquisando as **APIs de assistência médica do Azure** em **APIs que minha organização usa**. Você só poderá encontrá-lo se já tiver [implantado a API do Azure para FHIR](fhir-paas-powershell-quickstart.md).

    Se você estiver fazendo referência a um aplicativo de recurso diferente, selecione o [registro de aplicativo do recurso de API do FHIR](register-resource-azure-ad-client-app.md) que você criou anteriormente em **minhas APIs**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Cliente confidencial. Minhas APIs org." lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. Selecione os escopos (permissões) que o aplicativo confidencial deve ser capaz de solicitar em nome de um usuário:

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Cliente confidencial. Minhas APIs org.":::

## <a name="application-secret"></a>Segredo do aplicativo

1. Selecione **Certificados e segredos**.
1. Selecione **Novo segredo do cliente**. 

    ![Cliente confidencial. Segredo do aplicativo](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Forneça uma descrição e a duração do segredo (1 ano, 2 anos ou nunca).

3. Depois de gerado, ele será exibido no portal apenas uma vez. Anote-o e armazene-o com segurança.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como registrar um aplicativo cliente confidencial no Azure Active Directory. Em seguida, você pode acessar o servidor FHIR usando o postmaster
 
>[!div class="nextstepaction"]
>[Acessar a API do Azure para FHIR com o Postman](access-fhir-postman-tutorial.md)
