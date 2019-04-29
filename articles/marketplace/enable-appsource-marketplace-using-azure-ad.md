---
title: Habilitar uma listagem do Microsoft AppSource e Azure Marketplace usando o Azure Active Directory | Azure
description: Habilitar o tipo de listagem usando o Microsoft Azure Active Directory no Microsoft Azure Marketplace e no AppSource para editores de serviços e aplicativos.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: 247a45a38d732ace0455c6ca2ebbd5c44c384004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734245"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Habilite uma listagem do AppSource e do Marketplace usando o Active Directory do Azure

 O Active Directory do Azure (Azure AD) é um serviço de identidade na nuvem que permite a autenticação com uma conta da Microsoft. O Azure AD usa estruturas de padrão da indústria. [Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Benefícios do AD do Azure

Os clientes do Microsoft AppSource e do Azure Marketplace usam experiências no produto para pesquisar os catálogos de listagem. Essas ações exigem que os clientes assinem o produto. A integração do Azure AD fornece os seguintes benefícios:

- Engajamento mais rápido e uma experiência otimizada para o cliente
- Logon único (SSO) para milhões de usuários corporativos
- Experiência de login consistente em todos os aplicativos publicados por diferentes parceiros
- Autenticação escalonável entre plataformas para dispositivos móveis e aplicativos de nuvem

## <a name="offers-that-require-azure-ad"></a>Ofertas que exigem o Azure AD

As várias [listando opções e tipos de oferta](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) para AppSource e o Azure Marketplace tem diferentes requisitos de implementação do Azure AD. Consulte a tabela a seguir para obter detalhes:

| **Tipo de oferta**    | **SSO do Azure AD é necessário?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Entre em contato comigo | Avaliação | Test drive | Transação |
| Máquina Virtual | N/D | Não  | Não | Não  |
| Aplicativos Azure (modelo de solução)  | N/D | N/D | N/D | N/D |
| Aplicativos gerenciados  | N/D | N/D | N/D | Não  |
| SaaS  | Não  | sim | sim | Sim |
| Contêineres  | N/D | N/D | N/D | Não  |
| Serviços de Consultoria  | Não  | N/D | N/D | N/D |

Para obter mais informações sobre os requisitos técnicos de SaaS, consulte [Guia de publicação de oferta de aplicativo SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integração com o Azure AD

- Para obter informações sobre como habilitar o logon único integrando o Azure AD à sua listagem, consulte [Azure Active Directory para desenvolvedores]( https://aka.ms/aaddev).
- Para obter detalhes sobre o logon único do Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Habilitar uma listagem de avaliação

A configuração automatizada do cliente pode aumentar a probabilidade de conversão. Quando seu cliente seleciona sua listagem de avaliação e é redirecionado para seu ambiente de avaliação, você pode configurar o cliente diretamente, sem exigir etapas adicionais de login.

Durante a autenticação, o Azure AD envia um token para seu aplicativo ou oferta. As informações do usuário fornecidas pelo token permitem a criação de uma conta de usuário no seu aplicativo ou oferta. Para obter mais informações, consulte [tokens de exemplo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Quando você usa o Azure AD para habilitar a autenticação de um clique no seu aplicativo ou a listagem de avaliação, você:

- Simplificar a experiência do cliente do Marketplace para sua listagem de avaliação.
- Manter a percepção de uma experiência no produto mesmo quando o usuário é redirecionado do Marketplace para o seu domínio ou ambiente de avaliação.
- Reduza a probabilidade de abandono quando os usuários são redirecionados porque não há etapas adicionais de login.
- Reduza as barreiras de implantação para a grande população de usuários do Azure AD.

## <a name="verify-azure-ad-integration"></a>Verifique se a integração do Azure AD

### <a name="multitenant-solutions"></a>Soluções multi locatárias

Use o Azure AD para dar suporte as seguintes ações:

- Registre seu aplicativo em uma das vitrines do Marketplace. Modo de exibição [registro de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) ou [certificação do AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) para obter mais informações.
- Habilite o recurso de suporte multilocatário no Microsoft Azure Active Directory para obter uma experiência de avaliação com um clique.

Se você for novo no usando o logon único federado do Azure AD, siga estas etapas:

1. Registre seu aplicativo no Marketplace.
1. Desenvolver SSO com o Azure AD por meio [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) ou [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Habilite o recurso de suporte multilocação no Azure AD para fornecer uma experiência de avaliação com um clique.

### <a name="single-tenant-solutions"></a>Soluções de inquilino único

Use o Azure AD para oferecer suporte a uma das seguintes ações:

- Adicionar usuários convidados ao seu diretório por meio do [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Configurar manualmente avaliações para clientes usando o **entre em contato comigo** opção de publicação.
- Desenvolva um test drive por cliente.
- Crie um aplicativo de demonstração de exemplo multilocatário que usa SSO.

## <a name="next-steps"></a>Próximas etapas

- Certifique-se de que você [registrado no Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Entre no [Portal do Cloud Partner](https://cloudpartner.azure.com/) para criar ou concluir sua oferta.
