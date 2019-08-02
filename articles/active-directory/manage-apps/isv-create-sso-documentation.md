---
title: Criar e publicar a documentação de logon única para o seu aplicativo
description: Diretrizes para fornecedores independentes de software sobre como integrar com o Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53221343ac606b6076cc9cc3cff6e0f96c1a3ac3
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659587"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Criar e publicar a documentação de logon única para o seu aplicativo   

## <a name="documentation-on-your-site"></a>Documentação em seu site

Facilidade de adoção é um fator importante nas decisões de software empresarial. Documentação de limpar fácil de seguir dá suporte a seus clientes em sua jornada de adoção e reduz os custos de suporte. Trabalhando com milhares de fornecedores de software, a Microsoft tem visto o que funciona.

É recomendável que a documentação do seu site no mínimo incluem os itens a seguir.

* Introdução à sua funcionalidade SSO

  * Protocolos com suporte

  * SKU e versão

  * Lista de provedores de identidade com suporte com links de documentação

* Informações de licenciamento para o seu aplicativo

* Controle de acesso baseado em função para configurar o SSO

* Etapas de configuração de SSO

  * Elementos de configuração de interface do usuário para SAML com valores esperados do provedor

  * Informações do provedor de serviço a serem passados para provedores de identidade

* Se o OIDC/OAuth

  * Lista de permissões necessárias para autorização com justificativas de negócios

* Etapas de testes para os usuários do piloto

* Informações de solução de problemas, incluindo mensagens e códigos de erro

* Suporte a mecanismos para os clientes

## <a name="documentation-on-the-microsoft-site"></a>Documentação no Site da Microsoft

Ao listar seu aplicativo com a Galeria de aplicativo do Azure Active Directory, que também publica seu aplicativo no Azure Marketplace, a Microsoft irá gerar a documentação para nossos clientes mútuos explicando o processo passo a passo. Você pode ver um exemplo [aqui](https://aka.ms/appstutorial). Esta documentação é criada com base em seu envio para a Galeria e você pode atualizá-lo facilmente se você fizer alterações em seu aplicativo usando sua conta do GitHub.

## <a name="next-steps"></a>Próximas etapas

[Listar seu aplicativo na Galeria de aplicativo do Azure AD](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)