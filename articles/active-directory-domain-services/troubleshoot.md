---
title: Solução de problemas Azure Active Directory Domain Services | Microsoft Docs '
description: Saiba como solucionar erros comuns ao criar ou gerenciar Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 89b04f86d41f8e4828580f70a9aec8acea3e0053
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618443"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Erros comuns e etapas de solução de problemas para Azure Active Directory Domain Services

Como parte central da identidade e da autenticação para aplicativos, o Azure Active Directory Domain Services (AD DS do Azure) às vezes tem problemas. Se você tiver problemas, há algumas mensagens de erro comuns e etapas de solução de problemas associadas para ajudá-lo a fazer as coisas serem executadas novamente. A qualquer momento, você também pode [abrir uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

Este artigo fornece etapas para solução de problemas comuns no Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Não é possível habilitar o Azure AD Domain Services para seu diretório do Azure AD

Se você tiver problemas para habilitar o Azure AD DS, examine os seguintes erros e etapas comuns para resolvê-los:

| **Mensagem de erro de exemplo** | **Resolução** |
| --- |:--- |
| *O nome aaddscontoso.com já está em uso nesta rede. Especifique um nome que não esteja em uso.* |[Conflito de nome de domínio na rede virtual](troubleshoot.md#domain-name-conflict) |
| *Não foi possível habilitar os serviços de domínio neste locatário do Azure AD. O serviço não tem as permissões adequadas para o aplicativo chamado ' Azure AD Domain Services Sync '. Exclua o aplicativo chamado ' Azure AD Domain Services Sync ' e tente habilitar os serviços de domínio para seu locatário do Azure AD.* |[Os serviços de domínio não têm as permissões adequadas para o aplicativo de sincronização de Azure AD Domain Services](troubleshoot.md#inadequate-permissions) |
| *Não foi possível habilitar os serviços de domínio neste locatário do Azure AD. O aplicativo de serviços de domínio no seu locatário do Azure AD não tem as permissões necessárias para habilitar os serviços de domínio. Exclua o aplicativo com o identificador de aplicativo d87dcbc6-a371-462e-88e3-28ad15ec4e64 e tente habilitar os serviços de domínio para seu locatário do Azure AD.* |[O aplicativo de serviços de domínio não está configurado corretamente no seu locatário do Azure AD](troubleshoot.md#invalid-configuration) |
| *Não foi possível habilitar os serviços de domínio neste locatário do Azure AD. O aplicativo Microsoft Azure AD está desabilitado em seu locatário do Azure AD. Habilite o aplicativo com o identificador de aplicativo 00000002-0000-0000-C000-000000000000 e tente habilitar os serviços de domínio para seu locatário do Azure AD.* |[O aplicativo Microsoft Graph está desabilitado no seu locatário do Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflito de Nome de Domínio

**Mensagem de erro**

*O nome aaddscontoso.com já está em uso nesta rede. Especifique um nome que não esteja em uso.*

**Resolução**

Verifique se você não tem um ambiente de AD DS existente com o mesmo nome de domínio na mesma ou em uma rede virtual emparelhada. Por exemplo, você pode ter um domínio AD DS chamado *aaddscontoso.com* que é executado em VMs do Azure. Quando você tenta habilitar um domínio gerenciado do Azure AD DS com o mesmo nome de domínio do *aaddscontoso.com* na rede virtual, a operação solicitada falha.

Essa falha ocorre devido a conflitos de nome para o nome de domínio na rede virtual. Uma pesquisa DNS verifica se um ambiente de AD DS existente responde no nome de domínio solicitado. Para resolver essa falha, use um nome diferente para configurar o domínio gerenciado ou desprovisionar o domínio de AD DS existente e, em seguida, tente habilitar o AD DS do Azure novamente.

### <a name="inadequate-permissions"></a>Permissões inadequadas

**Mensagem de erro**

*Não foi possível habilitar os serviços de domínio neste locatário do Azure AD. O serviço não tem as permissões adequadas para o aplicativo chamado ' Azure AD Domain Services Sync '. Exclua o aplicativo chamado ' Azure AD Domain Services Sync ' e tente habilitar os serviços de domínio para seu locatário do Azure AD.*

**Resolução**

Verifique se há um aplicativo chamado *Azure AD Domain Services sincronização* no diretório do Azure AD. Se esse aplicativo existir, exclua-o e tente novamente habilitar o Azure AD DS. Para verificar se há um aplicativo existente e excluí-lo, se necessário, conclua as seguintes etapas:

1. No portal do Azure, selecione **Azure Active Directory** no menu de navegação à esquerda.
1. Selecione **Aplicativos empresariais**. Selecione *Todos os aplicativos* no menu suspenso **Tipo de aplicativo** e, sem seguida, **Aplicar**.
1. Na caixa de pesquisa, insira *Azure AD Domain Services sincronização*. Se o aplicativo existir, selecione-o e escolha **excluir**.
1. Depois de excluir o aplicativo, tente habilitar o Azure AD DS novamente.

### <a name="invalid-configuration"></a>Configuração inválida

**Mensagem de erro**

*Não foi possível habilitar os serviços de domínio neste locatário do Azure AD. O aplicativo de serviços de domínio no seu locatário do Azure AD não tem as permissões necessárias para habilitar os serviços de domínio. Exclua o aplicativo com o identificador de aplicativo d87dcbc6-a371-462e-88e3-28ad15ec4e64 e tente habilitar os serviços de domínio para seu locatário do Azure AD.*

**Resolução**

Verifique se você tem um aplicativo existente chamado *AzureActiveDirectoryDomainControllerServices* com um identificador de aplicativo de *d87dcbc6-a371-462e-88e3-28ad15ec4e64* no seu diretório do Azure AD. Se esse aplicativo existir, exclua-o e tente habilitar a AD DS do Azure novamente.

Use o seguinte script do PowerShell para procurar uma instância de aplicativo existente e excluí-la, se necessário:

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

### <a name="microsoft-graph-disabled"></a>Microsoft Graph desabilitado

**Mensagem de erro**

*Não foi possível habilitar os serviços de domínio neste locatário do Azure AD. O aplicativo Microsoft Azure AD está desabilitado em seu locatário do Azure AD. Habilite o aplicativo com o identificador de aplicativo 00000002-0000-0000-C000-000000000000 e tente habilitar os serviços de domínio para seu locatário do Azure AD.*

**Resolução**

Verifique se você desabilitou um aplicativo com o identificador *00000002-0000-0000-C000-000000000000*. Esse aplicativo é o aplicativo Microsoft Azure AD e fornece acesso à API do Graph para seu locatário do Azure AD. Para sincronizar seu locatário do Azure AD, esse aplicativo deve ser habilitado.

Para verificar o status desse aplicativo e habilitá-lo, se necessário, conclua as seguintes etapas:

1. No portal do Azure, selecione **Azure Active Directory** no menu de navegação à esquerda.
1. Selecione **Aplicativos empresariais**. Selecione *Todos os aplicativos* no menu suspenso **Tipo de aplicativo** e, sem seguida, **Aplicar**.
1. Na caixa de pesquisa, digite *00000002-0000-0000-C000-00000000000*. Selecione o aplicativo e, em seguida, escolha **Propriedades**.
1. Se **habilitado para que os usuários se conectem** estiver definido como *não*, defina o valor como *Sim* e, em seguida, selecione **salvar**.
1. Após habilitar o aplicativo, tente habilitar o Azure AD DS novamente.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Os usuários não conseguem entrar no domínio gerenciado pelos Serviços de Domínio do AD do Azure

Se um ou mais usuários em seu locatário do Azure AD não conseguirem entrar no domínio gerenciado, conclua as seguintes etapas de solução de problemas:

* **Formato das credenciais** -tente usar o formato UPN para especificar as credenciais, como `dee@aaddscontoso.onmicrosoft.com` . O formato UPN é a maneira recomendada para especificar credenciais no Azure AD DS. Verifique se esse UPN está configurado corretamente no Azure AD.

    O *sAMAccountName* para sua conta, como *AADDSCONTOSO\driley* , pode ser gerado automaticamente se houver vários usuários com o mesmo prefixo UPN em seu locatário ou se o prefixo UPN for muito longo. Portanto, o formato *sAMAccountName* para sua conta pode ser diferente do que você espera ou usa em seu domínio local.

* **Sincronização de senha** – Verifique se você habilitou a sincronização de senha para [usuários somente de nuvem][cloud-only-passwords] ou para [ambientes híbridos usando Azure ad Connect][hybrid-phs].
    * **Contas híbridas sincronizadas:** Se as contas de usuário afetadas forem sincronizadas de um diretório local, verifique as seguintes áreas:
    
      * Você implantou ou atualizou para o, a [versão mais recente recomendada do Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594).
      * Você configurou Azure AD Connect para [executar uma sincronização completa][hybrid-phs].
      * Dependendo do tamanho do diretório, pode levar algum tempo para que as contas de usuário e os hashes de credenciais estejam disponíveis no domínio gerenciado. Certifique-se de Aguardar tempo suficiente antes de tentar autenticar no domínio gerenciado.
      * Se o problema persistir depois de verificar as etapas anteriores, tente reiniciar o serviço de *sincronização de Microsoft Azure ad*. Em seu servidor de Azure AD Connect, abra um prompt de comando e execute os seguintes comandos:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Contas somente em nuvem**: se a conta de usuário afetada for uma conta de usuário somente em nuvem, verifique se o [usuário alterou sua senha depois de habilitar o Azure AD DS][cloud-only-passwords]. Essa redefinição de senha faz com que os hashes de credencial necessários para o domínio gerenciado sejam gerados.

* **Verifique se a conta de usuário está ativa**: por padrão, cinco tentativas de senha inválidas em 2 minutos no domínio gerenciado fazem com que uma conta de usuário seja bloqueada por 30 minutos. O usuário não pode entrar enquanto a conta está bloqueada. Após 30 minutos, a conta de usuário será desbloqueada automaticamente.
  * Tentativas de senha inválidas no domínio gerenciado não bloqueiam a conta de usuário no Azure AD. A conta de usuário é bloqueada somente dentro do domínio gerenciado. Verifique o status da conta de usuário no *Active Directory console administrativo (ADAC)* usando a [VM de gerenciamento][management-vm], não no Azure AD.
  * Você também pode [Configurar políticas de senha refinadas][password-policy] para alterar o limite e a duração do bloqueio padrão.

* **Contas externas** – Verifique se a conta de usuário afetada não é uma conta externa no locatário do Azure AD. Exemplos de contas externas incluem contas da Microsoft como `dee@live.com` contas de usuário ou de um diretório externo do Azure AD. O Azure AD DS não armazena credenciais para contas de usuário externas para que não possam entrar no domínio gerenciado.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Há um ou mais alertas no seu domínio gerenciado

Se houver alertas ativos no domínio gerenciado, ele poderá impedir que o processo de autenticação funcione corretamente.

Para ver se há alertas ativos, [Verifique o status de integridade de um domínio gerenciado][check-health]. Se algum alerta for mostrado, [solucione-o e resolva-os][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Os usuários removidos do seu locatário do Azure AD não são removidos do seu domínio gerenciado

O Azure AD protege contra a exclusão acidental de objetos de usuário. Quando você exclui uma conta de usuário de um locatário do Azure AD, o objeto de usuário correspondente é movido para a lixeira. Quando essa operação de exclusão é sincronizada com o domínio gerenciado, a conta de usuário correspondente é marcada como desabilitada. Esse recurso ajuda a recuperar, ou a restaurar, a conta de usuário.

A conta de usuário permanece no estado desabilitado no domínio gerenciado, mesmo se você recriar uma conta de usuário com o mesmo UPN no diretório do Azure AD. Para remover a conta de usuário do domínio gerenciado, você precisará forçar a exclusão do locatário do Azure AD.

Para remover totalmente uma conta de usuário de um domínio gerenciado, exclua o usuário permanentemente do seu locatário do Azure AD usando o cmdlet [Remove-MsolUser][Remove-MsolUser] do PowerShell com o `-RemoveFromRecycleBin` parâmetro.

## <a name="next-steps"></a>Próximas etapas

Se você continuar tendo problemas, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
