---
title: 'Azure Active Directory Domain Services: Administrar Política de Grupo | Microsoft Docs'
description: Administrar a política de grupo nos domínios gerenciados do Azure Active Directory Domain Services
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
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: c7b32885fdb3cf4f3e584c916d6b234fff54bfc4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234038"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrar a política de grupo em um domínio gerenciado do Azure AD Domain Services
O Azure Active Directory Domain Services inclui GPOs (Objetos de Política de Grupo) internos para os contêineres "Usuários do AADDC" e "Computadores do AADDC". Você pode personalizar esses GPOs internos para configurar a Política de Grupo no domínio gerenciado. Além disso, os membros do grupo "Administradores do AAD DC" podem criar suas próprias UOs personalizadas no domínio gerenciado. Eles também podem criar GPOs personalizados e vinculá-los a essas UOs personalizadas. Os usuários que pertencem ao grupo "Administradores do AAD DC" recebem privilégios de administração da Política de Grupo no domínio gerenciado.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa do seguinte:

1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](create-instance.md).
4. Uma **máquina virtual ingressada no domínio** por meio da qual você administra o domínio gerenciado dos Serviços de Domínio do Azure AD. Se você não tiver esse tipo de máquina virtual, execute todas as tarefas descritas no artigo [Ingressar uma máquina virtual do Windows em um domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md).
5. Você precisa das credenciais de uma **conta de usuário que pertença ao grupo "Administradores do AAD DC"** em seu diretório para administrar a Política de Grupo para seu domínio gerenciado.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Tarefa 1 — Provisionar uma máquina virtual adicionado ao domínio para administrar a Política de Grupo remotamente para o domínio gerenciado
Domínios gerenciados dos Serviços de Domínio do Azure AD podem ser gerenciados remotamente usando as ferramentas administrativas familiares do Active Directory, como o ADAC (Centro Administrativo do Active Directory) ou o AD PowerShell. Do mesmo modo, a Política de Grupo para o domínio gerenciado pode ser administrada remotamente usando as ferramentas de administração da Política de Grupo.

Os administradores em seu diretório do de Azure AD não têm privilégios para se conectar a controladores de domínio no domínio gerenciado por meio da Área de Trabalho Remota. Os membros do grupo "Administradores do AAD DC" podem administrar remotamente a Política de Grupo para domínios gerenciados. Eles também podem usar as ferramentas de Política de Grupo em um computador servidor/cliente adicionado ao domínio gerenciado. As ferramentas de Política de Grupo podem ser instaladas como parte do recurso opcional Gerenciamento de Política de Grupo no Windows Server e computadores cliente adicionados ao domínio gerenciado.

A primeira etapa é provisionar uma máquina virtual do Windows Server que tenha ingressado no domínio gerenciado. Para obter instruções, confira o artigo intitulado [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Tarefa 2 — Instalar as ferramentas de Política de Grupo na máquina virtual
Execute as etapas a seguir para instalar as Ferramentas de administração de Política de Grupo na máquina virtual adicionada ao domínio.

1. Navegue até o Portal do Azure. Clique em **Todos os recursos** no painel esquerdo. Localize e clique na máquina virtual criada na Tarefa 1.
2. Clique no botão **Conectar** na guia Visão Geral. Um arquivo do Protocolo RDP (.rdp) é criado e baixado.

    ![Conectar-se à máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Para se conectar à sua VM, abra o arquivo RDP baixado. Se solicitado, clique em **Conectar**. No prompt de logon, use as credenciais de um usuário pertencente ao grupo “Administradores do AAD DC”. Por exemplo, usamos “bob@domainservicespreview.onmicrosoft.com” no nosso caso. Você pode receber um aviso do certificado durante o processo de logon. Clique em Sim ou em Continuar para prosseguir com a conexão.
4. Na tela inicial, abra **Gerenciador do Servidor**. Clique em **Adicionar Funções e Recursos** no painel central da janela do Gerenciador do Servidor.

    ![Iniciar o Gerenciador do Servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na página **Antes de Começar** do **Assistente de Adição de Funções e Recursos**, clique em **Avançar**.

    ![Página Antes de Começar](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na página **Tipo de Instalação**, deixe a opção **Instalação baseada em função ou recurso** marcada e clique em **Avançar**.

    ![Página Tipo de Instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na página **Seleção de Servidor**, escolha a máquina virtual atual no pool de servidores e clique em **Avançar**.

    ![Página Seleção de Servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na página **Funções do Servidor**, clique em **Avançar**. Ignoramos esta página, já que não estamos instalando nenhuma função no servidor.
9. Na página **Recursos**, selecione o recurso **Gerenciamento de Política de Grupo**.

    ![Página Recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Na página **Confirmação**, clique em **Instalar** para instalar o recurso Gerenciamento de Política de Grupo na máquina virtual. Quando a instalação do recurso for concluída com êxito, clique em **Fechar** para sair do **Assistente de Adição de Funções e Recursos**.

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Tarefa 3 — Iniciar o console de gerenciamento de Política de Grupo para administrar a Política de Grupo
Você pode usar o console de gerenciamento de Política de Grupo na máquina virtual adicionada ao domínio para administrar a Política de Grupo no domínio gerenciado.

> [!NOTE]
> É preciso ser um membro do grupo "Administradores do AAD DC" para administrar a Política de Grupo no domínio gerenciado.
>
>

1. Na tela inicial, clique em **Ferramentas Administrativas**. Você deverá ver o console de **Gerenciamento de Política de Grupo** instalado na máquina virtual.

    ![Iniciar o Gerenciamento de Política de Grupo](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Clique em **Gerenciamento de Política de Grupo** para iniciar o console de Gerenciamento de Política de Grupo.

    ![Console de Política de Grupo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Tarefa 4 - Personalizar objetos de política de grupo internos
Há dois GPOs (Objetos de Política de Grupo) internos — um para cada um dos contêineres "Computadores do AADDC" e "Usuários do AADDC" no domínio gerenciado. Você pode personalizar esses GPOs internos para configurar a política de grupo no domínio gerenciado.

1. No console **Gerenciamento de Política de Grupo**, clique para expandir os nós **Floresta: contoso100.com** e **Domínios** para ver as políticas de grupo de seus domínios gerenciados.

    ![GPOs internos](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Você pode personalizar esses GPOs internos para configurar as políticas de grupo no domínio gerenciado. Clique com botão direito do mouse no GPO e clique em **Editar...** para personalizar o GPO interno. A ferramenta Editor de Configuração de Política de Grupo permite que você personalize o GPO.

    ![Editar GPO interno](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Agora você pode usar o console do **Editor de Gerenciamento de Política de Grupo** para editar o GPO interno. Por exemplo, a captura de tela a seguir mostra como personalizar o GPO interno "Computadores do AADDC".

    ![Personalizar GPO](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Tarefa 5 – Criar um objeto de política de grupo (GPO) personalizado
Você pode criar ou importar seus próprios objetos de política de grupo personalizados. Também pode vincular GPOs personalizados a uma UO personalizada que você criou no seu domínio gerenciado. Para saber mais sobre como criar unidades organizacionais personalizadas, confira [Criar uma UO personalizada em um domínio gerenciado](create-ou.md).

> [!NOTE]
> É preciso ser um membro do grupo "Administradores do AAD DC" para administrar a Política de Grupo no domínio gerenciado.
>
>

1. No console **Gerenciamento de Política de Grupo**, clique para selecionar a unidade organizacional (UO) personalizada. Clique com o botão direito do mouse na UO e clique em **Criar um GPO neste domínio e vinculá-lo aqui...** .

    ![Criar um GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Especifique um nome para o novo GPO e clique em **OK**.

    ![Especifique um nome para o GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Um novo GPO é criado e vinculado à sua UO personalizada. Clique com botão direito do mouse no GPO e clique em **Editar...**  no menu.

    ![GPO recém-criado](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Você pode personalizar o GPO recém-criado usando o **Editor de gerenciamento de política de grupo**.

    ![Personalizar o novo GPO](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Mais informações sobre como usar o [Console de Gerenciamento de Política de Grupo](https://technet.microsoft.com/library/cc753298.aspx) estão disponíveis no Technet.

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - Guia de Introdução](create-instance.md)
* [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Gerenciar um domínio de Azure AD Domain Services](manage-domain.md)
* [Console de Gerenciamento de Política de Grupo](https://technet.microsoft.com/library/cc753298.aspx)
