---
title: Entrada de chave de segurança sem senha-Azure Active Directory
description: Habilitar a entrada de chave de segurança sem senha no Azure AD usando as chaves de segurança do FIDO2
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71a29a2a7e379e253b52813eb7a76f669abbf668
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653823"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Habilitar entrada de chave de segurança sem senha 

Para empresas que usam senhas hoje e têm um ambiente de PC compartilhado, as chaves de segurança fornecem uma maneira simples para os trabalhadores autenticarem sem inserir um nome de usuário ou senha. As chaves de segurança fornecem produtividade aprimorada para os trabalhadores e têm mais segurança.

Este documento se concentra na habilitação da autenticação com senha baseada em chave de segurança. No final deste artigo, você poderá entrar em aplicativos baseados na Web com sua conta do Azure AD usando uma chave de segurança FIDO2.

## <a name="requirements"></a>Requisitos

- [Autenticação multifator do Azure AD](howto-mfa-getstarted.md)
- Habilitar o [registro de informações de segurança combinadas](concept-registration-mfa-sspr-combined.md)
- [Chaves de segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatíveis
- Webauthn requer o Windows 10 versão 1903 ou superior * *

Para usar as chaves de segurança para fazer logon em serviços e aplicativos Web, você deve ter um navegador que ofereça suporte ao protocolo webauthn. Entre eles estão o Microsoft Edge, o Chrome, o Firefox e o Safari.

## <a name="prepare-devices"></a>Preparar dispositivos

Para dispositivos ingressados no Azure AD, a melhor experiência é no Windows 10 versão 1903 ou superior.

Dispositivos ingressados no Azure AD híbrido devem executar o Windows 10 versão 2004 ou superior.

## <a name="enable-passwordless-authentication-method"></a>Habilitar método de autenticação com senha

### <a name="enable-the-combined-registration-experience"></a>Habilitar a experiência de registro combinado

Os recursos de registro para métodos de autenticação com senha contam com o recurso de registro combinado. Siga as etapas no artigo [habilitar o registro de informações de segurança combinadas](howto-registration-mfa-sspr-combined.md), para habilitar o registro combinado.

### <a name="enable-fido2-security-key-method"></a>Habilitar o método de chave de segurança FIDO2

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory**  >    >  política de método de autenticação dos **métodos de autenticação** de segurança  >  .
1. Na chave de **segurança** do método FIDO2, escolha as seguintes opções:
   1. **Habilitar** -Sim ou não
   1. **Destino** -todos os usuários ou Selecionar usuários
1. **Salve** a configuração.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registro de usuário e gerenciamento de chaves de segurança FIDO2

1. Navegue até [https://myprofile.microsoft.com](https://myprofile.microsoft.com) .
1. Entrar se ainda não estiver.
1. Clique em **informações de segurança**.
   1. Se o usuário já tiver pelo menos um método de autenticação multifator do Azure AD registrado, ele poderá registrar imediatamente uma chave de segurança FIDO2.
   1. Se eles não tiverem pelo menos um método de autenticação multifator do Azure AD registrado, eles deverão adicionar um.
1. Adicione uma chave de segurança do FIDO2 clicando em **Adicionar método** e escolhendo **chave de segurança**.
1. Escolha **dispositivo USB** ou **dispositivo NFC**.
1. Prepare sua chave e escolha **Avançar**.
1. Uma caixa será exibida e solicitará que o usuário crie/Insira um PIN para sua chave de segurança e, em seguida, execute o gesto necessário para a chave, biométrica ou toque.
1. O usuário será devolvido à experiência de registro combinada e será solicitado a fornecer um nome significativo para a chave para que o usuário possa identificar qual delas tem várias. Clique em **Próximo**.
1. Clique em **concluído** para concluir o processo.

## <a name="sign-in-with-passwordless-credential"></a>Entrar com credencial sem senha

No exemplo abaixo, um usuário já provisionou sua chave de segurança FIDO2. O usuário pode optar por entrar na Web com sua chave de segurança FIDO2 dentro de um navegador com suporte no Windows 10 versão 1903 ou superior.

![Entrada de chave de segurança Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Solução de problemas e comentários

Se você quiser compartilhar comentários ou encontrar problemas com esse recurso, compartilhe por meio do aplicativo Hub de comentários do Windows usando as seguintes etapas:

1. Inicie o **Hub de comentários** e verifique se você está conectado.
1. Envie comentários sob a seguinte categorização:
   - Categoria: segurança e privacidade
   - Subcategoria: FIDO
1. Para capturar os logs, use a opção para **recriar o meu problema**.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="security-key-provisioning"></a>Provisionamento de chave de segurança

O provisionamento e desprovisionamento de chaves de segurança do administrador não está disponível.

### <a name="upn-changes"></a>Alterações de UPN

Estamos trabalhando para dar suporte a um recurso que permita a alteração de UPN em dispositivos ingressados no Azure AD híbrido e no Azure AD. Se o UPN de um usuário for alterado, você não poderá mais modificar as chaves de segurança FIDO2 para considerar a alteração. A resolução é redefinir o dispositivo e o usuário precisa registrá-lo novamente.

## <a name="next-steps"></a>Próximas etapas

[Entrada da chave de segurança do FIDO2 do Windows 10](howto-authentication-passwordless-security-key-windows.md)

[Habilitar a autenticação FIDO2 para recursos locais](howto-authentication-passwordless-security-key-on-premises.md)

[Saiba mais sobre o registro de dispositivo](../devices/overview.md)

[Saiba mais sobre a autenticação multifator do Azure AD](../authentication/howto-mfa-getstarted.md)
