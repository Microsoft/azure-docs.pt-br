---
title: Registrar um aplicativo cliente confidencial no Azure AD – API do Azure para FHIR
description: Registrar um aplicativo cliente confidencial no Azure Active Directory que se autentica em nome de um usuário e solicita acesso a aplicativos de recursos.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284419"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrar um aplicativo cliente confidencial no Azure Active Directory

Neste tutorial, você aprenderá a registrar um aplicativo cliente confidencial no Azure Active Directory (Azure AD).  

Um registro de aplicativo cliente é uma representação do Azure AD de um aplicativo que pode ser usado para autenticar em nome de um usuário e solicitar acesso a [aplicativos de recursos](register-resource-azure-ad-client-app.md). Um aplicativo cliente confidencial é um aplicativo que pode ser confiável para manter um segredo e apresentar esse segredo ao solicitar tokens de acesso. Exemplos de aplicativos confidenciais são aplicativos do lado do servidor. 

Para registrar um novo aplicativo cliente confidencial, consulte as etapas abaixo. 

## <a name="register-a-new-application"></a>Registrar um novo aplicativo

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.

1. Selecione **Registros de aplicativo**. 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="portal do Azure. Novo registro de aplicativo.":::

1. Selecione **Novo registro**.

1. Dê ao aplicativo um nome de exibição voltado para o usuário.

1. Para **tipos de conta com suporte**, selecione quem pode usar o aplicativo ou acessar a API.

1. Adicional Forneça um **URI de redirecionamento**. Esses detalhes podem ser alterados mais tarde, mas se você souber a URL de resposta do seu aplicativo, insira-o agora.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="Novo registro de aplicativo cliente confidencial.":::

1. Selecione **Registrar**.

## <a name="api-permissions"></a>Permissões de API

Agora que você registrou seu aplicativo, deve selecionar quais permissões de API esse aplicativo deve solicitar em nome dos usuários.

1. Selecione **Permissões de API**.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="Cliente confidencial. Permissões de API.":::

1. Selecione **Adicionar uma permissão**.

    Se estiver usando a API do Azure para FHIR, você adicionará uma permissão às APIs de assistência médica do Azure pesquisando a **API de assistência médica do Azure** em **APIs que minha organização usa**. O resultado da pesquisa para a API de assistência médica do Azure retornará somente se você já tiver [implantado a API do Azure para FHIR](fhir-paas-powershell-quickstart.md).

    Se você estiver fazendo referência a um aplicativo de recurso diferente, selecione o [registro de aplicativo do recurso de API do FHIR](register-resource-azure-ad-client-app.md) que você criou anteriormente em **minhas APIs**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Cliente confidencial. Minhas APIs org." lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. Selecione os escopos (permissões) que o aplicativo cliente confidencial pedirá em nome de um usuário. Selecione **user_impersonation** e, em seguida, selecione **Adicionar permissões**.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Cliente confidencial. Permissões delegadas":::


## <a name="application-secret"></a>Segredo do aplicativo

1. Selecione **certificados & segredos** e, em seguida, selecione **novo segredo do cliente**. 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="Cliente confidencial. Segredo do aplicativo.":::

1. Insira uma **Descrição** para o segredo do cliente. Selecione o menu suspenso **expira** para escolher um período de expiração e clique em **Adicionar**.

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="Adicione um segredo do cliente.":::

1. Após a criação da cadeia de caracteres de segredo do cliente, copie seu **valor** e **ID** e armazene-os em um local seguro de sua escolha.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="Cadeia de caracteres secreta do cliente."::: 

> [!NOTE]
>A cadeia de caracteres secreta do cliente é visível apenas uma vez no portal do Azure. Quando você navega para fora da página da Web certificados & segredos e retorna de volta para ele, a cadeia de caracteres de valor se torna mascarada. É importante fazer uma cópia da cadeia de caracteres secreta do cliente imediatamente após sua geração. Se você não tiver uma cópia de backup do segredo do cliente, deverá repetir as etapas acima para gerá-lo novamente.
 
## <a name="next-steps"></a>Próximas etapas

Neste artigo, você foi guiado pelas etapas de como registrar um aplicativo cliente confidencial no Azure AD. Você também foi guiado pelas etapas de como adicionar permissões de API à API de assistência médica do Azure. Por fim, você mostrou como criar um segredo do aplicativo. Além disso, você pode aprender a acessar seu servidor FHIR usando o postmaster.
 
>[!div class="nextstepaction"]
>[Acessar a API do Azure para FHIR com o Postman](access-fhir-postman-tutorial.md)
