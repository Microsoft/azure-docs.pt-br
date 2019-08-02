---
title: Tutorial para convidar usuários de colaboração B2B em massa – Azure Active Directory | Microsoft Docs
description: Neste tutorial, você aprenderá a usar o PowerShell e um arquivo CSV para enviar convites em massa para usuários externos de colaboração do Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3bd02afa1fe1aaba6602201f839468a58673c29
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277991"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Tutorial: Convidar usuários de colaboração B2B do Azure AD em massa

Se você usar a colaboração B2B do Azure AD (Azure Active Directory) para trabalhar com parceiros externos, poderá convidar vários usuários convidados para a sua organização ao mesmo tempo. Neste tutorial, você aprenderá a usar o PowerShell para enviar convites em massa para usuários externos. Especificamente, faça o seguinte:

> [!div class="checklist"]
> * Prepare um arquivo com valores separados por vírgula (.csv) com as informações do usuário
> * Execute um script do PowerShell para enviar convites
> * Verifique se os usuários foram adicionados ao diretório

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="install-the-latest-azureadpreview-module"></a>Instalar o módulo AzureADPreview mais recente
Instale a versão mais recente do módulo do Azure AD PowerShell para Graph (AzureADPreview). 

Primeiro, verifique quais módulos estão instalados. Abra o Windows PowerShell como usuário com privilégios elevados (Executar como administrador) e execute o seguinte comando:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Com base na saída, realize um destes procedimentos:

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

### <a name="get-test-email-accounts"></a>Obter contas de email de teste

É necessário ter duas ou mais contas de email de teste para enviar o convite. As contas precisam estar fora da organização. É possível usar qualquer tipo de conta, incluindo contas sociais, como endereços gmail.com ou outlook.com.

## <a name="prepare-the-csv-file"></a>Preparar o arquivo CSV

No Microsoft Excel, crie um arquivo CSV com a lista de nomes de usuários e endereços de email dos convidados. Não se esqueça de incluir os títulos de coluna **Name** e **InvitedUserEmailAddress**. 

Por exemplo, crie uma planilha no seguinte formato:


![Saída do PowerShell exibindo aceitações pendentes do usuário](media/tutorial-bulk-invite/AddUsersExcel.png)

Salve o arquivo como **C:\BulkInvite\Invitations.csv**. 

Se você não tiver o Excel, poderá criar um arquivo CSV em qualquer editor de texto, como o Bloco de Notas. Separe cada valor com uma vírgula e cada linha com uma nova linha. 

## <a name="sign-in-to-your-tenant"></a>Entrar no locatário

Execute o comando a seguir para se conectar com o domínio do locatário:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Por exemplo, `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Insira suas credenciais quando solicitado.

## <a name="send-bulk-invitations"></a>Enviar convites em massa

Para enviar os convites, execute o seguinte script do PowerShell (em que **c:\bulkinvite\invitations.csv** é o caminho do arquivo CSV): 

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv
   
$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo
   
$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."
   
foreach ($email in $invitations) 
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.microsoft.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```
O script envia um convite para os endereços de email no arquivo Invitations.csv. Você deverá ver uma saída semelhante ao exemplo a seguir para cada usuário:

![Saída do PowerShell exibindo aceitações pendentes do usuário](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>Verificar se os usuários existem no diretório

Para verificar se os usuários convidados foram adicionados ao Azure AD, execute o seguinte comando:
```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```
Você deverá ver os usuários que você convidou listados, com um nome UPN no formato *endereçodeemail*#EXT#\@*domínio*. Por exemplo, *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, em que contoso.onmicrosoft.com é a organização da qual você enviou os convites.

## <a name="clean-up-resources"></a>Limpar recursos

Será possível excluir as contas de usuário de teste no diretório quando elas não forem mais necessárias. Execute o comando a seguir para excluir uma conta de usuário:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Por exemplo: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você enviou convites em massa para usuários convidados fora da sua organização. Em seguida, saiba como o processo de resgate de convites funciona.

> [!div class="nextstepaction"]
> [Saiba mais sobre o processo de resgate de convite de colaboração do Azure AD B2B](redemption-experience.md)

