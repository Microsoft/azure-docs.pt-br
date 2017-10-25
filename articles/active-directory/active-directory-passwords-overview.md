---
title: "Visão geral da redefinição de senha de autoatendimento do Azure AD | Microsoft Docs"
description: "O que a redefinição de senha de autoatendimento no Azure AD faz para sua organização?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: de2d41d8c2787ffdc80daddf959487f6031da87d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Redefinição de senha de autoatendimento no Azure AD para o profissional de TI

"Autoatendimento" é um jargão usado em vários departamentos de TI em todo o mundo, mas com significados diferentes. O mercado está repleto de produtos que permitem o gerenciamento de grupos locais, senhas ou perfis de usuário na nuvem ou no local.

A SSPR (Redefinição de senha de autoatendimento) do Azure AD (Azure Active Directory) tem a vantagem da facilidade de uso e implantação. A redefinição de senha de autoatendimento do Azure AD combina um conjunto de recursos que:

* Permite aos usuários gerenciar suas próprias senhas
  * De qualquer dispositivo
  * Em qualquer local
  * A qualquer momento
* Manter a conformidade com políticas definidas por você, como administrador

Se você estiver pronto, comece com o Azure AD SSPR usando nosso [guia de início rápido](active-directory-passwords-getting-started.md) e faça com que seus usuários redefinam rapidamente suas próprias senhas.

## <a name="what-is-possible"></a>O que é possível

* **Alteração de senha por autoatendimento** permite que os usuários finais ou os administradores alterem suas senhas sem a assistência de um administrador
* **Desbloqueio de conta por autoatendimento** permite aos usuários finais desbloqueiem suas próprias contas sem assistência do administrador
* **Redefinição de senha por autoatendimento** permite que os usuários finais ou os administradores redefinam suas senhas automaticamente sem a assistência do administrador. A redefinição de senha por autoatendimento exige o Azure AD Premium ou Básico - [Edições do Azure Active Directory](active-directory-editions.md).
* **Redefinição de senha iniciada pelo administrador** permite que um administrador redefina a senha de um usuário final ou de outro administrador de dentro do [Portal do Azure](https://docs.microsoft.com/azure/azure-portal-overview)
* **Relatórios de atividade de gerenciamento de senha** fornecem aos administradores percepções sobre as atividades de registro e de redefinição de senha que ocorrem em suas organizações - [Relatórios de gerenciamento](active-directory-passwords-reporting.md)
* **Write-back de senha** permite o gerenciamento de senhas locais na nuvem, de modo que todos os cenários acima possam ser executados por, ou em nome de, usuários federados ou sincronizados por senha. O Write-back de Senha exige o [Azure AD Premium](active-directory-get-started-premium.md).

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Por que escolher a redefinição da senha por autoatendimento do Azure AD

* **Reduzir os custos**, pois a redefinição de senha com auxílio do suporte ou da assistência técnica representa normalmente 20% dos gastos da organização de TI
* **Aprimorar as experiências do usuário final** e **reduzir o volume da assistência técnica** fornecendo aos usuários finais a capacidade de resolver seus próprios problemas de senha, sem ligar para a assistência técnica ou abrir uma solicitação de suporte.
* **Gerar mobilidade**, pois os usuários podem redefinir suas senhas onde quer que estejam.

## <a name="azure-ad-self-service-password-reset-availability"></a>Disponibilidade da redefinição da senha por autoatendimento do Azure AD

A redefinição de senha por autoatendimento do Azure AD está disponível em três camadas, dependendo de sua assinatura.

* **Azure AD Gratuito**: os administradores somente de nuvem podem redefinir suas próprias senhas.
* **Azure AD Básico** ou qualquer **assinatura paga do O365**: usuários somente de nuvem podem alterar suas próprias senhas e administradores somente de nuvem podem redefinir suas próprias senhas.
* **Azure AD Premium**: qualquer usuário ou administrador, incluindo somente nuvem, federado ou usuários com sincronização de senha, pode redefinir sua própria senha. As senhas locais exigem a habilitação do write-back de senha.

## <a name="azure-ad-self-service-password-reset-a-sum-of-the-parts"></a>Redefinição de senha por autoatendimento do Azure AD, uma soma das partes

A redefinição de senha por autoatendimento no Azure AD é composta pelos seguintes componentes:

* **Portal de configuração de gerenciamento de senhas**, onde você pode controlar as opções de gerenciamento de senhas em seu locatário por meio do Portal do Azure
* **Portal de registro de redefinição de senha**, onde os usuários podem se autorregistrar para a redefinição de senha
* **Portal de redefinição de senha**, onde os usuários podem redefinir suas senhas usando os desafios definidos pelo administrador e as respostas fornecidas pelos usuários
* **Portal de alteração de senha do usuário**, onde os usuários podem alterar suas próprias senhas inserindo a senha antiga e escolhendo uma nova
* **Relatórios de gerenciamento de senha**, onde os administradores podem ver e analisar os relatórios de atividade de senhas de seus locatários no Portal do Azure
* **Write-back de senha no local usando o Azure Connect AD** permite habilitar da nuvem o gerenciamento de usuários locais, federados ou sincronizados por senha

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Preços do Azure AD, SLA, atualizações e roteiro

Encontre mais detalhes sobre esses tópicos nas páginas a seguir

* [**Detalhes de preços do Azure AD**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Preços do Office 365**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Contratos de Nível de Serviço do Azure**](https://azure.microsoft.com/support/legal/sla/)
* [**Contrato de Nível de Serviço do Microsoft Online Services**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Atualizações do Azure**](https://azure.microsoft.com/updates/)
* [**Roteiro do Azure**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Próximas etapas

Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [**Início Rápido**](active-directory-passwords-getting-started.md): comece agora mesmo a usar o gerenciamento de autoatendimento de senhas do Azure AD 
* [**Licenciamento**](active-directory-passwords-licensing.md): configure o licenciamento do Azure AD
* [**Dados**](active-directory-passwords-data.md): entenda os dados que são necessários e como eles são usados para o gerenciamento de senhas
* [**Distribuição**](active-directory-passwords-best-practices.md): planeje e implante o SSPR para seus usuários usando as diretrizes descritas aqui
* [**Personalizar**](active-directory-passwords-customize.md): personalize a aparência da experiência do SSPR em sua empresa.
* [**Relatórios**](active-directory-passwords-reporting.md): descubra se, quando e onde os usuários estão acessando a funcionalidade SSPR
* [**Detalhamento Técnico**](active-directory-passwords-how-it-works.md): veja os bastidores para entender como o recurso funciona
* [**Perguntas frequentes**](active-directory-passwords-faq.md): como? Por quê? O quê? Onde? Quem? Quando? – respostas para perguntas que você sempre quis fazer
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - Saiba como resolver problemas comuns que vemos com a SSPR

