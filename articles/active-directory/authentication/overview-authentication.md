---
title: Visão geral da autenticação do Azure Active Directory
description: Saiba mais sobre os diferentes métodos de autenticação e os recursos de segurança para conexões de usuário com o Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f4659b9ee809cc1f1caeb1cb9c0d626b1b3a3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725647"
---
# <a name="what-is-azure-active-directory-authentication"></a>O que é a autenticação do Azure Active Directory?

Um dos principais recursos de uma plataforma de identidade é verificar ou *autenticar* as credenciais quando um usuário entra em um dispositivo, um aplicativo ou um serviço. No Azure AD (Azure Active Directory), a autenticação envolve mais do que apenas a verificação de um nome de usuário e uma senha. Para aprimorar a segurança e reduzir a necessidade de suporte técnico, a autenticação do Azure AD inclui os seguintes componentes:

* Redefinição de senha de autoatendimento
* Autenticação multifator do Azure AD
* Integração híbrida para o write-back de alterações de senha no ambiente local
* Integração híbrida para imposição de políticas de proteção de senha em um ambiente local
* Autenticação sem senha

Assista ao nosso vídeo curto para saber mais sobre esses componentes de autenticação.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4KVJA]

## <a name="improve-the-end-user-experience"></a>Aprimorar a experiência do usuário final

O Azure AD ajuda a proteger a identidade de um usuário e a simplificar a experiência de conexão dele. Recursos como redefinição de senha por autoatendimento permitem que os usuários atualizem ou alterem as respectivas senhas usando um navegador da Web em qualquer dispositivo. Esse recurso é especialmente útil quando o usuário esquece a senha ou a conta é bloqueada. Sem esperar que um suporte técnico ou um administrador forneça suporte, um usuário pode desbloquear a si mesmo e continuar trabalhando.

A Autenticação Multifator do Azure AD permite que os usuários escolham uma forma adicional de autenticação durante a conexão, como uma chamada telefônica ou uma notificação de aplicativo móvel. Essa capacidade reduz o requisito de uma só forma fixa de autenticação secundária, como um token de hardware. Se, atualmente, o usuário não tiver uma forma de autenticação adicional, ele poderá escolher outro método e continuar funcionando.

![Métodos de autenticação em uso na tela de login](media/concept-authentication-methods/overview-login.png)

A autenticação sem senha elimina a necessidade de o usuário criar e lembrar uma senha segura. Funcionalidades como o Windows Hello para Empresas ou chaves de segurança FIDO2 permitem que os usuários entrem em um dispositivo ou um aplicativo sem uma senha. Essa capacidade pode reduzir a complexidade do gerenciamento de senhas em diferentes ambientes.

## <a name="self-service-password-reset"></a>Redefinição de senha de autoatendimento

A redefinição de senha por autoatendimento dá aos usuários a capacidade de alterar ou redefinir a senha, sem envolvimento do administrador ou do suporte técnico. Se a conta de um usuário estiver bloqueada ou se ele esquecer a senha, ele poderá seguir os avisos para desbloquear a si mesmo e voltar ao trabalho. Essa capacidade reduz as chamadas de suporte técnico e a perda de produtividade quando um usuário não consegue entrar no dispositivo ou em um aplicativo.

A redefinição de senha por autoatendimento funciona nos seguintes cenários:

* **Alteração de senha:** o usuário sabe a senha, mas deseja alterá-la para uma nova.
* **Redefinição de senha:** quando um usuário não consegue entrar, como quando esqueceu a senha e deseja redefini-la.
* **Desbloqueio de conta:** quando um usuário não consegue entrar porque a conta está bloqueada e deseja desbloqueá-la.

Quando um usuário atualiza ou redefine a senha usando a redefinição de senha por autoatendimento, também pode ser feito o write-back dessa senha em um ambiente local do Active Directory. O write-back de senha garante que um usuário possa usar imediatamente as credenciais atualizadas com dispositivos e aplicativos locais.

## <a name="azure-ad-multi-factor-authentication"></a>Autenticação multifator do Azure AD

A autenticação multifator é um processo em que um usuário é solicitado durante o processo de conexão para obter uma forma adicional de identificação, como inserir um código no celular ou fornecer uma verificação de impressão digital.

Se você usar apenas uma senha para autenticar um usuário, isso deixará um vetor inseguro para ataque. Se a senha for fraca ou se tiver sido exposta em outro lugar, será realmente o usuário entrando com o nome de usuário e a senha ou um invasor? Quando você precisar de uma segunda forma de autenticação, a segurança será aprimorada, pois esse fator adicional não será algo fácil de ser obtido ou duplicado por um invasor.

![Imagem conceitual das diferentes formas de autenticação multifator](./media/concept-mfa-howitworks/methods.png)

A Autenticação Multifator do Azure AD funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que você sabe, normalmente, uma senha.
* Algo que você tem, como um dispositivo confiável que não seja facilmente duplicado, como um telefone ou uma chave de hardware.
* Algo que você é: uma biometria, como uma impressão digital ou uma verificação facial.

Os usuários podem se registrar na redefinição de senha por autoatendimento e na Autenticação Multifator do Azure AD em uma etapa para simplificar a experiência de integração. Os administradores podem definir quais formas de autenticação secundária podem ser usadas. A Autenticação Multifator do Azure AD também pode ser necessária quando os usuários executam uma redefinição de senha por autoatendimento para proteger ainda mais esse processo.

## <a name="password-protection"></a>Proteção por senha

Por padrão, o Azure AD bloqueia senhas fracas, como *Password1*. Uma lista global de senhas proibidas é automaticamente atualizada e imposta, que inclui senhas fracas conhecidas. Se um usuário do Azure AD tentar definir a senha como uma dessas senhas fracas, ele receberá uma notificação para escolher uma senha mais segura.

Para aumentar a segurança, você pode definir políticas de proteção de senha personalizadas. Essas políticas podem usar filtros para bloquear qualquer variação de uma senha que contenha um nome, como *Contoso* ou uma localização como *Londres*, por exemplo.

Para a segurança híbrida, você pode integrar a proteção de senha do Azure AD a um ambiente local do Active Directory. Um componente instalado no ambiente local recebe a lista global de senhas excluídas e as políticas de proteção de senha personalizadas do Azure AD e os controladores de domínio as usam para processar eventos de alteração de senha. Essa abordagem híbrida garante que, independente de qual for a maneira ou a localização usada por um usuário para alterar as credenciais, você imporá o uso de senhas fortes.

## <a name="passwordless-authentication"></a>Autenticação sem senha

A meta final para muitos ambientes é remover o uso de senhas como parte dos eventos de conexão. Recursos como a proteção por senha do Azure ou a Autenticação Multifator do Azure AD ajudam a aprimorar a segurança, mas um nome de usuário e uma senha permanecem sendo uma forma fraca de autenticação que pode ser exposta ou atacada por força bruta.

![Segurança versus conveniência com o processo de autenticação que leva à eliminação de senhas](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Quando você se conecta com um método sem senha, as credenciais são fornecidas pelo uso de métodos como biometria com o Windows Hello para Empresas ou uma chave de segurança FIDO2. Esses métodos de autenticação não podem ser duplicados com facilidade por um invasor.

O Azure AD fornece maneiras de se autenticar nativamente usando métodos sem senha, a fim de simplificar a experiência de conexão para os usuários e reduzir o risco de ataques.

## <a name="next-steps"></a>Próximas etapas

Para começar, confira o [tutorial da SSPR (redefinição de senha por autoatendimento)][tutorial-sspr] e a [Autenticação Multifator do Azure AD][tutorial-azure-mfa].

Para saber mais sobre os conceitos de redefinição de senha por autoatendimento, confira [Como funciona a redefinição de senha por autoatendimento do Azure AD][concept-sspr].

Para saber mais sobre os conceitos da autenticação multifator, confira [Como funciona a Autenticação Multifator do Azure AD][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
