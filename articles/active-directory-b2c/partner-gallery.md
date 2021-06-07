---
title: Galeria de parceiros de ISV para Azure AD B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar-se com nossos parceiros ISV para adaptar sua experiência do usuário final às suas necessidades. Nossa rede de parceiros estende nossos recursos de solução; Habilitar MFA, autenticação de cliente segura, controle de acesso baseado em função; combater fraude por meio de prova de verificação de identidade.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e0125f14570e917b228c5f713e636335755581d
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024261"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Parceiros ISV Azure Active Directory B2C

Nossa rede de parceiros de ISV estende nossos recursos de solução para ajudá-lo a criar experiências de usuário final diretas. Com o Azure AD B2C, você pode integrar com parceiros ISV para habilitar os métodos de autenticação multifator (MFA), realizar o controle de acesso baseado em função, habilitar a verificação de identidade e a prova, melhorar a segurança com detecção de bot e proteção contra fraudes e atender aos requisitos de SCA (autenticação segura do cliente) da diretiva de serviços de pagamento 2 (PSD2). Use nossas instruções detalhadas de exemplo para aprender a integrar aplicativos com os parceiros ISV.

>[!NOTE]
>O [site da comunidade Azure Active Directory B2C no GitHub](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) também fornece políticas personalizadas de exemplo da Comunidade.

## <a name="identity-verification-and-proofing"></a>Verificação de identidade e prova de ortografia

A Microsoft faz parceria com os seguintes ISVs para verificação de identidade e prova.

| Parceiro ISV | Orientações de descrição e integração |
|:-------------------------|:--------------|
|![Captura de tela de um logotipo do Experian.](./media/partner-gallery/experian-logo.png) | O [Experian](./partner-experian.md) é um provedor de verificação de identidade e de provação que executa avaliações de risco com base em atributos de usuário para evitar fraudes. |
|![Captura de tela de um logotipo do IDology.](./media/partner-gallery/idology-logo.png) | O [IDology](./partner-idology.md) é um provedor de verificação de identidade e de provação com soluções de verificação de ID, soluções de prevenção de fraudes, soluções de conformidade e outras.|
|![Captura de tela de um logotipo do Jumio.](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) é um serviço de verificação de ID, que permite a verificação de ID automatizada em tempo real, protegendo os dados do cliente. |
| ![Captura de tela de um logotipo do LexisNexis.](./media/partner-gallery/lexisnexis-logo.png) | O [LexisNexis](./partner-lexisnexis.md) é um provedor de validação de identidade e criação de perfil que verifica a identificação do usuário e fornece uma avaliação de risco abrangente com base no dispositivo do usuário. |
| ![Captura de tela de um logotipo do Onfido](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) é uma ID de documento e uma solução de verificação de biometria facial que permite que as empresas atendam aos requisitos de *clientes* e de identidade em tempo real.  |

## <a name="mfa-and-passwordless-authentication"></a>MFA e autenticação com senha

A Microsoft faz parceria com os seguintes ISVs para MFA e autenticação sem senha.

| Parceiro ISV | Orientações de descrição e integração |
|:-------------------------|:--------------|
| ![Captura de tela de um logotipo do hypr](./media/partner-gallery/hypr-logo.png) | O [Hypr](./partner-hypr.md) é um provedor de autenticação sem senha, que substitui senhas com criptografias de chave pública eliminando fraude, phishing e reutilização de credenciais. |
| ![Captura de tela de um logotipo do ITSM](./media/partner-gallery/itsme-logo.png) | o [ITSM](./partner-itsme.md) é uma solução de identificação digital em conformidade com os serviços de autenticação e confiança (eiDAS) para permitir que os usuários se conectem com segurança sem leitores de cartão, senhas, autenticação de dois fatores e vários códigos de PIN. |
|![Captura de tela de um logotipo sem Key.](./media/partner-gallery/keyless-logo.png) | Keyless é um provedor de autenticação [sem](./partner-keyless.md) senha que fornece autenticação na forma de uma varredura biométrica facial e elimina a fraude, phishing e reutilização de credenciais.
| ![Captura de tela de um logotipo do Nevis](./media/partner-gallery/nevis-logo.png) | O [Nevis](./partner-nevis.md) habilita a autenticação sem senha e fornece uma experiência de usuário final totalmente marcada e móvel com identidade visual com o aplicativo de acesso de Névis para autenticação de cliente forte e para atender aos requisitos de transação PSD2. |
| ![Captura de tela de um logotipo do trusona](./media/partner-gallery/trusona-logo.png) | A integração do [Trusona](./partner-trusona.md) ajuda você a entrar com segurança e permite a autenticação sem senha, MFA e verificação de licença digital. |
| ![Captura de tela de um logotipo do twilio.](./media/partner-gallery/twilio-logo.png) | [Twilio Verify app](./partner-twilio.md) fornece várias soluções para habilitar a MFA por meio de OTP (senha de uso único) do SMS, uma senha de uso único baseada em tempo (TOTP) e notificações por push e para obedecer aos requisitos de SCA para PSD2. |
| ![Captura de tela de um logotipo do typingDNA](./media/partner-gallery/typingdna-logo.png) | O [TypingDNA](./partner-typingdna.md) permite uma autenticação de cliente forte analisando o padrão de digitação de um usuário. Ele ajuda as empresas a habilitar uma MFA silenciosa e a cumprir os requisitos de SCA para PSD2. |
| ![Captura de tela de um logotipo do whoiam](./media/partner-gallery/whoiam-logo.png) | O [WhoIAM](./partner-whoiam.md) é um aplicativo BRIMS (sistema de gerenciamento de identidades) que permite que as organizações verifiquem sua base de usuários por voz, SMS e email. |

## <a name="role-based-access-control"></a>Controle de acesso baseado em funções 
 
A Microsoft faz parceria com os seguintes ISVs para controle de acesso baseado em função.

| Parceiro ISV | Orientações de descrição e integração |
|:-------------------------|:--------------|
| ![Captura de tela de um logotipo do n8identity](./media/partner-gallery/n8identity-logo.png) | O [N8Identity](./partner-n8identity.md) é uma plataforma de governança de identidade como serviço que fornece solução para atender à migração de contas de clientes e à administração do CSR (Customer Service Requests) em execução no Microsoft Azure. |
| ![Captura de tela de um logotipo do Saviynt](./media/partner-gallery/saviynt-logo.png) | A plataforma nativa de nuvem [Saviynt](./partner-Saviynt.md) promove uma melhor segurança, conformidade e governança por meio de análise inteligente e integração entre aplicativos para simplificar a modernização de ti. |

## <a name="security"></a>Segurança

A Microsoft faz parceria com os seguintes ISVs para segurança.

| Parceiro ISV | Orientações de descrição e integração |
|:-------------------------|:--------------|
| ![Captura de tela de um logotipo do arkose Lab](./media/partner-gallery/arkose-logo.png) | O [arkose Labs](./partner-arkose-labs.md) é um provedor de soluções de prevenção de fraude que ajuda as organizações a proteger contra ataques de bot, ataques de tomada de conta e aberturas de contas fraudulentas. |
| ![Captura de tela de um logotipo do Microsoft Dynamics 365](./media/partner-gallery/microsoft-dynamics365-logo.png) | A [proteção contra fraudes do Microsoft Dynamics 365](./partner-dynamics-365-fraud-protection.md) é uma solução que ajuda as organizações a proteger contra aberturas de contas fraudulentas por meio da impressão digital do dispositivo. |
| ![Captura de tela de um logotipo de ping](./media/partner-gallery/ping-logo.png) | A [identidade de ping](./partner-ping-identity.md) permite acesso híbrido seguro a aplicativos herdados locais em várias nuvens. |
| ![Captura de tela de um logotipo do Strata](./media/partner-gallery/strata-logo.png) | O [Strata](./partner-strata.md) fornece acesso híbrido seguro a aplicativos locais, impondo políticas de acesso consistentes, mantendo as identidades sincronizadas e tornando simples a transição de aplicativos de sistemas de identidade herdados para autenticação baseada em padrões e controle de acesso fornecido pelo Azure ad B2C. |
| ![Captura de tela de um logotipo do Zscaler](./media/partner-gallery/zscaler-logo.png) | O [Zscaler](./partner-zscaler.md) fornece acesso seguro e baseado em políticas a aplicativos e ativos privados sem o custo, os aborrecimentos ou os riscos de segurança de uma VPN. |

## <a name="additional-information"></a>Informações adicionais

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

## <a name="next-steps"></a>Próximas etapas

Selecione um parceiro nas tabelas mencionadas para saber como integrar sua solução com o Azure AD B2C.
