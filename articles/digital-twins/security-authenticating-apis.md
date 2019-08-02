---
title: Entenda a autenticação da API dos Gêmeos Digitais do Azure | Microsoft Docs
description: Use os Gêmeos Digitais do Azure para se conectar e autenticar nas APIs
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyhughes
ms.openlocfilehash: 114edc072524552fab35e9cad6fc85573c4e8d0e
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846543"
---
# <a name="connect-and-authenticate-to-apis"></a>Conecte e autentique as APIs

O Gêmeos Digitais do Azure usa o Active Directory do Azure (Azure AD) para autenticar usuários e proteger aplicativos. O Microsoft Azure Active Directory oferece suporte à autenticação para diversas arquiteturas modernas. Todos eles são baseados nos protocolos padrão da indústria OAuth 2.0 ou OpenID Connect. Além disso, os desenvolvedores podem usar o Microsoft Azure Active Directory para criar aplicativos de locatário único e de linha de negócios (LOB). Os desenvolvedores também podem usar o  Microsoft Azure Active Directory para desenvolver aplicativos multilocatários.

Para obter uma visão geral do Microsoft Azure Active Directory, visite a [página de fundamentos](https://docs.microsoft.com/azure/active-directory/fundamentals/index) para obter guias passo a passo, conceitos e iniciações rápidas.

Para integrar um aplicativo ou serviço ao Azure AD, um desenvolvedor deve primeiro registrar o aplicativo no Azure AD. Para instruções detalhadas e screenshots, veja [este início rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Cinco cenários de aplicativos principais](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) são suportados pelo Microsoft Azure Active Directory:

* Aplicativo de página única (SPA): Um usuário precisa entrar em um aplicativo de página única protegido pelo Azure AD.
* Navegador da Web para aplicativo Web: Um usuário precisa entrar em um aplicativo Web protegido pelo Azure AD.
* Aplicativo nativo para API da Web: Um aplicativo nativo que é executado em um telefone, Tablet ou PC precisa autenticar um usuário para obter recursos de uma API Web protegida pelo Azure AD.
* Aplicativo Web para API da Web: um aplicativo Web precisa obter recursos de uma API Web protegida pelo Azure AD.
* Aplicativo de daemon ou de servidor para API da Web: Um aplicativo daemon ou um aplicativo de servidor sem interface do usuário da Web precisa obter recursos de uma API Web protegida pelo Azure AD.

A biblioteca de autenticação do Microsoft Azure oferece várias maneiras para adquirir tokens do Active Directory. Para detalhes sobre a biblioteca e amostras de código, veja [este artigo](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Chame os Gêmeos Digitais do Azure de uma API da web de camada intermediária

Quando os desenvolvedores arquitetam os Gêmeos Digitais do Azure, eles geralmente criam um aplicativo ou API de camada intermediária. O aplicativo ou API chama o downstream dos Gêmeos Digitais do Azure. Para dar suporte a essa arquitetura da solução da web padrão, certifique-se de que os usuários primeiro:

1. Autentiquem com o aplicativo de camada intermediária

1. Um token em nome do OAuth 2.0 é adquirido durante a autenticação

1. O token adquirido é então usado para autenticar com ou chamar as APIs que são downstream ainda mais usando o fluxo Em nome de

Para obter instruções sobre como orquestrar o fluxo [Em nome de OAuth 2.0 On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Você também pode exibir exemplos de código em [chamar uma API da web downstream](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Próximas etapas

Para configurar e testar os Gêmeos Digitais do Azure usando o fluxo de concessão implícita do OAuth 2.0, leia [Configurar o Postman](./how-to-configure-postman.md).

Para saber mais sobre a segurança dos Gêmeos Digitais do Azure, leia [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).