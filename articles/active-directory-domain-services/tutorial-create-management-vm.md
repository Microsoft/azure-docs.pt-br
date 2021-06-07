---
title: Tutorial – Criar uma VM de gerenciamento para o Azure Active Directory Domain Services | Microsoft Docs
description: Neste tutorial, você aprenderá a criar e configurar uma máquina virtual do Windows usada para administrar o domínio gerenciado do Azure Active Directory Domain Services.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d017fc3fc53c9f89978893670df10ecd731da904
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618171"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Criar uma VM de gerenciamento para configurar e administrar um domínio gerenciado do Azure Active Directory Domain Services

O Azure AD DS (Azure Active Directory Domain Services) fornece serviços de domínio gerenciado, como ingresso no domínio, política de grupo, LDAP e autenticação Kerberos/NTLM, que são totalmente compatíveis com o Active Directory do Windows Server. Você administra esse domínio gerenciado usando as mesmas RSAT (Ferramentas de Administração de Servidor Remoto) que um domínio local do Active Directory Domain Services. Como o Azure AD DS é um serviço gerenciado, há algumas tarefas administrativas que você não pode executar, como usar o protocolo RDP para se conectar aos controladores de domínio.

Este tutorial mostra como configurar uma VM do Windows Server no Azure e instalar as ferramentas necessárias para administrar um domínio gerenciado do Azure AD DS.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Entender as tarefas administrativas disponíveis em um domínio gerenciado
> * Instalar as ferramentas administrativas do Active Directory em uma VM do Windows Server
> * Usar o Centro Administrativo do Active Directory para executar tarefas comuns

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, veja o primeiro tutorial para [criar e configurar um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma VM do Windows Server que está unida ao domínio gerenciado.
    * Se necessário, confira o tutorial anterior para [criar uma VM do Windows Server e ingressá-la em um domínio gerenciado][create-join-windows-vm].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.
* Um host do Azure Bastion implantado na rede virtual do Azure AD DS.
    * Se necessário, [crie um host do Azure Bastion][azure-bastion].

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste tutorial, você criará e configurará uma VM de gerenciamento usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Tarefas administrativas disponíveis no Azure AD DS

O Azure AD DS fornece um domínio gerenciado para os usuários, aplicativos e serviços a serem consumidos. Essa abordagem altera algumas das tarefas de gerenciamento disponíveis que você pode fazer e quais privilégios você tem dentro do domínio gerenciado. Essas tarefas e permissões podem ser diferentes daquelas que você tem em um ambiente regular local do Active Directory Domain Services. Você também não pode se conectar a controladores de domínio do domínio gerenciado usando a Área de Trabalho Remota.

### <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tarefas administrativas que você pode executar em um domínio gerenciado

Membros do grupo *Administradores do AAD DC* recebem privilégios no domínio gerenciado que permitem executar tarefas como:

* Configurar o GPO (objeto de política de grupo) interno para os contêineres *Computadores do AADDC* e *Usuários do AADDC* no domínio gerenciado.
* Administre o DNS no domínio gerenciado.
* Criar e administrar personalizado UOs (unidades organizacionais) personalizadas no domínio gerenciado.
* Obter acesso administrativo a computadores ingressados no domínio gerenciado.

### <a name="administrative-privileges-you-dont-have-on-a-managed-domain"></a>Privilégios administrativos que você não tem em um domínio gerenciado

O domínio gerenciado está bloqueado e, portanto, você não tem privilégios para executar determinadas tarefas administrativas no domínio. Alguns dos exemplos a seguir são tarefas que você não pode fazer:

* Estender o esquema do domínio gerenciado.
* Conectar-se a controladores de domínio do domínio gerenciado usando a Área de Trabalho Remota.
* Adicionar controladores de domínio ao domínio gerenciado.
* Você não tem privilégios de *Administrador de Domínio* ou de *Administrador Corporativo* para o domínio gerenciado.

## <a name="sign-in-to-the-windows-server-vm"></a>Entrar na VM do Windows Server

No tutorial anterior, uma VM do Windows Server foi criada e unida ao domínio gerenciado. Use essa VM para instalar as ferramentas de gerenciamento. Se necessário, [siga as etapas do tutorial para criar e unir uma VM do Windows Server a um domínio gerenciado][create-join-windows-vm].

> [!NOTE]
> Neste tutorial, você usa uma VM do Windows Server no Azure que está unida ao domínio gerenciado. Você também poderá usar um cliente do Windows, como o Windows 10, que estiver unido ao domínio gerenciado.
>
> Para obter mais informações sobre como instalar as ferramentas administrativas em um cliente do Windows, confira [Instalar RSAT (Ferramentas de Administração de Servidor Remoto)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Para começar, conecte-se à VM do Windows Server, conforme a seguir:

1. No portal do Azure, selecione **Grupos de recursos** do lado esquerdo. Escolha o grupo de recursos em que a VM foi criada, como *myResourceGroup* e, em seguida, selecione a VM, como *myVM*.
1. No painel **Visão geral** da VM, selecione **Conectar** e, em seguida **Bastion**.

    ![Conectar-se à uma máquina virtual do Windows usando o Bastion no portal do Azure](./media/join-windows-vm/connect-to-vm.png)

1. Insira as credenciais da VM e, em seguida, selecione **Conectar**.

   ![Conectar-se por meio do host do Bastion no portal do Azure](./media/join-windows-vm/connect-to-bastion.png)

Se necessário, permita que o navegador da Web abra pop-ups para que a conexão do Bastion seja exibida. São necessários alguns segundos para estabelecer a conexão com a VM.

## <a name="install-active-directory-administrative-tools"></a>Instale as ferramentas administrativas do Active Directory

Use as mesmas ferramentas administrativas em um domínio gerenciado como ambientes locais do AD DS, como o ADAC (Centro Administrativo do Active Directory) ou o AD PowerShell. Essas ferramentas podem ser instaladas como parte do recurso RSAT (Ferramentas de Administração de Servidor Remoto) no Windows Server e em computadores cliente. Então, os membros do grupo *Administradores do AAD DC* podem administrar domínios gerenciados remotamente usando essas ferramentas administrativas do AD por meio de um computador unido ao domínio gerenciado.

Para instalar as Ferramentas de Administração do Active Directory em uma máquina virtual unida ao domínio, conclua as seguintes etapas:

1. Se **Gerenciador do Servidor** não abrir por padrão quando você entrar na VM, selecione o menu **Iniciar** e, em seguida, escolha **Gerenciador do Servidor**.
1. No painel *Dashboard* da janela **Gerenciador do Servidor**, selecione **Adicionar Funções e Recursos**.
1. Na página **Antes de Você Começar** do *Assistente de Adição de Funções e Recursos*, selecione **Avançar**.
1. Para o *Tipo de Instalação*, deixe a opção **Instalação baseada em função ou recurso** marcada e selecione **Avançar**.
1. Na página **Seleção de Servidor**, escolha a VM atual no pool de servidores, como *myvm.aaddscontoso.com* e, em seguida, selecione **Avançar**.
1. Na página **Funções do Servidor**, clique em **Avançar**.
1. Na página **Recursos**, expanda o nó **Ferramentas de Administração de Servidor Remoto** e, em seguida, expanda o nó **Ferramentas de Administração de Funções**.

    Escolha o recurso **Ferramentas do AD DS e do AD LDS** na lista de ferramentas de administração de funções e, em seguida, selecione **Avançar**.

    ![Instale as 'Ferramentas do AD DS e do AD LDS' da página Recursos](./media/tutorial-create-management-vm/install-features.png)

1. Na página **Confirmação**, selecione **Instalar**. A instalação das ferramentas administrativas pode levar um ou dois minutos.
1. Quando a instalação do recurso for concluída, selecione **Fechar** para sair do **Assistente de Adição de Funções e Recursos**.

## <a name="use-active-directory-administrative-tools"></a>Use as ferramentas administrativas do Active Directory

Com as ferramentas administrativas instaladas, vejamos como é possível usá-las para administrar o domínio gerenciado. Verifique se você está conectado à VM com uma conta de usuário que seja membro do grupo de *Administradores do AAD DC*.

1. No menu **Iniciar**, selecione **Ferramentas Administrativas do Windows**. As ferramentas administrativas do AD instaladas na etapa anterior são listadas.

    ![Lista de Ferramentas Administrativas instaladas no servidor](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Selecione **Centro Administrativo do Active Directory**.
1. Para explorar o domínio gerenciado, escolha o nome de domínio no painel esquerdo, como *aaddscontoso*. Dois contêineres denominados *Computadores do AADDC* e *Usuário do AADDC* estão na parte superior da lista.

    ![Listar os contêineres disponíveis do domínio gerenciado](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Para ver os usuários e grupos que pertencem ao domínio gerenciado, selecione o contêiner **Usuários do AADDC**. As contas e os grupos de usuário do locatário do Azure AD são listados neste contêiner.

    Na saída de exemplo a seguir, uma conta de usuário chamada *Contoso Admin* e um grupo para *Administradores do AAD DC* são mostrados neste contêiner.

    ![Exiba a lista de usuários de domínio do Azure AD DS no Centro Administrativo do Active Directory](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Para ver os computadores que estão unidos ao domínio gerenciado, selecione o contêiner **Computadores do AADDC**. Uma entrada para a máquina virtual atual, como *myVM*, é listada. As contas de computador de todos os dispositivos que ingressaram no domínio gerenciado serão armazenadas no contêiner *Computadores do AADDC*.

Ações comuns do Centro Administrativo do Active Directory – como a redefinição de senha de uma conta de usuário ou o gerenciamento da associação a grupos – estão disponíveis. Essas ações só funcionam para usuários e grupos criados diretamente no domínio gerenciado. As informações de identidade só são sincronizadas *do* Azure AD para o Azure AD DS. Não há nenhum write-back do Azure AD DS para o Azure AD. Você não pode alterar as senhas nem a associação de grupo gerenciado para usuários sincronizados do Azure AD e fazer com que essas alterações sejam sincronizadas de volta.

Você também pode usar o *Módulo do Active Directory para Windows PowerShell*, instalado como parte das ferramentas administrativas, para gerenciar ações comuns em seu domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Entender as tarefas administrativas disponíveis em um domínio gerenciado
> * Instalar as ferramentas administrativas do Active Directory em uma VM do Windows Server
> * Usar o Centro Administrativo do Active Directory para executar tarefas comuns

Para interagir de maneira segura com o domínio gerenciado por meio de outros aplicativos, habilite o protocolo LDAPS.

> [!div class="nextstepaction"]
> [Configurar o LDAP seguro para um domínio gerenciado](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md