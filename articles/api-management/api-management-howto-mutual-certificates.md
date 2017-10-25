---
title: "Proteger serviços de back-end usando autenticação de certificado do cliente - Gerenciamento de API do Azure | Microsoft Docs"
description: "Saiba como garantir serviços de back-end usando autenticação de certificado do cliente no Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 43453331-39b2-4672-80b8-0a87e4fde3c6
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 464e5e2b2d9678bef002497588eac60db074f9bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Como garantir serviços de back-end usando autenticação de certificado do cliente no Gerenciamento de API do Azure
O Gerenciamento de API fornece a capacidade para garantir acesso ao serviço back-end de uma API usando certificados do cliente. Este guia mostra como gerenciar certificados no Portal do editor de API e como configurar uma API para usar um certificado para acessar seu serviço back-end.

Para obter mais informações sobre gerenciamento de certificados usando a API REST de gerenciamento de API, consulte [Entidade de certificado da API REST de Gerenciamento de API do Azure][Azure API Management REST API Certificate entity].

## <a name="prerequisites"> </a>Pré-requisitos
Este guia mostra como configurar sua instância de serviço de Gerenciamento de API para usar a autenticação de certificado do cliente para acessar o serviço back-end para uma API. Antes de seguir as etapas descritas neste tópico, é necessário configurar seu serviço back-end para a autenticação de certificado do cliente ([para configurar a autenticação de certificado nos Sites do Azure, consulte este artigo][to configure certificate authentication in Azure WebSites refer to this article]) e ter acesso ao certificado e à senha do certificado para carregar no portal do editor do Gerenciamento de API.

## <a name="step1"> </a>Carregar um certificado do cliente
Para começar, clique em **Portal do Editor** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal do editor de Gerenciamento de API.

![Portal do editor de API][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gerenciamento de API, confira [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **Segurança** no menu **Gerenciamento de API** à esquerda e clique em **Certificados do cliente**.

![Certificados do cliente][api-management-security-client-certificates]

Para carregar um novo certificado, clique em **Carregar um certificado**.

![Carregar um certificado][api-management-upload-certificate]

Navegue para o seu certificado e digite a senha para o certificado.

> O certificado deve estar no formato **.pfx** . Os certificados autoassinados são permitidos.
> 
> 

![Carregar um certificado][api-management-upload-certificate-form]

Clique em **Carregar** para carregar o certificado.

> A senha do certificado é validada neste momento. Se ela estiver incorreta, é exibida uma mensagem de erro.
> 
> 

![Certificado carregado][api-management-certificate-uploaded]

Uma vez que o certificado é carregado, ele aparece na guia **Certificados do cliente** . Se você tiver vários certificados, anote a entidade ou os últimos quatro caracteres da impressão digital, que serão usados para selecionar o certificado ao configurar uma API para usar certificados, como abordado na seção [Configurar uma API para usar um certificado do cliente para a autenticação de gateway][Configure an API to use a client certificate for gateway authentication] a seguir.

> Para desabilitar a validação da cadeia de certificados ao usar, por exemplo, um certificado autoassinado, siga as etapas descritas neste [item](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end) das Perguntas frequentes.
> 
> 

## <a name="step1a"> </a>Excluir um certificado do cliente
Para excluir um certificado, clique em **Excluir** ao lado do certificado desejado.

![Excluir Certificado][api-management-certificate-delete]

Clique em **Sim, excluir** para confirmar.

![Confirmar exclusão][api-management-confirm-delete]

Se o certificado está em uso por uma API, então uma tela de aviso é exibida. Para excluir o certificado, você primeiro deve remover o certificado de quaisquer APIs que são configuradas para usá-la.

![Confirmar exclusão][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurar uma API para usar um certificado do cliente para a autenticação de gateway
Clique nas **APIs** no menu **Gerenciamento de API** à esquerda, clique no nome da API desejada e clique na guia **Segurança**.

![Segurança da API][api-management-api-security]

Selecione **Certificados do cliente** na lista suspensa **Com credenciais**.

![Certificados do cliente][api-management-mutual-certificates]

Selecione o certificado desejado da lista suspensa **Certificado do cliente** . Se houver certificados múltiplos, você pode olhar no assunto ou nos últimos quatro caracteres da impressão digital como observado na seção anterior para determinar o certificado correto.

![Selecionar certificado][api-management-select-certificate]

Clique em **Salvar** para salvar as alterações de configuração para a API.

> Essa alteração tem efeito imediato e chama para operações desta API, que usará o certificado para autenticar no servidor back-end.
> 
> 

![Salvar alterações da API][api-management-save-api]

> Quando um certificado é especificado para a autenticação de gateway para o serviço back-end de uma API, ele se torna parte da política dessa API e pode ser exibido no editor da política.
> 
> 

![Política do certificado][api-management-certificate-policy]

## <a name="self-signed-certificates"></a>Certificados autoassinados

Se você estiver usando certificados autoassinados, precisará desabilitar a validação da cadeia de certificados para que o Gerenciamento de API comunique-se com o sistema de back-end, caso contrário, ele retornará um código de erro 500. Para configurar isso, você pode usar cmdlets [`New-AzureRmApiManagementBackend`](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (para novo back-end) ou [`Set-AzureRmApiManagementBackend`](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (para back-end existente) do PowerShell e definir o parâmetro `-SkipCertificateChainValidation` como `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre outras maneiras de proteger seu serviço de back-end, como HTTP básica ou autenticação secreta compartilhada, consulte o vídeo a seguir.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Last-mile-Security/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



