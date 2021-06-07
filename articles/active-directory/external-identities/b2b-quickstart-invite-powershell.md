---
title: 'Guia de início rápido: adicionar um usuário participante usando o PowerShell – Azure AD'
description: Este início rápido ensinará a usar o PowerShell para enviar um convite para um usuário de colaboração do Azure AD B2B.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/28/2018
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom:
- it-pro
- seo-update-azuread-jan
- mode-api
ms.collection: M365-identity-device-management
ms.openlocfilehash: 765d8ea99167512e553cef9cc6f7fed583eff469
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529825"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Início rápido: adicionar um usuário convidado com o PowerShell

Existem várias maneiras de convidar parceiros externos para aplicativos e serviços com a colaboração B2B do Azure Active Directory. No início rápido anterior, você aprendeu como adicionar usuários convidados diretamente no portal de administração do Azure Active Directory. Também é possível usar o PowerShell para adicionar usuários convidados, um de cada vez ou em massa. Neste início rápido, você usará o comando New-AzureADMSInvitation para adicionar um usuário convidado ao seu locatário do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="install-the-latest-azureadpreview-module"></a>Instalar o módulo AzureADPreview mais recente
Instale a versão mais recente do módulo do Azure AD PowerShell para Graph (AzureADPreview). 

Primeiro, verifique quais módulos estão instalados. Abra o Windows PowerShell como usuário com privilégios elevados (Executar como administrador) e execute o seguinte comando:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Se o módulo AzureADPreview for exibido sem uma mensagem que indica uma versão posterior, você estará pronto. Caso contrário, com base na saída, realize um destes procedimentos:

- Se nenhum resultado for retornado, execute o seguinte comando para instalar o módulo AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Se apenas o módulo AzureAD for exibido nos resultados, execute os comandos a seguir para instalar o módulo AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Se somente o módulo AzureADPreview for exibido nos resultados, mas você receber uma mensagem que indica uma versão posterior, execute os comandos a seguir para atualizar o módulo: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Talvez você receba um aviso de que está instalando o módulo de um repositório não confiável. Isso ocorrerá se você não tiver definido o repositório PSGallery como confiável anteriormente. Pressione **Y** para instalar o módulo.

### <a name="get-a-test-email-account"></a>Obter uma conta de email de teste

É necessário ter uma conta de email de teste para enviar o convite. A conta precisa estar fora da organização. É possível usar qualquer tipo de conta, incluindo uma conta social, como um endereço gmail.com ou outlook.com.

## <a name="sign-in-to-your-tenant"></a>Entrar no locatário

Execute o comando a seguir para se conectar com o domínio do locatário:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Por exemplo, `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Insira suas credenciais quando solicitado.

## <a name="send-an-invitation"></a>Enviar um convite

1. Para enviar um convite para a conta de email de teste, execute o seguinte comando do PowerShell (substitua **"Sanda"** e **sanda\@fabrikam.com** pelo nome da conta de email de teste e o endereço de email): 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.microsoft.com -SendInvitationMessage $true
   ```
2. O comando enviará um convite para o endereço de email especificado. Verifique o resultado, que deverá ser semelhante ao seguinte:

   ![Saída do PowerShell exibindo aceitações pendentes do usuário](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Verificar se o usuário existe no diretório

1. Para verificar se o usuário convidado foi adicionado ao Azure AD, execute o seguinte comando:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Verifique a saída para confirmar se o usuário que você convidou está na lista com um nome UPN no formato *endereçodeemail*#EXT#\@*domínio*. Por exemplo, *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, em que contoso.onmicrosoft.com é a organização da qual você enviou os convites.

   ![Saída do PowerShell exibindo o usuário convidado adicionado](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Limpar recursos

É possível excluir a conta de usuário de teste no diretório quando ela não for mais necessária. Execute o comando a seguir para excluir uma conta de usuário:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Por exemplo: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você convidou e adicionou um único usuário convidado ao seu diretório usando o PowerShell. Em seguida, saiba como convidar usuários em massa usando o PowerShell.

> [!div class="nextstepaction"]
> [Tutorial: Convidar usuários de colaboração B2B do Azure AD em massa](tutorial-bulk-invite.md)
