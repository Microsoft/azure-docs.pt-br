---
title: Registrar um aplicativo cliente público no Azure AD – API do Azure para FHIR
description: Este artigo explica como registrar um aplicativo cliente público no Azure Active Directory, em preparação para a implantação da API do FHIR no Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: ff7bb6084839af56b5f6e874b39929021f23f8a1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398072"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Registrar um aplicativo cliente público no Azure Active Directory

Neste artigo, você aprenderá a registrar um aplicativo público em Azure Active Directory.  

Os registros de aplicativo cliente são Azure Active Directory representações de aplicativos que podem autenticar e solicitar permissões de API em nome de um usuário. Clientes públicos são aplicativos como aplicativos móveis e aplicativos JavaScript de página única que não podem manter segredos confidenciais. O procedimento é semelhante ao [registro de um cliente confidencial](register-confidential-azure-ad-client-app.md), mas como os clientes públicos não podem ser confiáveis para manter um segredo do aplicativo, não há necessidade de adicionar um.

O guia de início rápido fornece informações gerais sobre como [registrar um aplicativo com a plataforma de identidade da Microsoft](../active-directory/develop/quickstart-register-app.md).

## <a name="app-registrations-in-azure-portal"></a>Registros de aplicativo em portal do Azure

1. No [portal do Azure](https://portal.azure.com), no painel navegação à esquerda, clique em **Azure Active Directory**.

2. Na folha **Azure Active Directory** , clique em **registros de aplicativo** :

    ![portal do Azure. Novo registro de aplicativo.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Clique no **novo registro**.

## <a name="application-registration-overview"></a>Visão geral do registro de aplicativo

1. Dê ao aplicativo um nome de exibição.

2. Forneça uma URL de resposta. A URL de resposta é onde os códigos de autenticação serão retornados ao aplicativo cliente. Você pode adicionar mais URLs de resposta e editar as existentes posteriormente.

    ![portal do Azure. Novo registro de aplicativo público.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)


Para configurar seu aplicativo de [área de trabalho](../active-directory/develop/scenario-desktop-app-registration.md), [móvel](../active-directory/develop/scenario-mobile-app-registration.md) ou [de página única](../active-directory/develop/scenario-spa-app-registration.md) como aplicativo público:

1. Na [portal do Azure](https://portal.azure.com), em **registros de aplicativo** , selecione seu aplicativo e, em seguida, selecione **autenticação**.

2. Selecione **Configurações avançadas**  >  **tipo de cliente padrão**. Para **tratar aplicativo como um cliente público** , selecione **Sim**.

3. Para um aplicativo de página única, selecione **tokens de acesso** e **tokens de ID** para habilitar o fluxo implícito.

   - Se o seu aplicativo conectar usuários, selecione **Tokens de ID**.
   - Se o seu aplicativo também precisar chamar uma API Web protegida, selecione **Tokens de acesso**.

## <a name="api-permissions"></a>Permissões de API

Da mesma forma que o [aplicativo cliente confidencial](register-confidential-azure-ad-client-app.md), você precisará selecionar quais permissões de API esse aplicativo deve ser capaz de solicitar em nome dos usuários:

1. Abra as **permissões de API**.

    Se estiver usando a API do Azure para FHIR, você adicionará uma permissão às APIs de assistência médica do Azure pesquisando as APIs de assistência médica do Azure em **APIs que minha organização usa**. Você só poderá encontrá-lo se já tiver [implantado a API do Azure para FHIR](fhir-paas-powershell-quickstart.md).

    
    Se você estiver fazendo referência a um aplicativo de recurso diferente, selecione o [registro de aplicativo do recurso de API do FHIR](register-resource-azure-ad-client-app.md) que você criou anteriormente em **minhas APIs** :

    ![portal do Azure. Novas permissões de API pública-API do Azure para FHIR padrão](media/public-client-app/api-permissions.png)


2. Selecione as permissões que você gostaria que o aplicativo pudesse solicitar: ![ portal do Azure. Permissões ao aplicativo](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>Validar a autoridade de servidor FHIR
Se o aplicativo que você registrou neste artigo e seu servidor FHIR estiverem no mesmo locatário do Azure AD, você será bom prosseguir para as próximas etapas.

Se você configurar o aplicativo cliente em um locatário do Azure AD diferente do servidor FHIR, será necessário atualizar a **autoridade**. Na API do Azure para FHIR, você define a autoridade em configurações – > autenticação. Defina sua autoridade como **https://login.microsoftonline.com/\<TENANT-ID>** .

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como registrar um aplicativo cliente público no Azure Active Directory. Em seguida, teste o acesso ao seu servidor FHIR usando o postmaster.
 
>[!div class="nextstepaction"]
>[Acessar a API do Azure para FHIR com o Postman](access-fhir-postman-tutorial.md)