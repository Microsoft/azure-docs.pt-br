---
title: 'Azure Active Directory Domain Services: Administrar um domínio gerenciado | Microsoft Docs'
description: Administrar domínios gerenciados dos Serviços de Domínio do Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: iainfou
ms.openlocfilehash: 4095161cd35ff610b357196c91e71256a8bc0263
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473020"
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrar um domínio gerenciado dos Serviços de Domínio do Azure Active Directory
Este artigo mostra como administrar um domínio gerenciado dos Serviços de Domínio do Azure Active Directory (AD).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para concluir as tarefas listadas neste artigo, será necessário ter:

1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](create-instance.md).
4. Uma **máquina virtual ingressada no domínio** por meio da qual você administra o domínio gerenciado dos Serviços de Domínio do Azure AD. Se você não tiver esse tipo de máquina virtual, execute todas as tarefas descritas no artigo [Ingressar uma máquina virtual do Windows em um domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md).
5. Você precisará das credenciais de uma **conta de usuário que pertença ao grupo 'Administradores do controlador de domínio do AAD'** em seu diretório para administrar seu domínio gerenciado.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tarefas administrativas que você pode executar em um domínio gerenciado
Membros do grupo 'Administradores do AAD DC' recebem privilégios no domínio gerenciado que permitem executar tarefas como:

* Ingressar máquinas no domínio gerenciado.
* Configure o GPO interno para os contêineres 'Computadores AADDC' e 'Usuários AADDC' no domínio gerenciado.
* Administre o DNS no domínio gerenciado.
* Crie e administre personalizado UOs (Unidades Organizacionais) personalizadas no domínio gerenciado.
* Obter acesso administrativo a computadores ingressados no domínio gerenciado.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Privilégios administrativos que você não tem em um domínio gerenciado
O domínio é gerenciado pela Microsoft, incluindo atividades como aplicação de patches, monitoramento e realização de backups. O domínio está bloqueado e você não tem privilégios para executar determinadas tarefas administrativas no domínio. Veja abaixo alguns exemplos de tarefas que você não pode executar.

* Você não tem privilégios de Administrador Corporativo ou de Administrador de Domínio para o domínio gerenciado.
* Você não pode estender o esquema do domínio gerenciado.
* Você não pode se conectar a controladores de domínio do domínio gerenciado usando a Área de Trabalho Remota.
* Não é possível adicionar controladores de domínio ao domínio gerenciado.

## <a name="task-1---create-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Tarefa 1: Criar uma máquina virtual do Windows Server ingressada no domínio para administrar remotamente o domínio gerenciado
Domínios gerenciados dos Serviços de Domínio do Azure AD podem ser gerenciados usando as ferramentas administrativas familiares do Active Directory, como o ADAC (Centro Administrativo do Active Directory) ou o AD PowerShell. Os administradores de locatários não têm privilégios para conectar-se a controladores de domínio no domínio gerenciado por meio da Área de Trabalho Remota. Os membros do grupo 'Administradores do AAD DC' podem administrar domínios gerenciados remotamente usando as ferramentas administrativas do AD de um computador cliente do Windows/Windows Server que tenha ingressado no domínio gerenciado. As ferramentas administrativas do AD podem ser instaladas como parte do recurso opcional RSAT (Ferramentas de Administração de Servidor Remoto) no Windows Server e computadores clientes que ingressaram no domínio gerenciado.

A primeira etapa é configurar uma máquina virtual do Windows Server que tenha ingressado no domínio gerenciado. Para obter instruções, confira o artigo intitulado [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Administrar remotamente o domínio gerenciado de um computador cliente (por exemplo, Windows 10)
As instruções neste artigo usam uma máquina virtual do Windows Server para administrar o domínio gerenciado do AAD-DS. No entanto, você também pode optar por usar uma máquina virtual do cliente Windows (por exemplo, o Windows 10) para fazer isso.

Você pode [instalar as Ferramentas de Administração de Servidor Remoto (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) em uma máquina virtual cliente do Windows seguindo as instruções no TechNet.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Tarefa 2: instalar ferramentas de administração do Active Directory na máquina virtual
Execute as etapas a seguir para instalar as Ferramentas de administração do Active Directory na máquina virtual ingressada no domínio. Consulte o Technet para obter mais [informações sobre como instalar e usar as Ferramentas de Administração de Servidor Remoto](https://technet.microsoft.com/library/hh831501.aspx).

1. Navegue até o Portal do Azure. Clique em **Todos os recursos** no painel esquerdo. Localize e clique na máquina virtual criada na Tarefa 1.
2. Clique no botão **Conectar** na guia Visão Geral. Um arquivo do Protocolo RDP (.rdp) é criado e baixado.

    ![Conectar-se à máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Para se conectar à sua VM, abra o arquivo RDP baixado. Se solicitado, clique em **Conectar**. Use as credenciais de um usuário pertencente ao grupo "Administradores do AAD DC". Por exemplo: "bob@domainservicespreview.onmicrosoft.com". Você pode receber um aviso do certificado durante o processo de logon. Clique em Sim ou em Continuar para prosseguir com a conexão.
4. Na tela inicial, abra **Gerenciador do Servidor**. Clique em **Adicionar Funções e Recursos** no painel central da janela do Gerenciador do Servidor.

    ![Iniciar o Gerenciador do Servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na página **Antes de Começar** do **Assistente de Adição de Funções e Recursos**, clique em **Avançar**.

    ![Página Antes de Começar](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na página **Tipo de Instalação**, deixe a opção **Instalação baseada em função ou recurso** marcada e clique em **Avançar**.

    ![Página Tipo de Instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na página **Seleção de Servidor**, escolha a máquina virtual atual no pool de servidores e clique em **Avançar**.

    ![Página Seleção de Servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na página **Funções do Servidor**, clique em **Avançar**.
9. Na página **Recursos**, clique para expandir o nó **Ferramentas de Administração de Servidor Remoto** e, em seguida, clique para expandir o nó **Ferramentas de Administração de Funções**. Selecione o recurso **Ferramentas do AD LDS e AD DS** da lista de ferramentas de administração de funções.

    ![Página Recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. Na página **Confirmação**, clique em **Instalar** para instalar o AD e o recurso Ferramentas do AD LDS na máquina virtual. Quando a instalação do recurso for concluída com êxito, clique em **Fechar** para sair do **Assistente de Adição de Funções e Recursos**.

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Tarefa 3: conectar e explorar o domínio gerenciado
Agora, você pode usar as ferramentas administrativas do Windows Server AD para explorar e administrar o domínio gerenciado.

> [!NOTE]
> Você precisará ser um membro do grupo "Administradores do controlador de domínio do AAD" para administrar o domínio gerenciado.
>
>

1. Na tela inicial, clique em **Ferramentas Administrativas**. Você deve ver as ferramentas administrativas do AD instaladas na máquina virtual.

    ![Ferramentas Administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Clique em **Centro Administrativo do Active Directory**.

    ![Centro Administrativo do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Para explorar o domínio, clique no nome de domínio no painel à esquerda (por exemplo, ' contoso100.com'). Observe os dois contêineres chamados 'Computadores do AADDC' e 'Usuários do AADDC', respectivamente.

    ![ADAC - exibir domínio](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Clique no contêiner chamado **Usuários do AADDC** para ver todos os usuários e grupos que pertencem ao domínio gerenciado. Você deve ver as contas de usuário e grupos do seu locatário do Azure AD exibidas neste contêiner. Observe que neste exemplo, uma conta de usuário para o usuário 'bob' e um grupo chamado 'Administradores do AAD DC' estão disponíveis neste contêiner.

    ![ADAC - usuários de domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Clique no contêiner chamado **Computadores do AADDC** para ver os computadores associados a esse domínio gerenciado. Você deve ver uma entrada para a máquina virtual atual, que é ingressada no domínio. Contas de computador para todos os computadores que ingressaram em um domínio gerenciado aparecerá dos Serviços de Domínio do Azure AD são armazenados nesse contêiner 'Computadores do AADDC'.

    ![ADAC - computadores ingressados no domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - Guia de Introdução](create-instance.md)
* [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Implantar as Ferramentas de Administração de Servidor Remoto](https://technet.microsoft.com/library/hh831501.aspx)
