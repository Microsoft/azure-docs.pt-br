---
title: "Azure Active Directory Domain Services: guia de solução de problemas | Microsoft Docs"
description: "Guia de solução de problemas para os Serviços de Domínio do AD do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: d6695b0c40f56093e8701dfe6394143268114453
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services – Guia de solução de problemas
Este artigo fornece dicas de solução de problemas que você pode encontrar ao configurar ou administrar os Serviços de Domínio do AD (Active Directory do Azure).

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Não é possível habilitar o Azure AD Domain Services para seu diretório do Azure AD
Esta seção ajuda a solucionar erros quando você tenta habilitar os Serviços de Domínio do Azure AD para seu diretório e eles falham ou voltam para ‘Desabilitado’.

Selecione as etapas de solução de problemas que correspondam à mensagem de erro que você encontrar.

| **Mensagem de erro** | **Resolução** |
| --- |:--- |
| *O nome contoso100.com já está em uso nesta rede. Especifique um nome que não esteja em uso.* |[Conflito de nome de domínio na rede virtual](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| Não foi possível habilitar os *Serviços de Domínio neste locatário do Azure AD. O serviço não tem as permissões adequadas para o aplicativo chamado 'Sincronização dos Serviços de Domínio do Azure AD'. Exclua o aplicativo chamado 'Sincronização dos Serviços de Domínio do Azure AD' e tente habilitar os Serviços de Domínio para seu locatário do Azure AD.* |[Os Serviços de Domínio não têm as permissões adequadas para o aplicativo Sincronização dos Serviços de Domínio do Azure AD](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| Não foi possível habilitar os *Serviços de Domínio neste locatário do Azure AD. O aplicativo Serviços de Domínio no seu locatário do Azure AD não tem as permissões necessárias para habilitar os Serviços de Domínio. Exclua o aplicativo com o identificador d87dcbc6-a371-462e-88e3-28ad15ec4e64 e tente habilitar os Serviços de Domínio para o seu locatário do Azure AD.* |[O aplicativo Serviços de Domínio não foi configurado corretamente em seu locatário](active-directory-ds-troubleshooting.md#invalid-configuration) |
| Não foi possível habilitar os *Serviços de Domínio neste locatário do Azure AD. O aplicativo Microsoft Azure AD está desabilitado no seu locatário do Microsoft Azure AD. Habilite o aplicativo com o identificador de aplicativo 00000002-0000-0000-c000-000000000000 e tente habilitar os Serviços de Domínio para seu locatário do Azure AD.* |[O aplicativo Microsoft Graph está desabilitado no seu locatário do Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

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

1. Navegue até o **portal clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. No painel esquerdo, selecione o nó **Active Directory** .
3. Selecione o locatário do AD do Azure (diretório) para o qual você deseja habilitar os Serviços de Domínio do AD do Azure.
4. Navegue até a guia **Aplicativos** .
5. Escolha a opção **Aplicativos que minha empresa possui** na lista suspensa.
6. Verifique se há um aplicativo chamado **Sincronização dos Azure AD Domain Services**. Se o aplicativo já existir, exclua-o.
7. Após a exclusão do aplicativo, tente habilitar os Serviços de Domínio do AD do Azure novamente.

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

```
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

* **Conectar-se usando o formato UPN:** tente se conectar usando o formato UPN (por exemplo, “joeuser@contoso.com”), em vez do formato SAMAccountName (“CONTOSO\paulousuario”). O SAMAccountName pode ser gerado automaticamente para usuários cujo prefixo UPN é muito longo ou é o mesmo de outro usuário no domínio gerenciado. O formato UPN é exclusivo, de forma garantida, dentro de um locatário do Azure AD.

> [!NOTE]
> Recomendamos usar o formato UPN para entrar no domínio gerenciado do Azure AD Domain Services.
>
>

* Verifique se você [habilitou a sincronização de senhas](active-directory-ds-getting-started-password-sync.md) de acordo com as etapas descritas no Guia de Introdução.
* **Contas externas:** verifique se a conta de usuário afetada não é uma conta externa no locatário do Azure AD. Exemplos de contas externas incluem as contas da Microsoft (por exemplo, “joe@live.com”) ou as contas de usuário de um diretório externo do Azure AD. Como os Serviços de Domínio do AD do Azure não têm credenciais para tais contas de usuário, esses usuários não podem entrar no domínio gerenciado.
* **Contas sincronizadas:** se as contas de usuário afetadas forem sincronizadas de um diretório local, verifique se:

  * Você implantou ou atualizou para a [versão recomendada mais recente do Azure AD Connect](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
  * Você configurou o Azure AD Connect para [executar uma sincronização completa](active-directory-ds-getting-started-password-sync.md).
  * Dependendo do tamanho do diretório, pode levar algum tempo para as contas de usuário e os hashes de credenciais ficarem disponíveis nos Serviços de Domínio do AD do Azure. Aguarde o tempo suficiente antes de tentar novamente a autenticação (dependendo do tamanho do seu diretório, de algumas horas a um dia ou dois para diretórios grandes).
  * Se o problema persistir depois de verificar as etapas anteriores, tente reiniciar o Serviço de sincronização do Microsoft Azure AD. Em seu computador de sincronização, inicie um prompt de comando e execute os seguintes comandos:

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **Contas somente em nuvem**: se a conta de usuário afetado for uma conta de usuário somente em nuvem, verifique se o usuário alterou sua senha depois que você habilitou os Serviços de Domínio do Azure AD. Essa etapa faz com que os hashes de credenciais necessários para os Serviços de Domínio do AD do Azure sejam gerados.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Os usuários removidos do seu locatário do Azure AD não são removidos do seu domínio gerenciado
O Azure AD protege você contra a exclusão acidental de objetos de usuário. Quando você excluir uma conta de usuário de seu locatário do Azure AD, o objeto de usuário correspondente será movido para a Lixeira. Quando essa operação de exclusão for sincronizada para seu domínio gerenciado, isso fará com que a conta de usuário correspondente seja marcada como desabilitada. Esse recurso ajuda a recuperar ou a cancelar a exclusão da conta de usuário mais tarde.

A conta de usuário permanecerá no estado desabilitado no domínio gerenciado, mesmo se você recriar uma conta de usuário com o mesmo UPN no diretório do Azure AD. Para remover a conta de usuário do domínio gerenciado, você precisa forçar sua exclusão do locatário do Azure AD.

Para remover completamente a conta de usuário de seu domínio gerenciado, exclua permanentemente o usuário do seu locatário do Azure AD. Use o cmdlet Remove-MsolUser PowerShell com a opção '-RemoveFromRecycleBin', como descrito neste [artigo MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).

## <a name="contact-us"></a>Entre em contato
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](active-directory-ds-contact-us.md).
