---
title: Convidar usuários internos para colaboração B2B-Azure AD
description: Se você tiver contas de usuário internas para parceiros, distribuidores, fornecedores, fornecedores e outros convidados, poderá mudar para a colaboração B2B do Azure AD convidando-as para entrar com suas próprias credenciais externas ou logon. Use o PowerShell ou a API de convite Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576423"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Convidar usuários internos para colaboração B2B

Antes da disponibilidade da colaboração B2B do Azure AD, as organizações podem colaborar com distribuidores, fornecedores, fornecedores e outros usuários convidados Configurando credenciais internas para eles. Se você tiver usuários convidados internos como esses, poderá convidá-los para usar a colaboração B2B. Esses usuários convidados B2B poderão usar suas próprias identidades e credenciais para entrar, e você não precisará manter senhas ou gerenciar ciclos de vida da conta.

O envio de um convite para uma conta interna existente permite reter a ID de objeto do usuário, o UPN, as associações de grupo e as atribuições de aplicativo. Você não precisa excluir e convidar o usuário novamente manualmente ou reatribuir recursos. Para convidar o usuário, use a API de convite para passar o objeto de usuário interno e o endereço de email do usuário convidado junto com o convite. Quando o usuário aceita o convite, o serviço B2B altera o objeto de usuário interno existente para um usuário B2B. No futuro, o usuário deve entrar nos serviços de recursos de nuvem usando suas credenciais B2B.

## <a name="things-to-consider"></a>Itens a serem considerados

- **Acesso a recursos locais**: depois que o usuário é convidado para colaboração B2B, ele ainda pode usar suas credenciais internas para acessar recursos locais. Você pode evitar isso redefinindo ou alterando a senha na conta interna. A exceção é [um email de autenticação de senha de uso único](one-time-passcode.md); Se o método de autenticação do usuário for alterado para senha de uso único, eles não poderão mais usar suas credenciais internas.

- **Cobrança**: esse recurso não altera o UserType para o usuário, portanto, ele não alterna automaticamente o modelo de cobrança do usuário para o [preço de usuário ativo mensal (mau) de identidades externas](external-identities-pricing.md). Para ativar o preço do MAU para o usuário, altere o UserType para o usuário para `guest` . Observe também que seu locatário do Azure AD deve estar [vinculado a uma assinatura do Azure](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) para ativar a cobrança do mau.

- O **convite é unidirecional**: você pode convidar usuários internos para usar a colaboração B2B, mas não pode remover as credenciais B2B depois que elas são adicionadas. Para alterar o usuário de volta para um usuário somente interno, você precisará excluir o objeto de usuário e criar um novo.

- **Equipes**: quando o usuário acessa equipes usando suas credenciais externas, seu locatário não estará disponível inicialmente no seletor de locatários de equipes. O usuário pode acessar as equipes usando uma URL que contém o contexto do locatário, por exemplo: `https://team.microsoft.com/?tenantId=<TenantId>` . Depois disso, o locatário ficará disponível no seletor de locatário de equipes.

- **Usuários sincronizados no local**: para contas de usuário que são sincronizadas entre o local e a nuvem, o diretório local permanece a fonte de autoridade depois que eles são convidados a usar a colaboração B2B. As alterações feitas na conta local serão sincronizadas com a conta de nuvem, incluindo a desabilitação ou a exclusão da conta. Portanto, você não pode impedir que o usuário entre em sua conta local enquanto mantém sua conta de nuvem simplesmente excluindo a conta local. Em vez disso, você pode definir a senha da conta local para um GUID aleatório ou outro valor desconhecido.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Como convidar usuários internos para colaboração B2B

Você pode usar o PowerShell ou a API de convite para enviar um convite B2B para o usuário interno. Verifique se o endereço de email que você deseja usar para o convite está definido como o endereço de email externo no objeto de usuário interno.

- Você deve usar o endereço de email na propriedade User. mail para o convite.
- O domínio na propriedade mail do usuário deve corresponder à conta que está usando para entrar. Caso contrário, alguns serviços, como as equipes, não poderão autenticar o usuário.

Por padrão, o convite enviará ao usuário um email informando que eles foram convidados, mas você poderá suprimir esse email e enviar seu próprio.

> [!NOTE]
> Para enviar seu próprio email ou outra comunicação, você pode usar o `New-AzureADMSInvitation` com o `-SendInvitationMessage:$false` para convidar os usuários silenciosamente e, em seguida, enviar sua própria mensagem de email para o usuário convertido. Consulte [personalização e API de colaboração B2B do Azure ad](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Usar o PowerShell para enviar um convite B2B

Você precisará do módulo do Azure AD PowerShell versão 2.0.2.130 ou posterior. Use o comando a seguir para atualizar para o módulo mais recente do PowerShell do AzureAD e convidar o usuário interno para a colaboração B2B:

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Usar a API de convite para enviar um convite B2B

O exemplo a seguir ilustra como chamar a API de convite para convidar um usuário interno como um usuário B2B.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

A resposta à API é a mesma resposta que você obtém ao convidar um novo usuário convidado para o diretório.
## <a name="next-steps"></a>Próximas etapas

- [Resgate de convite de colaboração B2B](redemption-experience.md)
