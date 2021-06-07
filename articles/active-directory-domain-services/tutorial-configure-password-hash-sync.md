---
title: Habilitar sincronização de hash de senha para o Azure AD Domain Services | Microsoft Docs
description: Neste tutorial, saiba como habilitar a sincronização de hash de senha usando o Azure AD Connect para um domínio gerenciado do Azure Active Directory Domain Services.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: e83d8941d1be7fd36f53a881a21716252ad01954
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618341"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Tutorial: Habilitar sincronização de senha no Azure Active Directory Domain Services para ambientes híbridos

Para ambientes híbridos, um locatário do Azure AD (Active Directory) pode ser configurado para sincronizar com um ambiente AD DS (Active Directory Domain Services) local usando o Azure AD Connect. Por padrão, o Azure AD Connect não sincroniza os hashes de senha do NTLM (NT LAN Manager) herdado e do Kerberos necessários para o Azure AD DS (Azure Active Directory Domain Services).

Para usar o Azure AD DS com contas sincronizadas de um ambiente do AD DS local, é necessário configurar o Azure AD Connect para sincronizar esses hashes de senha necessários para a autenticação do NTLM e do Kerberos. Depois que o Azure AD Connect é configurado, uma criação de conta ou evento de alteração de senha local também sincroniza os hashes de senha herdados com o Azure AD.

Você não precisará seguir estas etapas se usar contas somente de nuvem sem nenhum ambiente do AD DS local ou se usar uma *floresta de recursos*. Para domínios gerenciados que usam uma floresta de recursos, os hashes de senha locais nunca são sincronizados. A autenticação para contas locais usa as relações de confiança de floresta com seus próprios controladores de domínio do AD DS.

Neste tutorial, você aprenderá:

> [!div class="checklist"]
> * Por que são necessários hashes de senha herdada do Kerberos e NTLM
> * Como configurar a sincronização de hash de senha herdada para o Azure AD Connect

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado à assinatura sincronizada com um diretório local usando o Azure AD Connect.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
    * Se necessário, [habilite o Azure AD Connect para sincronização de hash de senha][enable-azure-ad-connect].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Sincronização de hash de senha usando o Azure AD Connect

O Azure AD Connect é usado para sincronizar objetos como grupos e contas e usuário de um ambiente do AD DS local com um locatário do Azure AD. Como parte do processo, a sincronização do hash de senha permite que as contas usem a mesma senha no ambiente do AD DS local e o Azure AD.

Para autenticar os usuários no domínio gerenciado, o Azure AD DS precisa de hashes de senha em um formato adequado para a autenticação NTLM e Kerberos. O Azure AD não armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos, até que você habilite o Azure AD DS para seu locatário. Por motivos de segurança, o Azure AD também não armazena credenciais de senha no formato de texto não criptografado. Portanto, o Azure AD não pode gerar automaticamente essas hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos usuários.

O Azure AD Connect pode ser configurado para sincronizar os hashes de senha NTLM ou Kerberos necessários para Azure AD DS. Lembre-se de concluir as etapas para [habilitar o Azure AD Connect para sincronização de hash de senha][enable-azure-ad-connect]. Se você tiver uma instância existente do Azure AD Connect, [baixe e atualize para a versão mais recente][azure-ad-connect-download] para verificar se poderá sincronizar os hashes de senha herdados do NTLM e Kerberos. Essa funcionalidade não está disponível em versões anteriores do Azure AD Connect ou com a ferramenta DirSync herdada. A versão *1.1.614.0* ou superior do Azure AD Connect é necessária.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes do AD DS locais. Não há suporte para instalar o Azure AD Connect em um domínio gerenciado do Azure AD DS para sincronizar objetos de volta ao Azure AD.

## <a name="enable-synchronization-of-password-hashes"></a>Habilitar sincronização de hashes de senha

Com o Azure AD Connect instalado e configurado para sincronizar com o Azure AD, agora configure a sincronização de hash de senha herdado do NTLM e Kerberos. Um script do PowerShell é usado para configurar as configurações necessárias e, em seguida, iniciar a sincronização de senha completa com o Azure AD. Quando o processo de sincronização de hash de senha do Azure AD Connect for concluído, os usuários poderão entrar nos aplicativos por meio do Azure AD DS que usam os hashes de senha herdados do NTLM ou Kerberos.

1. No computador com o Azure AD Connect instalado, no menu Iniciar, abra o **Azure AD Connect > Serviço de Sincronização**.
1. Selecione a guia **Conectores**. As informações de conexão usadas para estabelecer a sincronização entre o ambiente do AD DS local e o Azure AD são listadas.

    O **Tipo** indica o *Microsoft Azure Active Directory* do conector do Azure AD ou o *Active Directory Domain Services* do conector do AD DS local. Anota os nomes de conector a serem usados no script do PowerShell na próxima etapa.

    ![Listar os nomes de conector no Sync Service Manager](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    Nesta captura de tela de exemplo, os conectores a seguir são usados:

    * O conector do Azure AD é denominado *contoso.onmicrosoft.com – AAD*
    * O conector do AD DS local é denominado *onprem.contoso.com*

1. Copie e cole o seguinte script do PowerShell no computador com o Azure AD Connect instalado. O script dispara uma sincronização de senha completa que inclui hashes de senha herdados. Atualize as variáveis `$azureadConnector` e `$adConnector` com os nomes de conector da etapa anterior.

    Execute este script em cada floresta do AD para sincronizar os hashes de senha do NTLM e Kerberos de conta local com o Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    Dependendo do tamanho do seu diretório em termos do número de contas e grupos, a sincronização de hashes de senha herdados com o Azure AD pode levar algum tempo. As senhas são sincronizadas com o domínio gerenciado depois de serem sincronizadas com o Azure AD.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]
> * Por que são necessários hashes de senha herdada do Kerberos e NTLM
> * Como configurar a sincronização de hash de senha herdada para o Azure AD Connect

> [!div class="nextstepaction"]
> [Saiba como a sincronização funciona em um domínio gerenciado do Azure AD Domain Services](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
