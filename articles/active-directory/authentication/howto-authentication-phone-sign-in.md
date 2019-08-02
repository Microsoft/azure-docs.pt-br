---
title: Entrada sem senha com o aplicativo Microsoft Authenticator (versão prévia) – Azure Active Directory
description: Entrar no Azure AD usando o aplicativo Microsoft Authenticator sem usar sua senha (versão prévia pública)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c464874708c7b93ec5620cc9ae253912ce1a4790
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357144"
---
# <a name="passwordless-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Entrada sem senha pelo telefone com o aplicativo Microsoft Authenticator (versão prévia pública)

O aplicativo Microsoft Authenticator pode ser usado para entrar em qualquer conta do Azure AD sem usar uma senha. Similar à tecnologia do [Windows Hello para Empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification), o Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário que esteja associada a um dispositivo e use biometria ou PIN.

![Exemplo de uma entrada de navegador solicitando que o usuário aprove a entrada](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Em vez de ver um prompt de senha após inserir um nome de usuário, uma pessoa que tenha habilitado entrada pelo telefone no aplicativo Microsoft Authenticator verá uma mensagem dizendo para tocar em um número em seu aplicativo. No aplicativo, o usuário deverá indicar o mesmo número, escolher Aprovar, informar o PIN ou biometria e, em seguida, a autenticação será concluída.

## <a name="enable-my-users"></a>Habilitar meus usuários

### <a name="tenant-prerequisites"></a>Pré-requisitos do locatário

* Active Directory do Azure
* Usuários finais habilitados para Autenticação Multifator do Azure
* Os usuários podem registrar seus dispositivos

### <a name="steps-to-enable"></a>Etapas para habilitar

Siga as etapas no artigo [habilitar a entrada sem senha para o Azure ad](howto-authentication-passwordless-enable.md#enable-new-passwordless-authentication-methods), para habilitar métodos de autenticação sem senha no seu diretório.

> [!NOTE]
> Se você habilitou esse recurso anteriormente para seu locatário usando um script do PowerShell, a definição de uma nova política para usuários e grupos substituirá a política existente em todo o locatário. 
>

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Como os meus usuários finais habilitam entrada pelo telefone?

Para versão prévia pública, não há nenhuma maneira de impor que os usuários criem ou usem essa nova credencial. Um usuário final só encontrará a entrada sem senha quando um administrador tiver habilitado seu locatário e o usuário tiver atualizado seu aplicativo Microsoft Authenticator para habilitar a entrada pelo telefone.

> [!NOTE]
> Essa funcionalidade está no aplicativo desde março de 2017, portanto, existe a possibilidade de que, quando a política estiver habilitada para um locatário, os usuários possam encontrar esse fluxo imediatamente. Esteja atento e prepare seus usuários para essa alteração.
>

1. Registrar-se na Autenticação Multifator do Azure
1. Versão mais recente do Microsoft Authenticator instalada em dispositivos que executam o iOS 8.0 ou superior ou o Android 6.0 ou superior.
1. Conta corporativa ou de estudante com notificações por push adicionadas ao aplicativo. A documentação do usuário final pode ser encontrada em [https://aka.ms/authappstart](https://aka.ms/authappstart).

Depois que o usuário tem a conta MFA com notificações por push no aplicativo Microsoft Authenticator, ele pode seguir as etapas no artigo [Entrar com seu telefone, não sua senha](../user-help/microsoft-authenticator-app-phone-signin-faq.md) para concluir o registro de entrada com telefone.

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="ad-fs-integration"></a>Integração do AD FS

Quando um usuário tiver habilitado a credencial sem senha do Microsoft Authenticator, a autenticação para esse usuário sempre enviará como padrão uma notificação de aprovação. Essa lógica impede que os usuários em um locatário híbrido sejam direcionados para o ADFS para verificação de entrada sem que o usuário realize uma etapa adicional de clicar em "Usar sua senha em vez disso". Esse processo também ignorará quaisquer políticas de Acesso Condicional locais e fluxos de autenticação de Passagem. A exceção a esse processo é se um login_hint for especificado, um usuário será encaminhado automaticamente para AD FS e ignorará a opção de usar a credencial sem senha.

### <a name="azure-mfa-server"></a>Servidor MFA do Azure

Usuários finais habilitados para MFA por meio do servidor de MFA do Azure local da organização ainda podem criar e usar uma credencial de logon único de telefone sem senha. Se o usuário tentar atualizar várias instalações (+5) do Microsoft Authenticator com a credencial, essa alteração poderá resultar em um erro.  

### <a name="device-registration"></a>Registro de dispositivo

Um dos pré-requisitos para criar essa nova credencial forte é que o dispositivo no qual ela reside esteja registrado no locatário do Azure AD para um usuário individual. Devido às restrições de registro de dispositivo, um dispositivo só pode ser registrado em um único locatário. Esse limite significa que apenas uma conta corporativa ou de estudante no aplicativo Microsoft Authenticator pode ser habilitada para entrada por telefone.

## <a name="next-steps"></a>Próximas etapas

[O que não tem senha?](concept-authentication-passwordless.md)

[Saiba mais sobre registro de dispositivo](../devices/overview.md#getting-devices-in-azure-ad)

[Saiba mais sobre a Autenticação Multifator do Azure](../authentication/howto-mfa-getstarted.md)
