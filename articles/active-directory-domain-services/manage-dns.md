---
title: Gerenciar o DNS para Azure AD Domain Services | Microsoft Docs
description: Saiba como instalar as ferramentas do servidor DNS para gerenciar o DNS e criar encaminhadores condicionais para um Azure Active Directory Domain Services domínio gerenciado.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: afa6920a36a5a7218571239b36815004d8f2d450
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619344"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>Administrar o DNS e criar encaminhadores condicionais em um Azure Active Directory Domain Services domínio gerenciado

No Azure Active Directory Domain Services (AD DS do Azure), um componente de chave é DNS (resolução de nomes de domínio). O AD DS do Azure inclui um servidor DNS que fornece resolução de nomes para o domínio gerenciado. Esse servidor DNS inclui registros e atualizações de DNS internos para os principais componentes que permitem a execução do serviço.

Ao executar seus próprios aplicativos e serviços, talvez seja necessário criar registros DNS para computadores que não são ingressados no domínio, configurar endereços IP virtuais para balanceadores de carga ou configurar encaminhadores de DNS externos. Os usuários que pertencem ao grupo de *Administradores de DC do AAD* recebem privilégios de administração de DNS no domínio gerenciado AD DS do Azure e podem criar e editar registros DNS personalizados.

Em um ambiente híbrido, as zonas e os registros DNS configurados em outros namespaces DNS, como um ambiente de AD DS local, não são sincronizados com o domínio gerenciado. Para resolver os recursos nomeados em outros namespaces DNS, crie e use encaminhadores condicionais que apontam para servidores DNS existentes em seu ambiente.

Este artigo mostra como instalar as ferramentas de servidor DNS e usar o console DNS para gerenciar registros e criar encaminhadores condicionais no Azure AD DS.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar um Azure Active Directory Domain Services domínio gerenciado][create-azure-ad-ds-instance].
* Conectividade do seu Azure AD DS rede virtual para onde os outros namespaces DNS estão hospedados.
    * Essa conectividade pode ser fornecida com uma conexão de [Gateway de VPN][vpn-gateway] do Azure ou [Azure ExpressRoute][expressroute] .
* Uma VM de gerenciamento do Windows Server que é unida ao domínio gerenciado.
    * Se necessário, conclua o tutorial para [criar uma VM do Windows Server e associá-la a um domínio gerenciado][create-join-windows-vm].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

## <a name="install-dns-server-tools"></a>Instalar ferramentas de servidor DNS

Para criar e modificar os registros DNS em um domínio gerenciado, você precisa instalar as ferramentas do servidor DNS. Essas ferramentas podem ser instaladas como um recurso no Windows Server. Para obter mais informações sobre como instalar as ferramentas administrativas em um cliente Windows, consulte instalar o [ferramentas de administração de servidor remoto (RSAT)][install-rsat].

1. Entre na sua VM de gerenciamento. Para obter as etapas sobre como se conectar usando o portal do Azure, consulte [conectar-se a uma VM do Windows Server][connect-windows-server-vm].
1. Se **Gerenciador do Servidor** não abrir por padrão quando você entrar na VM, selecione o menu **Iniciar** e, em seguida, escolha **Gerenciador do Servidor**.
1. No painel *Dashboard* da janela **Gerenciador do Servidor**, selecione **Adicionar Funções e Recursos**.
1. Na página **Antes de Você Começar** do *Assistente de Adição de Funções e Recursos*, selecione **Avançar**.
1. Para o *Tipo de Instalação*, deixe a opção **Instalação baseada em função ou recurso** marcada e selecione **Avançar**.
1. Na página **Seleção de Servidor**, escolha a VM atual no pool de servidores, como *myvm.aaddscontoso.com* e, em seguida, selecione **Avançar**.
1. Na página **Funções do Servidor**, clique em **Avançar**.
1. Na página **Recursos**, expanda o nó **Ferramentas de Administração de Servidor Remoto** e, em seguida, expanda o nó **Ferramentas de Administração de Funções**. Selecione o recurso **Ferramentas do Servidor DNS** na lista de ferramentas de administração de funções.

    ![Opte por instalar as ferramentas do servidor DNS na lista de ferramentas de administração de função disponíveis](./media/manage-dns/install-dns-tools.png)

1. Na página **Confirmação**, selecione **Instalar**. Pode levar um minuto ou dois para instalar as ferramentas do servidor DNS.
1. Quando a instalação do recurso for concluída, selecione **Fechar** para sair do **Assistente de Adição de Funções e Recursos**.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Abra o console de gerenciamento do DNS para administrar o DNS

Com as ferramentas do servidor DNS instaladas, você pode administrar os registros DNS no domínio gerenciado.

> [!NOTE]
> Para administrar o DNS em um domínio gerenciado, você deve estar conectado a uma conta de usuário que seja membro do grupo de *Administradores de DC do AAD* .

1. Na tela iniciar, selecione **Ferramentas administrativas**. Uma lista de ferramentas de gerenciamento disponíveis é mostrada, incluindo o **DNS** instalado na seção anterior. Selecione **DNS** para iniciar o console de gerenciamento do DNS.
1. Na caixa de diálogo **conectar ao servidor DNS** , selecione **o seguinte computador** e, em seguida, insira o nome de domínio DNS do domínio gerenciado, como *aaddscontoso.com*:

    ![Conectar-se ao domínio gerenciado no console DNS](./media/manage-dns/connect-dns-server.png)

1. O console DNS se conecta ao domínio gerenciado especificado. Expanda as **zonas de pesquisa direta** ou as **zonas de pesquisa inversa** para criar suas entradas DNS necessárias ou editar os registros existentes, conforme necessário.

    ![Console DNS - administrar domínio](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Ao gerenciar registros usando as ferramentas do servidor DNS, certifique-se de não excluir ou modificar os registros DNS internos que são usados pelo Azure AD DS. Os registros DNS internos incluem registros DNS do domínio, registros de servidor de nome e outros registros usados para a localização do controlador de domínio. Se você modificar esses registros, os serviços de domínio serão interrompidos na rede virtual.

## <a name="create-conditional-forwarders"></a>Criar encaminhadores condicionais

Uma zona DNS AD DS do Azure deve conter apenas a zona e os registros para o próprio domínio gerenciado. Não crie zonas adicionais no domínio gerenciado para resolver recursos nomeados em outros namespaces DNS. Em vez disso, use encaminhadores condicionais no domínio gerenciado para informar ao servidor DNS onde ir para resolver endereços para esses recursos.

Um encaminhador condicional é uma opção de configuração em um servidor DNS que permite definir um domínio DNS, como *contoso.com*, para encaminhar consultas para o. Em vez do servidor DNS local que está tentando resolver consultas para registros nesse domínio, as consultas DNS são encaminhadas para o DNS configurado para esse domínio. Essa configuração garante que os registros DNS corretos sejam retornados, pois você não cria uma zona DNS local com registros duplicados no domínio gerenciado para refletir esses recursos.

Para criar um encaminhador condicional em seu domínio gerenciado, conclua as seguintes etapas:

1. Selecione a zona DNS, como *aaddscontoso.com*.
1. Selecione **encaminhadores condicionais**, em seguida, selecione à direita e escolha **novo encaminhador condicional...**
1. Insira seu outro **domínio DNS**, como *contoso.com*, em seguida, insira os endereços IP dos servidores DNS para esse namespace, conforme mostrado no exemplo a seguir:

    ![Adicionar e configurar um encaminhador condicional para o servidor DNS](./media/manage-dns/create-conditional-forwarder.png)

1. Marque a caixa para **armazenar este encaminhador condicional em Active Directory e replique-** o da seguinte maneira e, em seguida, selecione a opção para *todos os servidores DNS nesse domínio*, conforme mostrado no exemplo a seguir:

    ![Console DNS-selecionar todos os servidores DNS neste domínio](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Se o encaminhador condicional for armazenado na *floresta* em vez do *domínio*, o encaminhador condicional falhará.

1. Para criar o encaminhador condicional, selecione **OK**.

A resolução de nomes dos recursos em outros namespaces de VMs conectadas ao domínio gerenciado agora deve ser resolvida corretamente. As consultas para o domínio DNS configurado no encaminhador condicional são passadas para os servidores DNS relevantes.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como gerenciar o DNS, veja o [artigo sobre ferramentas de DNS no TechNet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11)).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh