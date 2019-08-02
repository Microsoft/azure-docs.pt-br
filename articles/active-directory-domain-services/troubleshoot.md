---
title: 'Azure Active Directory Domain Services: Guia de solução de problemas | Microsoft Docs'
description: Guia de solução de problemas para os Serviços de Domínio do AD do Azure
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 2df1ac6325f692e2d433238ae0b92d8e3f8482b5
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472291"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services – Guia de solução de problemas
Este artigo fornece dicas de solução de problemas que você pode encontrar ao configurar ou administrar os Serviços de Domínio do AD (Active Directory do Azure).

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Não é possível habilitar o Azure AD Domain Services para seu diretório do Azure AD
Esta seção ajuda a solucionar erros quando você tenta habilitar o Azure AD Domain Services para o seu diretório.

Selecione as etapas de solução de problemas que correspondam à mensagem de erro que você encontrar.

| **Mensagem de erro** | **Resolução** |
| --- |:--- |
| *O nome contoso100.com já está em uso nesta rede. Especifique um nome que não esteja em uso.* |[Conflito de nome de domínio na rede virtual](troubleshoot.md#domain-name-conflict) |
| Não foi possível habilitar os *Serviços de Domínio neste locatário do Azure AD. O serviço não tem as permissões adequadas para o aplicativo chamado 'Sincronização dos Serviços de Domínio do Azure AD'. Exclua o aplicativo chamado 'Sincronização dos Serviços de Domínio do Azure AD' e tente habilitar os Serviços de Domínio para seu locatário do Azure AD.* |[Os Serviços de Domínio não têm as permissões adequadas para o aplicativo Sincronização dos Serviços de Domínio do Azure AD](troubleshoot.md#inadequate-permissions) |
| Não foi possível habilitar os *Serviços de Domínio neste locatário do Azure AD. O aplicativo Serviços de Domínio no seu locatário do Azure AD não tem as permissões necessárias para habilitar os Serviços de Domínio. Exclua o aplicativo com o identificador d87dcbc6-a371-462e-88e3-28ad15ec4e64 e tente habilitar os Serviços de Domínio para o seu locatário do Azure AD.* |[O aplicativo Serviços de Domínio não foi configurado corretamente em seu locatário](troubleshoot.md#invalid-configuration) |
| Não foi possível habilitar os *Serviços de Domínio neste locatário do Azure AD. O aplicativo Microsoft Azure AD está desabilitado no seu locatário do Microsoft Azure AD. Habilite o aplicativo com o identificador de aplicativo 00000002-0000-0000-c000-000000000000 e tente habilitar os Serviços de Domínio para seu locatário do Azure AD.* |[O aplicativo Microsoft Graph está desabilitado no seu locatário do Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflito de Nome de Domínio
**Mensagem de erro:**

*O nome contoso100.com já está em uso nesta rede. Especifique um nome que não esteja em uso.*

**Correção:**

Certifique-se de que você não tenha um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, vamos supor que você tenha um domínio chamado ‘contoso.com’ já disponível na rede virtual selecionada. Posteriormente, você tenta habilitar um domínio gerenciado pelos Azure AD Domain Services com o mesmo nome de domínio (isto é, ‘contoso.com’) nessa rede virtual. Você encontra uma falha ao tentar habilitar os Serviços de Domínio do AD do Azure.

Essa falha ocorre devido a conflitos de nome com o nome de domínio nessa rede virtual. Nessa situação, você deve usar um nome diferente para definir o domínio gerenciado pelos Serviços de Domínio do AD do Azure. Como alternativa, você pode desprovisionar o domínio existente e, em seguida, habilitar os Serviços de Domínio do AD do Azure.

### <a name="inadequate-permissions"></a>Permissões inadequadas
**Mensagem de erro:**

Não foi possível habilitar os *Serviços de Domínio neste locatário do Azure AD. O serviço não tem as permissões adequadas para o aplicativo chamado 'Sincronização dos Serviços de Domínio do Azure AD'. Exclua o aplicativo chamado 'Sincronização dos Serviços de Domínio do Azure AD' e tente habilitar os Serviços de Domínio para seu locatário do Azure AD.*

**Correção:**

Verifique se há um aplicativo com o nome ‘Sincronização dos Serviços de Domínio do Azure AD’ em seu diretório do Azure AD. Se esse aplicativo existir, o exclua e habilite novamente os Serviços de Domínio do Azure AD.

Execute as seguintes etapas para verificar a presença do aplicativo e excluí-lo, caso ele já exista:

1. Navegue até a seção **Aplicativos** do seu diretório do Azure AD no [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Selecione **Todos os Aplicativos** no menu suspenso **Mostrar**. Selecione **Qualquer** no menu suspenso **Status do aplicativo**. Selecione **Qualquer** no menu suspenso **Visibilidade do aplicativo**.
3. Digite **Azure AD Domain Services Sync** na caixa de pesquisa. Se o aplicativo já existir, clique nele e clique no botão **Excluir** na barra de ferramentas para excluí-lo.
4. Após a exclusão do aplicativo, tente habilitar os Serviços de Domínio do AD do Azure novamente.

### <a name="invalid-configuration"></a>Configuração inválida
**Mensagem de erro:**

Não foi possível habilitar os *Serviços de Domínio neste locatário do Azure AD. O aplicativo Serviços de Domínio no seu locatário do Azure AD não tem as permissões necessárias para habilitar os Serviços de Domínio. Exclua o aplicativo com o identificador d87dcbc6-a371-462e-88e3-28ad15ec4e64 e tente habilitar os Serviços de Domínio para o seu locatário do Azure AD.*

**Correção:**

Verifique se você tiver um aplicativo com o nome 'AzureActiveDirectoryDomainControllerServices' (com um identificador de aplicativo d87dcbc6-a371-462e-88e3-28ad15ec4e64) no seu diretório do Azure AD. Se esse aplicativo existir, você precisará excluí-lo e, em seguida, reabilitar os Azure AD Domain Services.

Use o seguinte script do PowerShell para encontrar o aplicativo e excluí-lo.

> [!NOTE]
> Esse script usa cmdlets do **Azure AD PowerShell versão 2**. Para obter uma lista completa de todos os cmdlets disponíveis e para baixar o módulo, leia a [documentação de referência do AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph desabilitado
**Mensagem de erro:**

Não foi possível habilitar os Serviços de Domínio neste locatário do Azure AD. O aplicativo Microsoft Azure AD está desabilitado no seu locatário do Microsoft Azure AD. Habilite o aplicativo com o identificador de aplicativo 00000002-0000-0000-c000-000000000000 e tente habilitar os Serviços de Domínio para seu locatário do Azure AD.

**Correção:**

Verifique se você desabilitou um aplicativo com o identificador 00000002-0000-0000-c000-000000000000. Esse aplicativo é o aplicativo Microsoft Azure AD e fornece acesso à API do Graph para seu locatário do Azure AD. Os Serviços de Domínio do Azure AD precisa que esse aplicativo seja habilitado para sincronizar o Azure AD para seu domínio gerenciado.

Para resolver esse erro, habilite esse aplicativo e tente habilitar os Serviços de Domínio para seu locatário do Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Os usuários não conseguem entrar no domínio gerenciado pelos Serviços de Domínio do AD do Azure
Se um ou mais usuários em seu locatário do Azure AD não conseguirem entrar no domínio gerenciado recém-criado, execute as seguintes etapas de solução de problemas:

* **Entrar usando o formato UPN:** Tente entrar usando o formato UPN (por exemplo, joeuser@contoso.com) em vez do formato SAMNomeDaConta ('CONTOSO\paulousuario'). O SAMAccountName pode ser gerado automaticamente para usuários cujo prefixo UPN é muito longo ou é o mesmo de outro usuário no domínio gerenciado. O formato UPN é exclusivo, de forma garantida, dentro de um locatário do Azure AD.

> [!NOTE]
> Recomendamos usar o formato UPN para entrar no domínio gerenciado do Azure AD Domain Services.
>
>

* Verifique se você [habilitou a sincronização de senhas](active-directory-ds-getting-started-password-sync.md) de acordo com as etapas descritas no Guia de Introdução.
* **Contas externas:** Verifique se a conta de usuário afetada não é uma conta externa no locatário do Azure AD. Exemplos de contas externas incluem as contas da Microsoft (por exemplo, “joe@live.com”) ou as contas de usuário de um diretório externo do Azure AD. Como os Serviços de Domínio do AD do Azure não têm credenciais para tais contas de usuário, esses usuários não podem entrar no domínio gerenciado.
* **Contas sincronizadas:** Se as contas de usuário afetadas forem sincronizadas de um diretório local, verifique se:

  * Você implantou ou atualizou para a [versão recomendada mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
  * Você configurou o Azure AD Connect para [executar uma sincronização completa](active-directory-ds-getting-started-password-sync.md).
  * Dependendo do tamanho do diretório, pode levar algum tempo para as contas de usuário e os hashes de credenciais ficarem disponíveis nos Serviços de Domínio do AD do Azure. Espere tempo suficiente antes de tentar a autenticação novamente.
  * Se o problema persistir depois de verificar as etapas anteriores, tente reiniciar o Serviço de sincronização do Microsoft Azure AD. Em seu computador de sincronização, inicie um prompt de comando e execute os seguintes comandos:

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **Contas somente de nuvem**: Se a conta de usuário afetado for uma conta de usuário somente em nuvem, verifique se o usuário alterou a senha depois de você ter habilitado o Azure AD Domain Services. Essa etapa faz com que os hashes de credenciais necessários para os Serviços de Domínio do AD do Azure sejam gerados.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Há um ou mais alertas no seu domínio gerenciado

Consulte como resolver alertas em seu domínio gerenciado acessando o artigo [Solucionar alertas](troubleshoot-alerts.md).

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Os usuários removidos do seu locatário do Azure AD não são removidos do seu domínio gerenciado
O Azure AD protege você contra a exclusão acidental de objetos de usuário. Quando você excluir uma conta de usuário de seu locatário do Azure AD, o objeto de usuário correspondente será movido para a Lixeira. Quando essa operação de exclusão for sincronizada para seu domínio gerenciado, isso fará com que a conta de usuário correspondente seja marcada como desabilitada. Esse recurso ajuda a recuperar ou a cancelar a exclusão da conta de usuário mais tarde.

A conta de usuário permanecerá no estado desabilitado no domínio gerenciado, mesmo se você recriar uma conta de usuário com o mesmo UPN no diretório do Azure AD. Para remover a conta de usuário do domínio gerenciado, você precisa forçar sua exclusão do locatário do Azure Active Directory.

Para remover completamente a conta de usuário de seu domínio gerenciado, exclua permanentemente o usuário do seu locatário do Azure AD. Use o `Remove-MsolUser`cmdlet do PowerShell com a opção `-RemoveFromRecycleBin`, conforme descrito neste [artigo do MSDN](/previous-versions/azure/dn194132(v=azure.100)).


## <a name="contact-us"></a>Fale conosco
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](contact-us.md).
