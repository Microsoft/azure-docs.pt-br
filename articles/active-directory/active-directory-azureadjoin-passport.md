---
title: Autenticar identidades sem senhas com o Windows Hello for Business e Azure AD | | Microsoft Docs
description: "Fornece uma visão geral do Windows Hello for Business e informações adicionais sobre como implantar o Windows Hello for Business."
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 62adf8a9fd4400a056e2c0f59c79431acbad5865
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="authenticating-identities-without-passwords-through-windows-hello-for-business"></a>Autenticação de identidades sem senhas com o Windows Hello for Business
Somente os atuais métodos de autenticação com senha não são suficientes para manter os usuários protegidos. Os usuários reutilizar e esquecem senhas. As senhas são propensas a sofrer violações, phishing e falhas e são fáceis de adivinhar. Elas também podem ser difíceis de ser lembradas e propensas a ataques como "[pass the hash](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-windows-hello-for-business"></a>Sobre o Hello for Business
O Windows Hello for Business é uma abordagem de autenticação com chave privada/pública ou baseada em certificados para empresas e consumidores que vai além de senhas. Essa forma de autenticação depende de credenciais de par de chaves que podem substituir senhas e são resistentes a violações, roubos e phishing.

 O Windows Hello for Business permite ao usuário autenticar-se em uma conta da Microsoft, uma conta do Windows Server Active Directory, uma conta do Microsoft Azure Active Directory (Azure AD) ou um serviço de terceiros que ofereça suporte à autenticação FIDO (Fast IDentity Online). Após uma verificação inicial em duas etapas durante o registro no Windows Hello for Business, um Windows Hello for Business será configurado no dispositivo do usuário, e o usuário configura um gesto, que pode ser o Windows Hello ou um PIN. O usuário fornece o gesto para verificar sua identidade. Em seguida, o Windows usa o Windows Hello for Business para autenticar o usuário e ajudá-lo a acessar recursos e serviços protegidos.

A chave privada é disponibilizada somente por meio de um "gesto do usuário", como um PIN, dados biométricos ou dispositivo remoto, como um cartão inteligente, que o usuário usou para entrar no dispositivo. Essa informação é vinculada a um certificado ou um par de chaves assimétricas. Essa chave privada será atestada por hardware se o dispositivo tiver um chip do TPM (Trusted Platform Module). A chave privada nunca deixará o dispositivo.

A chave pública é registrada com o Active Directory do Azure e o Windows Server Active Directory (para local). Os IDPs (Provedores de identidade) validam o usuário mapeando a chave pública do usuário para a chave privada e fornece informações de entrada por meio do OTP (One Time Password), PhoneFactor ou um mecanismo de notificação diferente.

## <a name="why-enterprises-should-adopt-windows-hello-for-business"></a>Por que as empresas devem adotar o Windows Hello for Business
Ao habilitarem o Windows Hello for Business, as empresas podem tornar seus recursos ainda mais seguros ao:

* Configuração do Windows Hello for Business com uma opção preferencial de hardware. Isso significa que as chaves serão geradas no TPM 1.2 ou 2.0, quando estiverem disponíveis. Quando o TPM não estiver disponível, o software gerará a chave.
* Definir a complexidade e o comprimento do PIN, e se o uso do Hello está habilitado na sua organização
* Configurar o Windows Hello for Business para dar suporte a cenários semelhantes ao uso de cartão inteligente, usando a confiança baseada em certificado.

## <a name="how-windows-hello-for-business-works"></a>Como o Windows Hello for Business funciona
1. As chaves são geradas no hardware pelo TPM ou software. Muitos dispositivos têm um chip do TPM interno que protege o hardware integrando chaves criptográficas a dispositivos. O TPM 1.2 ou 2.0 gera chaves ou certificados que são criados a partir de chaves geradas.
2. O TPM atesta essas chaves associadas ao hardware.
3. Um único gesto de desbloqueio desbloqueia o dispositivo. Esse gesto permite o acesso a vários recursos se o dispositivo estiver associado a um domínio ou ao AD do Azure.

## <a name="how-the-windows-hello-for-business-lifecycle-works"></a>Como funciona o ciclo de vida do Windows Hello for Business
![Ciclo de vida do Windows Hello for Business](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

O diagrama acima ilustra o par de chaves privada/pública e a validação pelo provedor de identidade. Cada uma dessas etapas é explicada detalhadamente a seguir:

1. O usuário comprova sua identidade por meio de vários métodos internos de verificação (gestos, cartões inteligentes físicos, autenticação multifator) e envia essas informações para o IDP (provedor de identidade), como o Active Directory do Azure ou o Active Directory local.
2. O dispositivo cria e atesta a chave, captura a parte pública dessa chave, anexa a essa parte as instruções da estação, entra e a envia ao IDP para registrá-la.
3. Assim que o IDP registra a parte pública da chave, o IDP desafia o dispositivo a entrar com a parte privada da chave.
4. Em seguida, o IDP valida e emite o token de autenticação que permite ao usuário e ao dispositivo acessar recursos protegidos. Os IDPs podem gravar em aplicativos multiplataforma ou usar o suporte ao navegador por meio de APIs JavaScript/Webcrypto para criar e usar as credenciais do Windows Hello for Business para seus usuários.

## <a name="the-deployment-requirements-for-windows-hello-for-business"></a>Os requisitos de implantação para o Windows Hello for Business
### <a name="at-the-enterprise-level"></a>No nível corporativo
* A empresa tem uma assinatura do Azure.

### <a name="at-the-user-level"></a>No nível do usuário
* O computador do usuário deve executar o Windows 10 Professional ou Enterprise

Para obter instruções de implantação detalhadas, veja [Habilitar o Windows Hello for Business na organização](active-directory-azureadjoin-passport-deployment.md).

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

