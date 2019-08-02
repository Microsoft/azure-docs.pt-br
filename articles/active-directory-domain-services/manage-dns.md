---
title: Gerenciar o DNS para serviços de domínio do AD do Azure | Microsoft Docs
description: Gerenciar o DNS para o Azure AD Domain Services
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
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 6753c26a99bb38e92613a6bad753e7dd101ba68e
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473135"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrar o DNS em um domínio gerenciado dos Serviços de Domínio do Azure AD
Os Serviços de Domínio do Azure Active Directory incluem um servidor DNS (resolução de nomes de domínio) que fornece a resolução DNS para o domínio gerenciado. Ocasionalmente, talvez seja necessário configurar o DNS no domínio gerenciado. Talvez seja necessário criar registros DNS para computadores que não ingressaram no domínio, configurar endereços IP virtuais para balanceadores de carga ou configurar encaminhadores DNS externos. Por esse motivo, os usuários que pertencem ao grupo 'Administradores do AAD DC' recebem privilégios para administração de DNS no domínio gerenciado.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para concluir as tarefas listadas neste artigo, será necessário ter:

1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](create-instance.md).
4. Uma **máquina virtual ingressada no domínio** por meio da qual você administra o domínio gerenciado dos Serviços de Domínio do Azure AD. Se você não tiver esse tipo de máquina virtual, execute todas as tarefas descritas no artigo [Ingressar uma máquina virtual do Windows em um domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md).
5. Você precisa das credenciais de um **conta de usuário que pertencem ao grupo “Administradores do controlador de domínio do AAD”** em seu diretório para administrar o DNS para seu domínio gerenciado.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Tarefa 1 - Criar uma máquina virtual ingressada no domínio para administrar o DNS remotamente para o domínio gerenciado
Domínios gerenciados dos Serviços de Domínio do Azure AD podem ser gerenciados remotamente usando as ferramentas administrativas familiares do Active Directory, como o ADAC (Centro Administrativo do Active Directory) ou o AD PowerShell. Do mesmo modo, o DNS para o domínio gerenciado pode ser administrado remotamente usando as Ferramentas de administração do servidor DNS.

Os administradores em seu diretório do de Azure AD não têm privilégios para se conectar a controladores de domínio no domínio gerenciado por meio da Área de Trabalho Remota. Os membros do grupo 'Administradores do AAD DC' podem administrar DNS remotamente para domínios gerenciados usando as ferramentas do servidor DNS de um computador cliente do Windows/Windows Server que tenha ingressado no domínio gerenciado. As ferramentas do Servidor DNS fazem parte do recurso opção das Ferramentas de Administração do Servidor Remoto (RSAT)

A primeira etapa é criar uma máquina virtual do Windows Server que tenha ingressado no domínio gerenciado. Para obter instruções, confira o artigo intitulado [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Tarefa 2 - Instalar as ferramentas de Servidor DNS na máquina virtual
Completar as etapas a seguir para instalar as Ferramentas de administração de DNS na máquina virtual ingressada no domínio. Para obter mais informações sobre como [instalar e usar Ferramentas de Administração de Servidor Remoto](https://technet.microsoft.com/library/hh831501.aspx), consulte o Technet.

1. Navegue até o Portal do Azure. Clique em **Todos os recursos** no painel esquerdo. Localize e clique na máquina virtual criada na Tarefa 1.
2. Clique no botão **Conectar** na guia Visão Geral. Um arquivo do Protocolo RDP (.rdp) é criado e baixado.

    ![Conectar-se à máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Para se conectar à sua VM, abra o arquivo RDP baixado. Se solicitado, clique em **Conectar**. Use as credenciais de um usuário pertencente ao grupo "Administradores do AAD DC". Por exemplo, 'bob@domainservicespreview.onmicrosoft.com'. Você pode receber um aviso do certificado durante o processo de logon. Clique em Sim ou Continuar para se conectar.

4. Na tela inicial, abra **Gerenciador do Servidor**. Clique em **Adicionar Funções e Recursos** no painel central da janela do Gerenciador do Servidor.

    ![Iniciar o Gerenciador do Servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na página **Antes de Começar** do **Assistente de Adição de Funções e Recursos**, clique em **Avançar**.

    ![Página Antes de Começar](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na página **Tipo de Instalação**, deixe a opção **Instalação baseada em função ou recurso** marcada e clique em **Avançar**.

    ![Página Tipo de Instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na página **Seleção de Servidor**, escolha a máquina virtual atual no pool de servidores e clique em **Avançar**.

    ![Página Seleção de Servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na página **Funções do Servidor**, clique em **Avançar**.
9. Na página **Recursos**, clique para expandir o nó **Ferramentas de Administração de Servidor Remoto** e, em seguida, clique para expandir o nó **Ferramentas de Administração de Funções**. Selecione o recurso **Ferramentas do Servidor DNS** na lista de ferramentas de administração de funções.

    ![Página Recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. Na página **Confirmação**, clique em **Instalar** para instalar o recurso de ferramentas do Servidor DNS na máquina virtual. Quando a instalação do recurso for concluída com êxito, clique em **Fechar** para sair do **Assistente de Adição de Funções e Recursos**.

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Tarefa 3 - Iniciar o console de gerenciamento do DNS para administrar o DNS
Agora, você pode usar ferramentas de DNS do Windows Server para administrar o DNS no domínio gerenciado.

> [!NOTE]
> Você precisa ser um membro do grupo “Administradores do controlador de domínio do AAD” para administrar o DNS no domínio gerenciado.
>
>

1. Na tela inicial, clique em **Ferramentas Administrativas**. Você deverá ver o console **DNS** instalado na máquina virtual.

    ![Ferramentas administrativas - Console DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Clique em **DNS** para iniciar o console de Gerenciamento de DNS.
3. No diálogo **Conectar-se ao Servidor DNS**, clique em **O computador seguinte** e insira o nome de domínio DNS do domínio gerenciado (por exemplo, 'contoso100.com').

    ![Console DNS - conectar-se ao domínio](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. Conecta o Console DNS ao domínio gerenciado.

    ![Console DNS - administrar domínio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Agora você pode usar o console DNS para adicionar entradas DNS a computadores na rede virtual na qual você habilitou os Serviços de Domínio do AAD.

> [!WARNING]
> Seja cauteloso ao administrar o DNS para o domínio gerenciado usando as ferramentas de administração de DNS. Certifique-se de **não excluir nem modificar os registros DNS internos usados pelos Serviços de Domínio no domínio em questão**. Os registros DNS internos incluem registros DNS do domínio, registros de servidor de nome e outros registros usados para a localização do controlador de domínio. Se você modificar esses registros, os serviços de domínio serão interrompidos na rede virtual.
>
>

Para obter mais informações sobre como gerenciar o DNS, veja o [artigo sobre ferramentas de DNS no TechNet](https://technet.microsoft.com/library/cc753579.aspx).

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - Guia de Introdução](create-instance.md)
* [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Gerenciar um domínio do Azure AD Domain Services](manage-domain.md)
* [Ferramentas de administração do DNS](https://technet.microsoft.com/library/cc753579.aspx)
