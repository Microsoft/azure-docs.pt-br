---
title: Autorizar contas de desenvolvedor usando o OAuth 2.0 no Gerenciamento de API do Azure | Microsoft Docs
description: Aprenda a autorizar os usuários usando o OAuth 2.0 no Gerenciamento de API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: b7b003c588d7b079823bb046676a1226828fcae2
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249869"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Como autorizar contas de desenvolvedor usando o OAuth 2.0 no Gerenciamento de API do Azure

Muitas APIs dão suporte ao [OAuth 2.0](https://oauth.net/2/) para proteger a API e garantir que apenas usuários válidos tenham acesso e possam acessar apenas os recursos para os quais eles estão qualificados. Para usar o Console de desenvolvedor interativo do Gerenciamento de API do Azure com essas APIs, o serviço permite que você configure sua instância de serviço para funcionar com sua API habilitada para o OAuth 2.0.

## <a name="prerequisites"> </a>Pré-requisitos

Este guia mostra como configurar sua instância de serviço de Gerenciamento de API para usar a autorização OAuth 2.0 para contas do desenvolvedor, mas não mostra como configurar um provedor OAuth 2.0. A configuração para cada provedor OAuth 2.0 é diferente, embora as etapas sejam semelhantes e as peças de informações necessárias usadas ao configurar o OAuth 2.0 na sua instância de serviço de Gerenciamento de API sejam as mesmas. Este tópico mostra exemplos ao usar o Active Directory do Azure como um provedor OAuth 2.0.

> [!NOTE]
> Para obter mais informações sobre a configuração do OAuth 2.0 usando o Active Directory do Azure, consulte a amostra [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet].

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Configurar um servidor de autorização do OAuth 2.0 no Gerenciamento de API

> [!NOTE]
> Se você ainda não criou uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][Create an API Management service instance].

1. Clique na guia OAuth 2.0 no menu esquerdo e clique em **+Adicionar**.

    ![Menu OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Insira um nome e uma descrição opcional nos campos **Nome** e **Descrição**.

    > [!NOTE]
    > Esses campos são usados para identificar o servidor de autorização OAuth 2.0 dentro da instância do serviço de Gerenciamento de API atual e seus valores não vêm do servidor OAuth 2.0.

3. Digite a **URL da página de registro do cliente**. Essa página é onde os usuários podem criar e gerenciar suas contas e varia de acordo com o provedor OAuth 2.0 usado. A **URL de página de registro do cliente** aponta para a página que os usuários podem usar para criar e configurar suas próprias contas para provedores OAuth 2.0 que dão suporte a gerenciamento de contas por usuários. Algumas organizações não configuram nem usam essa funcionalidade, mesmo se o provedor OAuth 2.0 dá suporte a ele. Se seu provedor OAuth 2.0 não tem o gerenciamento de contas por usuários configurado, insira uma URL de espaço reservado aqui, como a URL da sua empresa, ou uma URL como `https://placeholder.contoso.com`.

    ![Novo servidor OAuth 2.0](./media/api-management-howto-oauth2/oauth-02.png)

4. A seção seguinte da forma contém as configurações dos **Tipos de concessão de autorização**, da **URL do ponto de extremidade de autorização** e do **Método de solicitação de autorização**.

    Especifique os **Tipos de concessão da autorização** ao verificar os tipos desejados. **código de autorização** é especificado por padrão.

    Digite a **URL do ponto de extremidade de autorização**. Por Azure Active Directory, essa URL será semelhante à URL a seguir, em que `<tenant_id>` é substituída pela ID do seu locatário do Azure AD.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    O **método de solicitação de autorização** especifica como a solicitação de autorização é enviada para o servidor OAuth 2.0. O **GET** é selecionado por padrão.

5. Em seguida, a **URL do Ponto de Extremidade do Token**, os **Métodos de autenticação do cliente**, **Método de envio do token de acesso** e **Escopo padrão** precisam ser especificados.

    ![Novo servidor OAuth 2.0](./media/api-management-howto-oauth2/oauth-03.png)

    Para o servidor OAuth 2.0 do Azure Active Directory, a **URL do ponto de extremidade do token** terá o seguinte formato, em que `<TenantID>` tem o formato de `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    A configuração padrão para os **métodos de autenticação do cliente** é **Básica** e o **método de envio do token de acesso** é **Cabeçalho de autorização**. Esses valores são configurados nesta seção da forma, junto com o **Escopo padrão**.

6. A seção **Credenciais do cliente** contém a **ID do cliente** e a **Senha do cliente**, que são obtidas durante o processo de criação e de configuração do seu servidor OAuth 2.0. Uma vez que a **ID do cliente** e a **Senha do cliente** são especificadas, o **redirect_uri** para o **código de autorização** é gerado. Essa URI é usada para configurar a URL de resposta em sua configuração do servidor OAuth 2.0.

    ![Novo servidor OAuth 2.0](./media/api-management-howto-oauth2/oauth-04.png)

    Se os **Tipos de concessão da autorização** são definidos como **Senha do proprietário do recurso**, a seção **Credenciais de senha do proprietário de recurso** é usada para especificar estas credenciais; não sendo o caso, você pode deixá-la em branco.

    Quando o formulário estiver concluído, clique em **Criar** para salvar a configuração do servidor de autorização do OAuth 2.0 do Gerenciamento de API. Quando a configuração do servidor for salva, você pode configurar as APIs para usar esta configuração, conforme mostrado na seção seguinte.

## <a name="step2"> </a>Configurar uma API para usar a autorização do usuário do OAuth 2.0

1. Clique em **APIs** no menu **Gerenciamento de API** esquerdo.

    ![APIs do OAuth 2.0](./media/api-management-howto-oauth2/oauth-05.png)

2. Clique no nome da API desejada e clique em **Configurações**. Role até a seção **Segurança** e, em seguida, e marque a caixa **OAuth 2.0**.

    ![Configurações OAuth 2.0](./media/api-management-howto-oauth2/oauth-06.png)

3. Selecione o **Servidor de autorização** desejado a partir da lista suspensa e clique em **Salvar**.

    ![Configurações OAuth 2.0](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"> </a>Testar a autorização do usuário do OAuth 2.0 no Portal do Desenvolvedor

Depois de configurar seu servidor de autorização OAuth 2.0 e configurar sua API para usar esse servidor, você pode testá-lo indo para o Portal do Desenvolvedor e chamando a API.  Clique no **portal do Desenvolvedor** no menu superior da página de **Visão geral** da instância de Gerenciamento de API do Azure.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e selecione **API de Eco**.

![API de Eco][api-management-apis-echo-api]

> [!NOTE]
> Se você tem apenas uma API configurada ou visível na conta, clicar em APIs levará você diretamente às operações dessa API.

Selecione a operação **Recurso GET**, clique em **Abrir Console** e selecione **Código de autorização** na lista suspensa.

![Abrir console][api-management-open-console]

Quando o **Código de autorização** está selecionado, uma janela pop-up é exibida com a forma de entrada do provedor OAuth 2.0. Neste exemplo, o formulário de inscrição é fornecido pelo Active Directory do Azure.

> [!NOTE]
> Se você tiver pop-ups desativados, será solicitado a habilitá-los pelo navegador. Depois de habilitar, selecione **Código de autorização** novamente e a forma de entrada será exibida.

![Entrar][api-management-oauth2-signin]

Depois de entrar, os **Cabeçalhos de solicitação** serão preenchidos com um cabeçalho `Authorization : Bearer` que autoriza a solicitação.

![Token do cabeçalho da solicitação][api-management-request-header-token]

Nesse momento, você pode configurar os valores desejados para os parâmetros restantes e enviar a solicitação.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar OAuth 2.0 e Gerenciamento de API, consulte o vídeo a seguir e o [artigo](api-management-howto-protect-backend-with-aad.md)que o acompanha.

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

