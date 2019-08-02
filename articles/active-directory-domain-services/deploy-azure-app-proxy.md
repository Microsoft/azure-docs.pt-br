---
title: 'Azure Active Directory Domain Services: Implantar Proxy de Aplicativo do AD do Azure | Microsoft Docs'
description: Usar o Proxy de Aplicativo do Azure AD nos domínios gerenciados do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: eca421697081310b1bf245172b3ff125e11c8728
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234164"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Implantar o Proxy de Aplicativo do Azure AD em um domínio gerenciado do Azure AD Domain Services
O Proxy de Aplicativo do Active Directory (AD) do Azure o ajuda a dar suporte a funcionários remotos publicando aplicativos locais para serem acessados via Internet. Com o Azure AD Domain Services, agora você pode usar modelo lift-and-shift em aplicativos herdados executados localmente para os Serviços de Infraestrutura do Azure. Depois, publique esses aplicativos usando o Proxy de Aplicativo do Azure AD, a fim de fornecer acesso remoto seguro aos usuários em sua organização.

Se você for um novo usuário do Proxy de Aplicativo do Azure AD, saiba mais sobre esse recurso com o artigo a seguir: [Como fornecer acesso remoto seguro para aplicativos locais](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa do seguinte:

1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. É necessário ter uma **licença Básica ou Premium do Azure AD** para usar o Proxy de Aplicativo do Azure AD.
4. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](create-instance.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Tarefa 1: Habilitar o Proxy de Aplicativo do Azure AD para o diretório do Azure AD
Execute as etapas a seguir para habilitar o Proxy de Aplicativo do Azure AD para seu diretório do Azure AD.

1. Entre como administrador no [Portal do Azure](https://portal.azure.com).

2. Clique em **Azure Active Directory** para exibir a visão geral do diretório. Clique em **Aplicativos empresariais**.

    ![Selecionar um diretório do Azure AD](./media/app-proxy/app-proxy-enable-start.png)
3. Clique em **Proxy de aplicativo**. Se você não tiver uma assinatura Básica ou Premium do Azure AD, verá uma opção para habilitar uma versão de avaliação. Alterne **Habilitar Proxy de Aplicativo?** para **Habilitar** e clique em **Salvar**.

    ![Habilitar Proxy de Aplicativo](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Para baixar o conector, clique no botão **Conector**.

    ![Baixe o conector](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Na página de download, aceite os termos de licença e o contrato de privacidade e clique no botão **Baixar**.

    ![Confirme o download](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Tarefa 2: Provisionar servidores Windows ingressados em domínio para implantar o conector do Proxy de Aplicativo do Azure AD
Você precisa de máquinas virtuais com Windows Server ingressadas em domínio nas quais pode instalar o conector do Proxy de Aplicativo do Azure AD. Para alguns aplicativos, você pode optar por provisionar vários servidores nos quais o conector está instalado. Essa opção de implantação fornece mais disponibilidade e ajuda a lidar com cargas mais pesadas de autenticação.

Provisione os servidores do conector na mesma rede virtual (ou uma rede virtual conectada/emparelhada), na qual você habilitou seu domínio gerenciado do Azure AD Domain Services. Da mesma forma, os servidores que hospedam os aplicativos publicados por meio do Proxy de Aplicativo precisam ser instalados na mesma rede virtual do Azure.

Para provisionar os servidores do conector, execute as tarefas descritas no artigo [Ingressar uma máquina virtual do Windows em um domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Tarefa 3: Instalar e registrar o conector do Proxy de Aplicativo do Azure AD
Antes, você provisionou uma máquina virtual com Windows Server e ingressou no domínio gerenciado. Nesta tarefa, você instalará o conector do Proxy de Aplicativo do Azure AD nesta máquina virtual.

1. Copie o pacote de instalação do conector na VM na qual você instalou o conector do Proxy de Aplicativo Web do Azure AD.

2. Execute **AADApplicationProxyConnectorInstaller.exe** na máquina virtual. Aceite os termos de licença de software.

    ![Aceite os termos de instalação](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Durante a instalação, você receberá uma solicitação para registrar o conector com o Proxy de Aplicativo de seu locatário do Azure AD.
   * Forneça suas **credenciais de administrador global do Azure AD**. Seu locatário de administrador global pode ser diferente das suas credenciais do Microsoft Azure.
   * A conta de administrador usada para registrar o conector deve pertencer ao mesmo diretório no qual você habilitou o serviço Proxy de Aplicativo. Por exemplo, se o domínio de locatário for contoso.com, o administrador deverá ser admin@contoso.com ou qualquer outro alias válido nesse domínio.
   * Se a Configuração de Segurança Aprimorada do Internet Explorer estiver ativada no servidor em que você estiver instalando o conector, a tela de registro poderá ser bloqueada. Siga as instruções na mensagem de erro para permitir o acesso. Certifique-se de que a Segurança Melhorada do Internet Explorer está desativada.
   * Se o registro do conector não for bem-sucedido, confira [Solucionar problemas de Proxy de Aplicativo](../active-directory/manage-apps/application-proxy-troubleshoot.md).

     ![Conector instalado](./media/app-proxy/app-proxy-connector-installed.png)
4. Para garantir o funcionamento correto do conector, execute a Solução de Problemas do Conector do Proxy de Aplicativo do Azure AD. O resultado da execução da solução de problemas deve ser um relatório de êxito.

    ![Êxito da solução de problemas](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Você deverá ver o conector recém-instalado listado na página do Proxy de aplicativo em seu diretório do Azure AD.

    ![No portal do Azure, o conector instalado é exibido como disponível](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Escolha instalar os conectores em vários servidores para garantir a alta disponibilidade aos aplicativos de autenticação publicados por meio do Proxy de Aplicativo do Azure AD. Execute as mesmas etapas listadas acima para instalar o conector em outros servidores ingressados em seu domínio gerenciado.
>
>

## <a name="next-steps"></a>Próximas etapas
Você configurou o Proxy de Aplicativo do Azure AD e o integrou a seu domínio gerenciado do Azure AD Domain Services.

* **Migrar seus aplicativos para máquinas virtuais do Azure:** Use lift-and-shift para migrar os aplicativos de servidores locais para máquinas virtuais do Azure ingressadas em seu domínio gerenciado. Isso ajuda você a se livrar dos custos com infraestrutura gerados pela execução de servidores localmente.

* **Publicar aplicativos usando o Proxy de Aplicativo do Azure AD:** Publique aplicativos em execução nas máquinas virtuais do Azure usando o Proxy de Aplicativo do Azure AD. Para saber mais, confira [publicar aplicativos usando o Proxy de Aplicativo do Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Nota sobre a implantação: publique aplicativos IWA (Autenticação Integrada do Windows) usando o Proxy de Aplicativo do Azure AD
Habilite o logon único para seus aplicativos usando a IWA (Autenticação Integrada do Windows) concedendo permissão aos Conectores de Proxy de Aplicativo para representar usuários e enviar e receber tokens em seu nome. Configure a KCD (Delegação restrita de Kerberos) para que o conector conceda as permissões necessárias para o acesso de recursos no domínio gerenciado. Use o mecanismo KCD baseado em recursos em domínios gerenciados para aumentar a segurança.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Habilitar a delegação restrita de Kerberos com base em recursos para o conector de Proxy de Aplicativo do Azure AD
O conector do Proxy de Aplicativo do Azure deve ser configurado para KCD (delegação restrita de Kerberos), para que possa representar usuários no domínio gerenciado. Em um domínio gerenciado do Azure AD Domain Services, você não tem privilégios de administrador de domínio. Portanto, **não é possível configurar um KCD tradicional no nível da conta em um domínio gerenciado**.

Use o KCD baseado em recursos conforme descrito neste [artigo](deploy-kcd.md).

> [!NOTE]
> Você precisará ser um membro do grupo "Administradores do controlador de domínio do AAD" para administrar o domínio gerenciado usando cmdlets do AD PowerShell.
>
>

Use o cmdlet Get-ADComputer do PowerShell para recuperar as configurações do computador no qual o conector de Proxy de Aplicativo do Azure AD está instalado.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Depois disso, use o cmdlet Set-ADComputer para configurar o KCD baseado em recursos para o servidor do recurso.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Se você tiver implantado vários conectores de Proxy de Aplicativo em seu domínio gerenciado, será necessário configurar o KCD baseado em recursos para cada instância do conector.


## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - guia de Introdução](create-instance.md)
* [Configurar a Delegação Restrita de Kerberos em um domínio gerenciado](deploy-kcd.md)
* [Visão geral da Delegação Restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
