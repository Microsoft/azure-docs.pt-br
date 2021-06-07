---
title: Redefinir o status de resgate de um usuário convidado-Azure AD
description: Saiba como redefinir o status de resgate de convite para um Azure Active Directory usuários convidados B2B em identidades externas do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2998c3ea0d65bd3c96bd1ac5bdfa8ff148c6c4cc
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780422"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>Redefinir o status de resgate de um usuário convidado

Depois que um usuário convidado resgatau seu convite para colaboração B2B, pode haver ocasiões em que você precisará atualizar suas informações de entrada, por exemplo, quando:

- O usuário deseja entrar usando um email e um provedor de identidade diferentes
- A conta do usuário em seu locatário inicial foi excluída e recriada
- O usuário mudou para uma empresa diferente, mas ainda precisa do mesmo acesso aos seus recursos
- As responsabilidades do usuário foram passadas para outro usuário

Para gerenciar esses cenários anteriormente, era necessário excluir manualmente a conta do usuário convidado do seu diretório e convidar o usuário novamente. Agora você pode usar o PowerShell ou a API de convite Microsoft Graph para redefinir o status de resgate do usuário e convidar o usuário novamente enquanto retém a ID de objeto, as associações de grupo e as atribuições de aplicativo do usuário. Quando o usuário resgata o novo convite, o UPN do usuário não é alterado, mas o nome de entrada do usuário é alterado para o novo email. O usuário pode, subsequentemente, entrar usando o novo email ou um email que você adicionou à `otherMails` Propriedade do objeto de usuário.

## <a name="reset-the-email-address-used-for-sign-in"></a>Redefinir o endereço de email usado para entrar

Se um usuário quiser entrar usando um email diferente:

1. Verifique se o novo endereço de email foi adicionado à `mail` `otherMails` propriedade ou do objeto de usuário. 
2.  Substitua o endereço de email na `InvitedUserEmailAddress` Propriedade pelo novo endereço de email.
3. Use um dos métodos abaixo para redefinir o status de resgate do usuário.

> [!NOTE]
>Durante a visualização pública, quando você estiver redefinindo o endereço de email do usuário, é recomendável definir a `mail` propriedade para o novo endereço de email. Dessa forma, o usuário pode resgatar o convite entrando em seu diretório, além de usar o link de resgate no convite.
>
## <a name="use-powershell-to-reset-redemption-status"></a>Usar o PowerShell para redefinir o status de resgate

Instale o módulo mais recente do PowerShell do AzureADPreview e crie um novo convite com `InvitedUserEmailAddress` definido como o novo endereço de email e `ResetRedemption` defina como `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Usar Microsoft Graph API para redefinir o status de resgate

Usando a [API de convite Microsoft Graph](/graph/api/resources/invitation?view=graph-rest-1.0), defina a `resetRedemption` propriedade como `true` e especifique o novo endereço de email na `invitedUserEmailAddress` propriedade.

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Próximas etapas

- [Adicionar usuários de colaboração B2B do Azure Active Directory usando o PowerShell](customize-invitation-api.md#powershell)
- [Propriedades de um usuário convidado do Azure AD B2B](user-properties.md)
