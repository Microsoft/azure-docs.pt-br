---
title: Solução de problemas de colaboração B2B – Azure Active Directory | Microsoft Docs
description: Correções para problemas comuns com a colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 02/12/2021
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60cd944ecb144a30e872259f6e959a11c3ea6319
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100365422"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Solução de problemas de colaboração B2B do Azure Active Directory

Confira aqui algumas correções para problemas comuns da colaboração B2B do Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > - **A partir de 4 de janeiro de 2021**, o Google está [preterindo o suporte de entrada do WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver usando a federação do Google ou a inscrição por autoatendimento com o Gmail, você deverá [testar seus aplicativos nativos de linha de negócios para garantir a compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).
   > - **A partir de outubro de 2021**, a Microsoft não dará mais suporte ao resgate de convites criando contas e locatários do Azure AD não gerenciados para cenários de colaboração B2B. Durante a preparação, incentivamos os clientes a aceitarem a [autenticação de senha avulsa por email](one-time-passcode.md). Agradecemos seus comentários sobre essa versão prévia do recurso pública e estamos empolgados em criar ainda mais maneiras de colaborar.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Adicionei um usuário externo, mas não consigo vê-lo em meu Catálogo de Endereços Global ou no seletor de pessoas

Para casos nos quais os usuários externos não são preenchidos na lista, o objeto pode demorar alguns minutos para replicar.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Um usuário convidado de B2B não está aparecendo no seletor de pessoas do SharePoint Online/OneDrive

A capacidade de pesquisar por usuários convidados existentes no seletor de pessoas do SharePoint Online (SPO) está desativado por padrão para coincidir com o comportamento herdado.

Esse recurso pode ser habilitado usando a configuração 'ShowPeoplePickerSuggestionsForGuestUsers' na coleta do site e do locatário. Defina esse recurso usando os cmdlets Set-SPOTenant e SPOSite, que permitem aos membros pesquisar todos os usuários convidados existentes no diretório. Alterações no escopo de locatário não afetam sites SPO já provisionados.

## <a name="invitations-have-been-disabled-for-directory"></a>Os convites foram desabilitados para o diretório

Se você for notificado de que não tem permissões para convidar usuários, verifique se sua conta de usuário está autorizada a convidar usuários externos em Azure Active Directory > configurações de usuário > usuários externos > gerenciar configurações de colaboração externas:

![Captura de tela mostrando as configurações de usuários externos](media/troubleshoot/external-user-settings.png)

Se você tiver modificado essas configurações recentemente ou se tiver atribuído a função Emissor de convite convidado a um usuário, talvez ocorra um atraso de 15 a 60 minutos até que as alterações tenham efeito.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>O usuário que eu convidei está recebendo um erro durante o resgate

Os erros comuns incluem:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>O Admin do convidado não permite a criação de Usuários Verificados por Email em seu locatário

Ao convidar usuários cuja organização está usando o Azure Active Directory onde a conta do usuário específico não existe (por exemplo, o usuário não existe no Azure AD contoso.com). O administrador de contoso.com pode ter uma política em vigor para impedir a criação de usuários. O usuário deve verificar com o administrador para determinar se os usuários externos são permitidos. A administração de usuário externo pode ter que permitir usuários verificados por email em seu domínio (consulte este [artigo](/powershell/module/msonline/set-msolcompanysettings) sobre como permitir Usuários de Email Verificados).

![Erro informando que o locatário não permite usuários verificados por email](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>O usuário externo ainda não existe em um domínio federado

Se você estiver usando a autenticação de Federação e o usuário ainda não existir no Azure Active Directory, o usuário não poderá ser convidado.

Para resolver esse problema, o administrador do usuário externo deve sincronizar a conta do usuário ao Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Como ‘\#’, que normalmente não é um caractere válido, é sincronizado com o Azure AD?

"\#" é um caractere reservado no UPNs para colaboração B2B do Azure AD ou usuários externos, pois a conta convidada user@contoso.com se torna user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Portanto, não há permissão para \# em UPNs provenientes do local entrar no Portal do Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recebo um erro ao adicionar usuários externos a um grupo sincronizado

Os usuários externos podem ser adicionados apenas a grupos de "Segurança" ou "atribuído", e não a grupos que são masterizados localmente.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Meu usuário externo não recebeu um email para o resgate

O convidado deve verificar com seu ISP ou o filtro de spam para garantir que o seguinte endereço tem permissão: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Observei que, às vezes, a mensagem personalizada não é incluída nas mensagens de convite

Para cumprir as leis de privacidade, nossas APIs não incluem mensagens personalizadas no convite por email quando:

- O emissor do convite não tem um endereço de email no locatário que está convidando
- Quando uma entidade do serviço de aplicativo envia o convite

Se esse cenário for importante para você, suprima nosso email de convite de API e envie-o por meio de um mecanismo de email de sua escolha. Consulte o departamento jurídico de sua organização para verificar se todo email enviado dessa forma também está em conformidade com as leis de privacidade.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Você recebe um erro "AADSTS65005" ao tentar fazer logon em um recurso do Azure

Um usuário que tem uma conta de convidado não pode fazer logon e está recebendo a seguinte mensagem de erro:

```plaintext
    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.
```

O usuário tem uma conta de usuário do Azure e é um locatário viral que foi abandonado ou não gerenciado. Além disso, não há administradores globais no locatário.

Para resolver esse problema, você deve assumir o locatário abandonado. Consulte  [assumir um diretório não gerenciado como administrador em Azure Active Directory](../enterprise-users/domains-admin-takeover.md). Você também deve acessar o DNS voltado para a Internet para o sufixo de domínio em questão a fim de fornecer evidências diretas de que você está no controle do namespace. Depois que o locatário for retornado a um estado gerenciado, discuta com o cliente se deixar os usuários e o nome de domínio verificado é a melhor opção para sua organização.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Um usuário convidado com um locatário just-in-time ou "viral" não pode redefinir sua senha

Se o locatário de identidade for um locatário JIT (just-in-time) ou viral (ou seja, um locatário do Azure não gerenciado e separado), somente o usuário convidado poderá redefinir sua senha. Às vezes, uma organização [assumirá o gerenciamento de locatários virais](../enterprise-users/domains-admin-takeover.md) que são criados quando os funcionários usam seus emails de trabalho para se inscrever em serviços. Depois que a organização assumir um locatário viral, somente um administrador da organização poderá redefinir a senha do usuário ou habilitar a SSPR. Se necessário, como a organização de convite, você pode remover a conta de usuário convidado do diretório e reenviar um convite.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>Um usuário convidado não pode usar o módulo AzureAD do PowerShell v1

A partir de 18 de novembro de 2019, os usuários convidados em seu diretório (definidos como contas de usuário em que a propriedade **UserType** é igual a **Guest**) são impedidos de usar o módulo AzureAD do PowerShell v1. No futuro, um usuário precisará ser um usuário membro (em que **UserType** é igual a **Member**) ou usar o módulo AzureAD do PowerShell v2.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>Em um locatário do governo dos EUA do Azure, não consigo convidar um usuário convidado de colaboração B2B

Na nuvem do governo dos EUA do Azure, atualmente, a colaboração B2B só tem suporte entre locatários que estão na nuvem do governo dos EUA do Azure e que ambos dão suporte à colaboração B2B. Se você convidar um usuário em um locatário que não faz parte da nuvem do governo dos EUA do Azure ou que ainda não dá suporte à colaboração B2B, você receberá um erro. Para obter detalhes e limitações, consulte [Azure Active Directory Premium as variações P1 e P2](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2).

## <a name="i-receive-the-error-that-azure-ad-cannot-find-the-aad-extensions-app-in-my-tenant"></a>Recebi o erro que o Azure AD não consegue localizar o AAD-Extensions-app em meu locatário

Ao usar recursos de inscrição de autoatendimento, como atributos de usuário personalizados ou fluxos de usuário, um aplicativo chamado `aad-extensions-app. Do not modify. Used by AAD for storing user data.` é criado automaticamente. Ele é usado pelas identidades externas do Azure AD para armazenar informações sobre usuários que se inscrevem e atributos personalizados coletados.

Se você excluiu acidentalmente o `aad-extensions-app`, é possível recuperá-lo em até 30 dias. Você pode restaurar o aplicativo usando o módulo do PowerShell do Azure AD.

1. Inicie o módulo do PowerShell do Azure AD e execute `Connect-AzureAD` .
1. Entre como um administrador global para o locatário do Azure AD do qual você deseja recuperar o aplicativo excluído.
1. Execute o comando do PowerShell `Get-AzureADDeletedApplication` .
1. Localize o aplicativo na lista em que o nome de exibição começa `aad-extensions-app` e copie seu `ObjectId` valor de propriedade.
1. Execute o comando do PowerShell `Restore-AzureADDeletedApplication -ObjectId {id}` . Substitua a `{id}` parte do comando pelo da `ObjectId` etapa anterior.

Agora você deve ver o aplicativo restaurado no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

[Obtenção de suporte para colaboração B2B](../fundamentals/active-directory-troubleshooting-support-howto.md)
