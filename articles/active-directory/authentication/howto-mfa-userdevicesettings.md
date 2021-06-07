---
title: Gerenciar métodos de autenticação para autenticação multifator do Azure AD-Azure Active Directory
description: Saiba como você pode definir Azure Active Directory configurações de usuário para a autenticação multifator do Azure AD
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc0be7e50c5cce511fafd7d8b407626bd57659bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98183125"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Gerenciar métodos de autenticação de usuário para a autenticação multifator do Azure AD

Os usuários no Azure AD têm dois conjuntos distintos de informações de contato:  

- Informações de contato do perfil público, que são gerenciadas no perfil do usuário e visíveis para os membros da sua organização. Para os usuários sincronizados do Active Directory local, essas informações são gerenciadas no Windows Server Active Directory Domain Services local.
- Os métodos de autenticação, que são sempre mantidos privados e são usados somente para autenticação, incluindo a MFA (autenticação multifator). Os administradores podem gerenciar esses métodos na folha do método de autenticação de um usuário e os usuários podem gerenciar seus métodos na página de informações de segurança de myaccount.

Ao gerenciar os métodos de autenticação multifator do Azure AD para seus usuários, os administradores de autenticação podem: 

1. Adicione métodos de autenticação para um usuário específico, incluindo números de telefone usados para MFA.
1. Redefina a senha de um usuário.
1. Exigir que um usuário registre novamente para MFA.
1. Revogar sessões de MFA existentes.
1. Excluir as senhas de aplicativo existentes de um usuário  

## <a name="add-authentication-methods-for-a-user"></a>Adicionar métodos de autenticação para um usuário 

Você pode adicionar métodos de autenticação para um usuário por meio do portal do Azure ou Microsoft Graph.  

> [!NOTE]
> Por motivos de segurança, os campos de informações de contato do usuário público não devem ser usados para executar MFA. Em vez disso, os usuários devem preencher seus números de método de autenticação a serem usados para MFA.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Adicionar métodos de autenticação do portal do Azure":::

Para adicionar métodos de autenticação para um usuário por meio do portal do Azure:  

1. Faça logon no **Portal do Azure**. 
1. Navegue até **Azure Active Directory**  >  **usuários**  >  **todos os usuários**. 
1. Escolha o usuário para o qual você deseja adicionar um método de autenticação e selecione **métodos de autenticação**.  
1. Na parte superior da janela, selecione **+ Adicionar método de autenticação**.
   1. Selecione um método (número de telefone ou email). O email pode ser usado para redefinição de senha automática, mas não para autenticação. Ao adicionar um número de telefone, selecione um tipo de telefone e insira o número de telefone com um formato válido (por exemplo, + 1 4255551234).
   1. Selecione **Adicionar**.

> [!NOTE]
> A experiência de visualização permite que os administradores adicionem quaisquer métodos de autenticação disponíveis para os usuários, enquanto a experiência original permite apenas a atualização de telefones e métodos de telefone alternativos.

### <a name="manage-methods-using-powershell"></a>Gerenciar métodos usando o PowerShell:  

Instale o módulo Microsoft. Graph. Identity. entrada do PowerShell usando os comandos a seguir. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Listar métodos de autenticação baseados em telefone para um usuário específico.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Crie um método de autenticação de telefone celular para um usuário específico.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType "mobile" -phoneNumber "+1 7748933135"
```

Remover um método de telefone específico para um usuário

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Os métodos de autenticação também podem ser gerenciados usando APIs Microsoft Graph, mais informações podem ser encontradas no documento [visão geral da API dos métodos de autenticação do Azure ad](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

## <a name="manage-user-authentication-options"></a>Gerenciar opções de autenticação de usuário

Se você tiver atribuído a função de *administrador de autenticação* , poderá exigir que os usuários redefinam sua senha, registrem-se novamente para MFA ou revokem as sessões de MFA existentes de seu objeto de usuário. Para gerenciar as configurações do usuário, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Escolha o usuário no qual você deseja executar uma ação e selecione **métodos de autenticação**. Na parte superior da janela, escolha uma das seguintes opções para o usuário:
   - **Redefinir senha** redefine a senha do usuário e atribui uma senha temporária que deve ser alterada na próxima entrada.
   - **Exigir o novo registro de MFA** faz isso para que, quando o usuário entrar na próxima vez, ele seja solicitado a configurar um novo método de autenticação de MFA.
   
      > [!NOTE]
      > Os métodos de autenticação atualmente registrados do usuário não são excluídos quando um administrador requer um novo registro para MFA. Depois que um usuário se registra novamente para MFA, recomendamos que eles examinem suas informações de segurança e excluam os métodos de autenticação registrados anteriormente que não podem mais ser usados.
   
   - **Revogar sessões de MFA** limpa as sessões do MFA lembradas do usuário e exige que elas executem o MFA da próxima vez que for exigida pela política no dispositivo.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Gerenciar métodos de autenticação do portal do Azure":::

## <a name="delete-users-existing-app-passwords"></a>Excluir senhas de aplicativo de usuários existentes

Para usuários que definiram senhas de aplicativo, os administradores também podem optar por excluir essas senhas, fazendo com que a autenticação herdada falhe nesses aplicativos. Essas ações poderão ser necessárias se você precisar fornecer assistência a um usuário ou precisar redefinir seus métodos de autenticação. Aplicativos sem navegador que foram associados a essas senhas de aplicativo deixarão de funcionar até que uma nova senha de aplicativo seja criada. 

Para excluir as senhas de aplicativo de um usuário, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No lado esquerdo, selecione **Azure Active Directory**  >  **usuários**  >  **todos os usuários**.
1. Selecione **Autenticação Multifator**. Role para a direita para ver esta opção de menu. Selecione a captura de tela de exemplo abaixo para ver a janela portal do Azure completa e o local do menu: [ ![ selecione autenticação multifator na janela usuários no Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de etapa rápida é exibida à direita.
1. Selecione **gerenciar configurações de usuário** e marque a caixa **excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**, conforme mostrado no exemplo a seguir: ![ excluir todas as senhas de aplicativo existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selecione **salvar** e **fechar**.

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como definir configurações de usuário individuais. Para definir as configurações gerais do serviço de autenticação multifator do Azure AD, consulte [definir configurações de autenticação multifator do Azure ad](howto-mfa-mfasettings.md).

Se os usuários precisarem de ajuda, consulte o [Guia do usuário para autenticação multifator do Azure ad](../user-help/multi-factor-authentication-end-user-first-time.md).
