---
title: Autorizar contas de desenvolvedor usando o OAuth 2.0 no Gerenciamento de API do Azure | Microsoft Docs
description: "Aprenda a autorizar os usuários usando o OAuth 2.0 no Gerenciamento de API."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 78c48247-64f0-4708-b2d0-98b61a821283
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: a19c453bb3271374b587f3d0b35adad55863b490
ms.contentlocale: pt-br
ms.lasthandoff: 07/12/2017

---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Como autorizar contas de desenvolvedor usando o OAuth 2.0 no Gerenciamento de API do Azure
Muitas APIs dão suporte ao [OAuth 2.0](http://oauth.net/2/) para proteger a API e garantir que apenas usuários válidos tenham acesso e possam acessar apenas os recursos para os quais eles estão qualificados. Para usar o Console de desenvolvedor interativo do Gerenciamento de API do Azure com essas APIs, o serviço permite que você configure sua instância de serviço para funcionar com sua API habilitada para o OAuth 2.0.

## <a name="prerequisites"> </a>Pré-requisitos
Este guia mostra como configurar sua instância de serviço de Gerenciamento de API para usar a autorização OAuth 2.0 para contas do desenvolvedor, mas não mostra como configurar um provedor OAuth 2.0. A configuração para cada provedor OAuth 2.0 é diferente, embora as etapas sejam semelhantes e as peças de informações necessárias usadas ao configurar o OAuth 2.0 na sua instância de serviço de Gerenciamento de API sejam as mesmas. Este tópico mostra exemplos ao usar o Active Directory do Azure como um provedor OAuth 2.0.

> [!NOTE]
> Para obter mais informações sobre a configuração do OAuth 2.0 usando o Azure Active Directory, consulte a amostra [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet].
> 
> 

## <a name="step1"> </a>Configurar um servidor de autorização do OAuth 2.0 no Gerenciamento de API
Para começar, clique em **Portal do Editor** no Portal do Azure para acessar o serviço de Gerenciamento de API.

![Portal do editor][api-management-management-console]

> [!NOTE]
> Se ainda não criou uma instância de serviço de Gerenciamento de API, confira [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **Segurança** no menu de **Gerenciamento da API** à esquerda, clique em **OAuth 2.0** e em **Adicionar servidor de autorização**.

![OAuth 2.0][api-management-oauth2]

Após clicar em **Adicionar servidor de autorização**, a nova forma do servidor de autorização é exibida.

![Novo servidor][api-management-oauth2-server-1]

Insira um nome e uma descrição opcional nos campos **Nome** e **Descrição**. 

> [!NOTE]
> Esses campos são usados para identificar o servidor de autorização OAuth 2.0 dentro da instância do serviço de Gerenciamento de API atual e seus valores não vêm do servidor OAuth 2.0.
> 
> 

Digite a **URL da página de registro do cliente**. Essa página é onde os usuários podem criar e gerenciar suas contas e varia de acordo com o provedor OAuth 2.0 usado. A **URL de página de registro do cliente** aponta para a página que os usuários podem usar para criar e configurar suas próprias contas para provedores OAuth 2.0 que dão suporte a gerenciamento de contas por usuários. Algumas organizações não configuram nem usam essa funcionalidade, mesmo se o provedor OAuth 2.0 dá suporte a ele. Se seu provedor OAuth 2.0 não tem o gerenciamento de contas por usuários configurado, insira uma URL de espaço reservado aqui, como a URL da sua empresa, ou uma URL como `https://placeholder.contoso.com`.

A seção seguinte da forma contém as configurações dos **tipos de concessão do código de autorização**, da **URL do ponto de extremidade de autorização** e do **método de solicitação de autorização**.

![Novo servidor][api-management-oauth2-server-2]

Especifique os **tipos de concessão do código de autorização** ao verificar os tipos desejados. **código de autorização** é especificado por padrão.

Digite a **URL do ponto de extremidade de autorização**. Para o Active Directory do Azure, essa URL será semelhante à URL seguinte, em que `<client_id>` é substituído pela ID do cliente que identifica seu aplicativo para o servidor OAuth 2.0.

`https://login.microsoftonline.com/<client_id>/oauth2/authorize`

O **método de solicitação de autorização** especifica como a solicitação de autorização é enviada para o servidor OAuth 2.0. O **GET** é selecionado por padrão.

A seção seguinte está onde a **URL de ponto de extremidade do token**, os **métodos de autenticação do cliente**, **o método de envio de token de acesso** e o **escopo padrão** são especificados.

![Novo servidor][api-management-oauth2-server-3]

Para o servidor OAuth 2.0 do Azure Active Directory, a **URL do ponto de extremidade do token** terá o seguinte formato, em que `<APPID>` tem o formato de `yourapp.onmicrosoft.com`.

`https://login.microsoftonline.com/<APPID>/oauth2/token`

A configuração padrão para os **métodos de autenticação do cliente** é **Básica** e o **método de envio do token de acesso** é **Cabeçalho de autorização**. Esses valores são configurados nesta seção da forma, junto com o **Escopo padrão**.

A seção **Credenciais do cliente** contém a **ID do cliente** e a **Senha do cliente**, que são obtidas durante o processo de criação e de configuração do seu servidor OAuth 2.0. Uma vez que a **ID do cliente** e a **Senha do cliente** são especificadas, o **redirect_uri** para o **código de autorização** é gerado. Essa URI é usada para configurar a URL de resposta em sua configuração do servidor OAuth 2.0.

![Novo servidor][api-management-oauth2-server-4]

Se os **Tipos de concessão do código de autorização** são definidos como **Senha do proprietário do recurso**, a seção **Credenciais de senha do proprietário de recurso** é usada para especificar estas credenciais; não sendo o caso, você pode deixá-la em branco.

![Novo servidor][api-management-oauth2-server-5]

Uma vez que o formulário está concluído, clique em **Salvar** para salvar a configuração do servidor de autorização do OAuth 2.0 de Gerenciamento de API. Quando a configuração do servidor for salva, você pode configurar as APIs para usar esta configuração, conforme mostrado na seção seguinte.

## <a name="step2"> </a>Configurar uma API para usar a autorização do usuário do OAuth 2.0
Clique nas **APIs** no menu **Gerenciamento de API** à esquerda, clique no nome da API desejada, clique na guia **Segurança** e marque a caixa para **OAuth 2.0**.

![Autorização do usuário][api-management-user-authorization]

Selecione o **Servidor de autorização** desejado a partir da lista suspensa e clique em **Salvar**.

![Autorização do usuário][api-management-user-authorization-save]

## <a name="step3"> </a>Testar a autorização do usuário do OAuth 2.0 no Portal do Desenvolvedor
Depois de configurar seu servidor de autorização OAuth 2.0 e configurar sua API para usar esse servidor, você pode testá-lo indo para o Portal do Desenvolvedor e chamando a API.  Clique em **Portal do desenvolvedor** no menu superior direito.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e selecione **API de Eco**.

![API de Eco][api-management-apis-echo-api]

> [!NOTE]
> Se você tem apenas uma API configurada ou visível na conta, clicar em APIs levará você diretamente às operações dessa API.
> 
> 

Selecione a operação **Recurso GET**, clique em **Abrir Console** e selecione **Código de autorização** na lista suspensa.

![Abrir console][api-management-open-console]

Quando o **Código de autorização** está selecionado, uma janela pop-up é exibida com a forma de entrada do provedor OAuth 2.0. Neste exemplo, o formulário de inscrição é fornecido pelo Active Directory do Azure.

> [!NOTE]
> Se você tiver pop-ups desativados, será solicitado a habilitá-los pelo navegador. Depois de habilitar, selecione **Código de autorização** novamente e a forma de entrada será exibida.
> 
> 

![Entrar][api-management-oauth2-signin]

Depois de entrar, os **Cabeçalhos de solicitação** serão preenchidos com um cabeçalho `Authorization : Bearer` que autoriza a solicitação.

![Token do cabeçalho da solicitação][api-management-request-header-token]

Nesse momento, você pode configurar os valores desejados para os parâmetros restantes e enviar a solicitação. 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como usar OAuth 2.0 e Gerenciamento de API, consulte o vídeo a seguir e o [artigo](api-management-howto-protect-backend-with-aad.md)que o acompanha.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps


