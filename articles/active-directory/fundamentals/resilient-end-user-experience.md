---
title: Experiência de usuário final resiliente usando o Azure AD B2C | Microsoft Docs
description: Métodos para criar resiliência na experiência do usuário final usando o Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b47a4a79fd423806693e86aef1edd132d844069e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557897"
---
# <a name="resilient-end-user-experience"></a>Experiência do usuário final resiliente

A experiência do usuário final de inscrição e entrada é composta pelos seguintes elementos:

- As interfaces com as quais o usuário interage – como CSS, HTML e JavaScript

- Os fluxos de usuário e políticas personalizadas que você cria, como inscrição, entrada e edição de perfil

- Os provedores de identidade (IDPs) para seu aplicativo – como conta local nome de usuário/senha, Outlook, Facebook e Google

![Imagem mostra os componentes da experiência do usuário final](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>Escolha entre o fluxo do usuário e a política personalizada  

Para ajudá-lo a configurar as tarefas de identidade mais comuns, o Azure AD B2C fornece [fluxos de usuário](../../active-directory-b2c/user-flow-overview.md)configuráveis internos. Você também pode criar suas próprias [políticas personalizadas](../../active-directory-b2c/custom-policy-overview.md), o que oferece máxima flexibilidade. No entanto, é recomendável usar políticas personalizadas somente para tratar de cenários complexos.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>Como decidir entre o fluxo do usuário e a política personalizada

Escolha fluxos de usuário internos se os requisitos de negócios puderem ser atendidos por eles. Desde o teste extensivo pela Microsoft, você pode minimizar os testes necessários para validar o funcionamento, o desempenho ou a escala desses fluxos de usuário de identidade no nível de política. Você ainda precisa testar seus aplicativos quanto à funcionalidade, ao desempenho e à escala.

Se você [escolher políticas personalizadas](../../active-directory-b2c/custom-policy-get-started.md) devido aos seus requisitos de negócios, certifique-se de executar testes no nível de política para funcional, desempenho ou escala, além de testes no nível de aplicativo.

Consulte o artigo que [compara fluxos de usuário e políticas personalizadas](../../active-directory-b2c/custom-policy-overview.md#comparing-user-flows-and-custom-policies) para ajudá-lo a decidir.

## <a name="choose-multiple-idps"></a>Escolha vários IDPs

Ao usar um [provedor de identidade externo](../../active-directory-b2c/technical-overview.md#external-identity-providers) , como o Facebook, certifique-se de ter um plano de fallback no caso de o provedor externo ficar indisponível.

### <a name="how-to-set-up-multiple-idps"></a>Como configurar vários IDPs

Como parte do processo de registro do provedor de identidade externo, inclua uma declaração de identidade verificada, como o número de celular do usuário ou o endereço de email. Confirme as declarações verificadas para a instância de diretório de Azure AD B2C subjacente. Se o provedor externo não estiver disponível, reverta para a declaração de identidade verificada e retorne para o número de telefone como um método de autenticação. Outra opção é enviar ao usuário uma senha de uso único para permitir que o usuário entre.

 Siga estas etapas para [criar caminhos de autenticação alternativos](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter):

 1. Configure sua política de inscrição para permitir a inscrição por conta local e IDPs externas.

 2. Configure uma política de perfil para permitir que os usuários [vinculem a outra identidade à sua conta](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) depois de entrarem.

 3. Notifique e permita que os usuários [alternem para um IDP alternativo](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) durante uma interrupção.

## <a name="availability-of-multi-factor-authentication"></a>Disponibilidade da autenticação multifator

Ao usar um [serviço de telefone para a autenticação multifator (MFA)](../../active-directory-b2c/phone-authentication-user-flows.md), certifique-se de considerar um provedor de serviços alternativo. O provedor de serviços de telefonia ou Telco local pode sofrer interrupções em seu serviço.

### <a name="how-to-choose-an-alternate-mfa"></a>Como escolher uma MFA alternativa  

O serviço de Azure AD B2C usa um provedor de MFA baseado em telefone interno para fornecer as OTPs (senhas de uso único) baseadas em tempo. Ele está na forma de uma chamada de voz e mensagem de texto para o número de telefone previamente registrado do usuário. Os seguintes métodos alternativos estão disponíveis para vários cenários:

Ao usar **fluxos de usuário**, há dois métodos para criar resiliência:

- **Alterar a configuração de fluxo do usuário**: após detectar uma interrupção na entrega de OTP baseada em telefone, altere o método de entrega de OTP de baseado em telefone para email e reimplante o fluxo do usuário, deixando os aplicativos inalterados.

![captura de tela mostra inscrição de entrada](media/resilient-end-user-experiences/create-sign-in.png)

- **Aplicativos de alteração**: para cada tarefa de identidade, como inscrever-se e entrar, defina dois conjuntos de fluxos de usuário. Configure o primeiro conjunto para usar a OTP baseada em telefone e a segunda para a OTP baseada em email. Após detectar uma interrupção na entrega de OTP baseada em telefone, altere e reimplante os aplicativos para alternar do primeiro conjunto de fluxos de usuário para o segundo, deixando os fluxos de usuário inalterados.  

Ao usar **políticas personalizadas**, há quatro métodos para criar resiliência. A lista abaixo está na ordem de complexidade e você precisará reimplantar as políticas atualizadas.

- **Habilitar a seleção de usuário de** OTP baseado em telefone ou OTP baseado em email: expor ambas as opções para os usuários e permitir que os usuários selecionem uma das opções de seleção automática. Não é necessário fazer alterações nas políticas ou nos aplicativos.

- **Alternar dinamicamente entre OTP baseado em telefone e OTP baseado em email**: coletar informações de telefone e email na inscrição. Defina a política personalizada com antecedência para alternar condicionalmente durante uma interrupção do telefone, da entrega de OTP baseada em telefone para email. Não é necessário fazer alterações nas políticas ou nos aplicativos.

- **Usar um aplicativo autenticador**: Atualize a política personalizada para usar um [aplicativo autenticador](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp). Se sua MFA normal for uma OTP baseada em telefone ou email, reimplante suas políticas personalizadas para alternar para usar o aplicativo autenticador.

>[!Note]
>Os usuários precisam configurar a integração do aplicativo autenticador durante a inscrição.

- **Usar perguntas de segurança**: se nenhum dos métodos acima for aplicável, implemente perguntas de segurança como um backup. Configure perguntas de segurança para os usuários durante a integração ou a edição de perfil e armazene as respostas em um banco de dados separado diferente do diretório. Esse método não atende ao requisito de MFA de "algo que você tem" por exemplo, telefone, mas oferece um secundário "algo que você sabe".

## <a name="use-a-content-delivery-network"></a>Usar uma rede de distribuição de conteúdo

As CDNs (redes de distribuição de conteúdo) são melhores com melhor desempenho e menos caras do que armazenamentos de BLOB para armazenamento da interface do usuário do fluxo de usuários personalizado. O conteúdo da página da Web é entregue mais rapidamente de uma rede distribuída geograficamente de servidores altamente disponíveis.  

Teste periodicamente a disponibilidade da CDN e o desempenho da distribuição de conteúdo por meio de cenário de ponta a ponta e teste de carga. Se você estiver planejando um surto futuro devido à promoção ou ao tráfego de feriados, revise suas estimativas para testes de carga.
  
## <a name="next-steps"></a>Próximas etapas

- [Recursos de resiliência para desenvolvedores de Azure AD B2C](resilience-b2c.md)
  
  - [Interfaces resilientes com processos externos](resilient-external-processes.md)
  - [Resiliência por meio de práticas recomendadas para desenvolvedores](resilience-b2c-developer-best-practices.md)
  - [Resiliência por meio de monitoramento e análise](resilience-with-monitoring-alerting.md)
- [Crie resiliência em sua infraestrutura de autenticação](resilience-in-infrastructure.md)
- [Aumentar a resiliência de autenticação e autorização em seus aplicativos](resilience-app-development-overview.md)