---
title: Comparar Identidades Externas – Azure Active Directory | Microsoft Docs
description: As Identidades Externas do Azure AD permitem que as pessoas fora da sua organização acessem seus aplicativos e recursos usando sua própria identidade. Compare soluções para Identidades Externas, incluindo colaboração B2B do Azure Active Directory e Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/02/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78fd91e2ba7badb1936b8603c8ed089130b29ffb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644058"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>O que são identidades externas no Azure Active Directory?

Com Identidades Externas no Azure AD, você pode permitir que pessoas fora da sua organização acessem seus aplicativos e recursos, deixando elas entrarem usando qualquer identidade que preferirem. Seus parceiros, distribuidores, fornecedores e outros usuários convidados podem “trazer suas próprias identidades”. Quer eles tenham uma identidade digital corporativa ou emitida pelo governo ou tenham uma identidade social não gerenciada como Google ou Facebook, eles podem usar as próprias credenciais para entrar. O provedor de identidade do usuário externo gerencia a identidade do usuário externo e você gerencia o acesso aos seus aplicativos com o Azure AD para manter seus recursos protegidos.

## <a name="external-identities-scenarios"></a>Cenários de Identidades Externas

As Identidades Externas do Azure AD concentram-se menos na relação de um usuário com sua organização e muito mais no modo como o usuário deseja acessar seus aplicativos e recursos. Nessa estrutura, o Azure AD dá suporte a uma variedade de cenários, desde a colaboração B2B (entre empresas) até o gerenciamento de acesso para aplicativos voltados para o consumidor/cliente ou cidadão (B2C, entre empresa e cliente).

- **Compartilhe seus aplicativos e recursos com usuários externos (colaboração B2B)** . Convide usuários externos em seu locatário como usuários “convidados” aos quais você pode atribuir permissões (para autorização), permitindo que eles usem as credenciais existentes deles (para autenticação). Os usuários entram nos recursos compartilhados usando um processo simples de convite e resgate com a conta corporativa, de estudante ou outra conta de email. Você também pode usar o [gerenciamento de direitos do Azure AD](../governance/entitlement-management-overview.md) para configurar políticas que [gerenciam o acesso de usuários externos](../governance/entitlement-management-external-users.md#how-access-works-for-external-users). E agora, com a disponibilidade dos [fluxos dos usuários de inscrição para autoatendimento](self-service-sign-up-overview.md), você pode permitir que os usuários externos se inscrevam nos aplicativos por conta própria. A experiência pode ser personalizada para permitir a inscrição com uma identidade corporativa, de estudante ou social (como o Google ou Facebook). Você também pode coletar informações sobre o usuário durante o processo de inscrição. Para obter mais informações, consulte a [Documentação do Azure AD B2B](index.yml).

- **Crie percursos do usuário com uma solução de gerenciamento de identidade de rótulo branco para aplicativos voltados para o consumidor e para o cliente (Azure AD B2C)** . Se você for um negócio ou desenvolvedor criando aplicativos voltados para o cliente, poderá fazer o dimensionamento para milhões de consumidores, clientes ou cidadãos usando um Azure AD B2C. Os desenvolvedores podem usar o Azure AD como o sistema CIAM (Gerenciamento de Identidades e Acesso do Cliente) completo para os aplicativos deles. Os clientes podem entrar com uma identidade já estabelecida (como Facebook ou Gmail). Com o Azure AD B2C, você tem controle total para personalizar e controlar como os clientes se inscrevem, entram e gerenciam seus perfis quando usam seus aplicativos. Para obter mais informações, consulte a [Documentação do Azure AD B2C](../../active-directory-b2c/index.yml).

## <a name="compare-external-identities-solutions"></a>Comparar soluções de Identidades Externas

A tabela a seguir fornece uma comparação detalhada dos cenários que você pode habilitar com Identidades Externas do Azure AD.

|   | Colaboração de usuário externo (B2B) | Acesso a aplicativos voltados para o consumidor/cliente (B2C)  |
| ---- | --- | --- |
| **Cenário principal** | Colaboração usando aplicativos da Microsoft (Microsoft 365, Teams etc.) ou os próprios aplicativos (aplicativos SaaS, aplicativos desenvolvidos personalizados etc.).  | Gerenciamento de identidade e acesso para aplicativos SaaS modernos ou desenvolvidos personalizados (não aplicativos próprios da Microsoft).   |
| **Destinado a**    | Colaborar com parceiros de negócios de organizações externas, como fornecedores, parceiros, fornecedores. Os usuários aparecem como usuários convidados em seu diretório. Esses usuários podem ou não ter TI gerenciada.  | Clientes do seu produto. Esses usuários são gerenciados em um diretório do Azure AD separado.  |
| **Provedores de identidade com suporte** | Os usuários externos podem colaborar usando contas corporativas, contas de estudante, qualquer endereço de email, provedores de identidade baseados em SAML e WS-Fed, Gmail e Facebook.  | Os usuários consumidores com contas de aplicativo local (qualquer endereço de email ou nome de usuário), várias identidades sociais com suporte e usuários com identidades corporativas e emitidas pelo governo por meio da federação direta.       |
| **Gerenciamento de usuários externos**   | Usuários externos são gerenciados no mesmo diretório que os funcionários, mas são anotados de normalmente como usuários convidados. Os usuários convidados podem ser gerenciados da mesma forma que os funcionários, ser adicionados aos mesmos grupos e assim por diante.    | Usuários externos são gerenciados no diretório do Azure AD B2C. Eles são gerenciados separadamente do diretório de parceiros e funcionários da organização (se houver).  |
| **SSO (logon único)**      | Há suporte para SSO (logon único) em todos os aplicativos conectados ao Azure AD. Por exemplo, você pode fornecer acesso ao Microsoft 365 ou a aplicativos locais, além de outros aplicativos SaaS, como o Salesforce ou o Workday.    | Há suporte para SSO para aplicativos de clientes dentro dos locatários do B2C do AD do Azure. Não há suporte para o SSO com o Microsoft 365 nem com outros aplicativos SaaS da Microsoft.    |
| **Política de segurança e conformidade**        | Gerenciada pela organização convidada/host (por exemplo, com [políticas de acesso condicional](conditional-access.md)). | Gerenciado pela organização por meio de Acesso Condicional e Proteção de Identidade.        |
| **Identidade visual**  | a marca da organização convidada/host é usada.    | Identidade visual totalmente personalizável por aplicativo ou organização.   |
| **Modelo de cobrança** | [Preço das identidades externas](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) baseado em MAU (usuários ativos mensais). <br>(Confira também: [detalhes de configuração B2B](external-identities-pricing.md)) | [Preço das identidades externas](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) baseado em MAU (usuários ativos mensais). <br>(Confira também: [detalhes de configuração B2C](../../active-directory-b2c/billing.md)) |
| **Mais informações** | [Postagem no blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentação](what-is-b2b.md)                   | [Página do produto](https://azure.microsoft.com/services/active-directory-b2c/), [documentação](../../active-directory-b2c/index.yml)       |

Proteja e gerencie clientes e parceiros além dos limites organizacionais com Identidades Externas do Azure AD.

## <a name="about-multitenant-applications"></a>Sobre aplicativos multilocatários

Se você estiver fornecendo um aplicativo como um serviço e não quiser gerenciar as contas de usuário dos seus clientes, um aplicativo multilocatário provavelmente será a escolha certa para você. Quando você desenvolve aplicativos destinados a outros locatários do Azure AD, você pode direcionar usuários de uma organização (locatário único) ou usuários de qualquer organização que já tenha um locatário do Azure AD (aplicativos multilocatários). Os registros de aplicativo no Azure AD são um locatário único por padrão, mas você pode tornar seu registro multilocatário. Esse aplicativo multilocatário é registrado uma vez por você no próprio Azure AD. Mas qualquer usuário do Azure AD de qualquer organização pode usar o aplicativo sem trabalho adicional de sua parte. Para obter mais informações, confira [Gerenciar a identidade em aplicativos multilocatários](/azure/architecture/multitenant-identity/), [Guia de Instruções](../develop/howto-convert-app-to-be-multi-tenant.md).

## <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Sobre o Azure AD B2C](../../active-directory-b2c/overview.md)