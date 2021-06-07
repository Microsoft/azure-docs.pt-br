---
title: Implantar Proxy de Aplicativo do AD do Azure para Azure AD Domain Services | Microsoft Docs
description: Saiba como fornecer acesso seguro a aplicativos internos para funcionários remotos Implantando e configurando Proxy de Aplicativo do Azure Active Directory em um domínio Azure Active Directory Domain Services gerenciado
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8305b2190cf3b157973f5844c3237ffe73adba66
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619909"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>Implantar Proxy de Aplicativo do AD do Azure para acesso seguro a aplicativos internos em um domínio Azure Active Directory Domain Services gerenciado

Com o Azure AD Domain Services (AD DS do Azure), você pode migrar e deslocar aplicativos herdados em execução localmente no Azure. O proxy de aplicativo Azure Active Directory (AD) ajuda você a dar suporte a trabalhadores remotos, publicando com segurança a parte dos aplicativos internos de um domínio gerenciado do Azure AD DS para que eles possam ser acessados pela Internet.

Se você for novo no Proxy de Aplicativo do AD do Azure e quiser saber mais, consulte [como fornecer acesso remoto seguro a aplicativos internos](../active-directory/manage-apps/application-proxy.md).

Este artigo mostra como criar e configurar um conector de Proxy de Aplicativo do AD do Azure para fornecer acesso seguro a aplicativos em um domínio gerenciado.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
    * Uma **licença de Azure ad Premium** é necessária para usar o proxy de aplicativo do AD do Azure.
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Criar uma VM do Windows ingressada no domínio

Para rotear o tráfego para aplicativos em execução em seu ambiente, instale o componente conector do Azure Proxy de Aplicativo do AD. Esse conector do Azure Proxy de Aplicativo do AD deve ser instalado em uma máquina virtual (VM) do Windows Server que tenha ingressado no domínio gerenciado. Para alguns aplicativos, você pode implantar vários servidores que cada um tem o conector do instalado. Essa opção de implantação fornece mais disponibilidade e ajuda a lidar com cargas mais pesadas de autenticação.

A VM que executa o conector de Proxy de Aplicativo do AD do Azure deve estar no mesmo ou em uma rede virtual emparelhada como seu domínio gerenciado. As VMs que hospedam os aplicativos que você publica usando o proxy de aplicativo também devem ser implantadas na mesma rede virtual do Azure.

Para criar uma VM para o conector de Proxy de Aplicativo do AD do Azure, conclua as seguintes etapas:

1. [Criar uma UO personalizada](create-ou.md). Você pode delegar permissões para gerenciar esta UO personalizada para usuários dentro do domínio gerenciado. As VMs do Azure Proxy de Aplicativo do AD e que executam seus aplicativos devem fazer parte da UO personalizada, não da UO dos *computadores DC do AAD* padrão.
1. [Domínio – ingresse nas máquinas virtuais][create-join-windows-vm], ambas que executam o conector de proxy de aplicativo do AD do Azure e aquelas que executam seus aplicativos, para o domínio gerenciado. Crie essas contas de computador na UO personalizada da etapa anterior.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Baixar o conector de Proxy de Aplicativo do AD do Azure

Execute as etapas a seguir para baixar o conector de Proxy de Aplicativo do AD do Azure. O arquivo de instalação que você baixa é copiado para a VM do proxy de aplicativo na próxima seção.

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de usuário que tenha permissões de *administrador corporativo* no Azure AD.
1. Procure e selecione **Azure Active Directory** na parte superior do portal e, em seguida, escolha **aplicativos empresariais**.
1. Selecione **proxy de aplicativo** no menu do lado esquerdo. Para criar seu primeiro conector e habilitar o proxy de aplicativo, selecione o link para **baixar um conector**.
1. Na página de download, aceite os termos de licença e o contrato de privacidade e, em seguida, selecione **aceitar termos & baixar**.

    ![Baixar o conector de proxy de Aplicativo Azure AD](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Instalar e registrar o conector de Proxy de Aplicativo do AD do Azure

Com uma VM pronta para ser usada como o conector de Proxy de Aplicativo do AD do Azure, agora Copie e execute o arquivo de instalação baixado do portal do Azure.

1. Copie o arquivo de instalação do conector do Azure Proxy de Aplicativo do AD para sua VM.
1. Execute o arquivo de instalação, como *AADApplicationProxyConnectorInstaller.exe*. Aceite os termos de licença de software.
1. Durante a instalação, você será solicitado a registrar o conector com o proxy de aplicativo no seu diretório do Azure AD.
   * Forneça as credenciais para um administrador global em seu diretório do Azure AD. As credenciais de administrador global do Azure AD podem ser diferentes das suas credenciais do Azure no portal

        > [!NOTE]
        > A conta de administrador global usada para registrar o conector deve pertencer ao mesmo diretório em que você habilita o serviço de proxy de aplicativo.
        >
        > Por exemplo, se o domínio do Azure AD for *contoso.com*, o administrador global deverá ser `admin@contoso.com` ou outro alias válido nesse domínio.

   * Se a configuração de segurança reforçada do Internet Explorer estiver ativada para a VM na qual você instala o conector, a tela de registro poderá ser bloqueada. Para permitir o acesso, siga as instruções na mensagem de erro ou desative a segurança aprimorada do Internet Explorer durante o processo de instalação.
   * Se o registro do conector falhar, consulte [solucionar problemas do Application Proxy](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. No final da instalação, uma observação é mostrada para ambientes com um proxy de saída. Para configurar o conector de Proxy de Aplicativo do AD do Azure para trabalhar com o proxy de saída, execute o script fornecido, como `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Na página proxy de aplicativo no portal do Azure, o novo conector é listado com o status *ativo*, conforme mostrado no exemplo a seguir:

    ![O novo conector de Proxy de Aplicativo do AD do Azure mostrado como ativo no portal do Azure](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Para fornecer alta disponibilidade para aplicativos que se autenticam por meio do Proxy de Aplicativo do AD do Azure, você pode instalar conectores em várias VMs. Repita as mesmas etapas listadas na seção anterior para instalar o conector em outros servidores ingressados no domínio gerenciado.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Habilitar a delegação restrita de Kerberos baseada em recursos

Se você quiser usar o logon único para seus aplicativos usando a autenticação integrada do Windows (IWA), Conceda permissão aos conectores do Azure Proxy de Aplicativo do AD para representar usuários e enviar e receber tokens em seu nome. Para conceder essas permissões, configure a delegação restrita de Kerberos (KCD) para que o conector acesse recursos no domínio gerenciado. Como você não tem privilégios de administrador de domínio em um domínio gerenciado, o KCD de nível de conta tradicional não pode ser configurado em um domínio gerenciado. Em vez disso, use KCD com base em recursos.

Para obter mais informações, consulte [Configurar a delegação restrita de Kerberos (KCD) no Azure Active Directory Domain Services](deploy-kcd.md).

> [!NOTE]
> Você deve estar conectado a uma conta de usuário que seja membro do grupo de *Administradores de DC do Azure ad* em seu locatário do Azure ad para executar os seguintes cmdlets do PowerShell.
>
> As contas de computador para sua VM do conector de proxy de aplicativo e VMs de aplicativo devem estar em uma UO personalizada em que você tenha permissões para configurar KCD com base em recursos. Você não pode configurar o KCD baseado em recursos para uma conta de computador no contêiner de *computadores DC do AAD* interno.

Use o [Get-ADComputer][Get-ADComputer] para recuperar as configurações do computador no qual o conector do proxy de aplicativo do AD do Azure está instalado. Em sua VM de gerenciamento ingressado no domínio e conectada como uma conta de usuário que seja membro do grupo de *Administradores de DC do Azure ad* , execute os cmdlets a seguir.

O exemplo a seguir obtém informações sobre a conta de computador chamada *appproxy.aaddscontoso.com*. Forneça seu próprio nome de computador para a VM de Proxy de Aplicativo do AD do Azure configurada nas etapas anteriores.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Para cada servidor de aplicativos que executa os aplicativos por trás do Azure Proxy de Aplicativo do AD use o cmdlet [set-ADComputer][Set-ADComputer] do PowerShell para configurar o KCD baseado em recursos. No exemplo a seguir, o conector de Proxy de Aplicativo do AD do Azure recebe permissões para usar o computador *AppServer.aaddscontoso.com* :

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Se você implantar vários conectores de Proxy de Aplicativo do AD do Azure, deverá configurar o KCD baseado em recursos para cada instância de conector.

## <a name="next-steps"></a>Próximas etapas

Com o Azure Proxy de Aplicativo do AD integrado com o AD DS do Azure, publique aplicativos para que os usuários acessem. Para obter mais informações, consulte [publicar aplicativos usando o Azure proxy de aplicativo do AD](../active-directory/manage-apps/application-proxy-add-on-premises-application.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer