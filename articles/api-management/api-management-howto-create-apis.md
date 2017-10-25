---
title: Como criar APIs no Gerenciamento de API do Azure
description: Saiba como criar e configurar APIs no Gerenciamento de API do Azure.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 14c20da4-f29f-4b28-bec7-3d4c50b734da
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: ab08256fbc3caca05bf23a12016ad2acf4fc7412
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-apis-in-azure-api-management"></a>Como criar APIs no Gerenciamento de API do Azure
Uma API no Gerenciamento de API representa um conjunto de operações que pode ser invocado pelas aplicações clientes. Novas APIs são criadas no Portal do editor e, depois, as operações desejadas são adicionadas. Após as operações serem adicionadas, a API é adicionada a um produto e pode ser publicada. Após uma API ser publicada, ela pode ser usada e assinada por desenvolvedores.

Este guia mostra a primeira etapa do processo: como criar e configurar uma nova API no Gerenciamento de API. Para obter mais informações sobre como adicionar operações e publicar um produto, consulte [Como adicionar operações a uma API][How to add operations to an API] e [Como criar e publicar um produto][How to create and publish a product].

## <a name="create-new-api"> </a>Criar uma nova API
APIs são criadas e configuradas no Portal do editor. Para acessar o Portal do editor, clique em **Portal do editor** no Portal do Azure para acessar o serviço de Gerenciamento de API.

![Portal do editor][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gerenciamento de API, confira [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **Adicionar API**.

![Criar API][api-management-create-api]

Use a janela **Adicionar nova API** para configurar a nova API.

![Adicionar nova API][api-management-add-new-api]

Os campos a seguir são usados para configurar a nova API.

* **Nome da API Web** fornece um nome descritivo exclusivo para a API. Esse nome será exibido nos portais do desenvolvedor e do editor.
* **URL de Serviço Web** utiliza como referência o serviço HTTP que está implementando a API. O gerenciamento de API envia as solicitações para esse endereço.
* **O sufixo da URL da API Web** está anexado à URL base do serviço de gerenciamento de API. A URL base é comum para todas as APIs hospedadas por uma instância de um serviço de Gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API para um editor específico.
* **Esquema de URL da API da Web** determina quais protocolos podem ser usados para acessar a API. A HTTPs é especificada por padrão.
* Para adicionar, opcionalmente, essa nova API a um produto, clique na lista suspensa **Produtos (opcional)** e escolha um produto. Esta etapa pode ser repetida várias vezes para adicionar a API a vários produtos.

Após configurar os valores desejados, clique em **Salvar**. Após criar a novas API, a página de resumo das APIs será exibida no Portal do Publicador.

![Resumo da API][api-management-api-summary]

## <a name="configure-api-settings"> </a>Definir configurações de API
Você pode usar a guia **Configurações** para verificar e editar a configuração de uma API. O **Nome da API Web**, a **URL do Serviço Web** e o **sufixo da URL da API Web** são definidos inicialmente, quando a API é criada, e podem ser modificados aqui. **Descrição** fornece uma descrição opcional, e o **Esquema de URL da API Web** determina quais protocolos podem ser usados para acessar a API.

![Configurações da API][api-management-api-settings]

Para configurar a autenticação de gateway para o serviço de back-end implementando a API, selecione a guia **Segurança** . A lista suspensa **Com credenciais** pode ser usada para configurar a autenticação como **HTTP básica** ou **Certificados do cliente**. Para usar a autenticação HTTP básica, basta inserir as credenciais desejadas. Para informações sobre como usar a autenticação de certificados do cliente, consulte [Como garantir serviços de back-end usando autenticação de certificados do cliente no Gerenciamento de API do Azure][How to secure back-end services using client certificate authentication in Azure API Management].

A guia **Seguran**ça também pode ser usada para configurar **Autorização do usuário** usando OAuth 2.0. Para obter mais informações, consulte [Como autorizar contas de desenvolvedor usando o OAuth 2.0 no Gerenciamento de API do Azure][How to authorize developer accounts using OAuth 2.0 in Azure API Management].

![Configurações da autenticação Básica][api-management-api-settings-credentials]

Clique em **Salvar** para salvar quaisquer mudanças feitas nas configurações da API.

## <a name="next-steps"> </a>Próximas etapas
Após criar uma API e definir as configurações, as próximas etapas são adicionar as operações à API, adicionar a API a um produto e publicá-la para que fique disponível para desenvolvedores. Para obter mais informações, consulte os seguintes artigos.

* [Como adicionar operações a uma API][How to add operations to an API]
* [Como criar e publicar um produto][How to create and publish a product]

[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[How to secure back-end services using client certificate authentication in Azure API Management]: api-management-howto-mutual-certificates.md
[How to authorize developer accounts using OAuth 2.0 in Azure API Management]: api-management-howto-oauth2.md
