---
title: 'Azure Active Directory Domain Services: Guia de administração | Microsoft Docs'
description: Criar uma UO (Unidade Organizacional) em um domínio gerenciado dos Serviços de Domínio do Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: b2bdad25d676d65494fdd5b6a314f8c3381254de
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473672"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Criar uma Unidade Organizacional (UO) em um domínio gerenciado dos Serviços de Domínio do Azure AD
Domínios gerenciados dos Serviços de Domínio do Azure AD incluem dois contêineres internos chamados 'Computadores do AADDC' e 'Usuários do AADDC', respectivamente. O contêiner 'Computadores do AADDC' tem objetos de computador para todos os computadores ingressados no domínio gerenciado. O contêiner 'Usuários do AADDC' inclui usuários e grupos no locatário do Azure AD. Ocasionalmente, pode ser necessário criar contas de serviço no domínio gerenciado para implantar cargas de trabalho. Para essa finalidade, você pode criar uma UO (unidade organizacional) personalizada no domínio gerenciado e criar contas de serviço dentro dessa UO. Este artigo mostra como criar uma UO em seu domínio gerenciado.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa do seguinte:

1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](create-instance.md).
4. Uma máquina virtual ingressada no domínio por meio da qual você administra o domínio gerenciado do Azure AD Domain Services. Se você não tiver esse tipo de máquina virtual, execute todas as tarefas descritas no artigo [Ingressar uma máquina virtual do Windows em um domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md).
5. Você precisa das credenciais de uma **conta de usuário que pertença ao grupo “Administradores do controlador de domínio do AAD”** em seu diretório para criar uma UO personalizada em seu domínio gerenciado.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Instalar ferramentas de administração do AD em uma máquina virtual ingressada no domínio para administração remota
Domínios gerenciados dos Serviços de Domínio do Azure AD podem ser gerenciados remotamente usando as ferramentas administrativas familiares do Active Directory, como o ADAC (Centro Administrativo do Active Directory) ou o AD PowerShell. Os administradores de locatários não têm privilégios para conectar-se a controladores de domínio no domínio gerenciado por meio da Área de Trabalho Remota. Para administrar o domínio gerenciado, instale o recurso de ferramentas de administração do AD em uma máquina virtual ingressada no domínio gerenciado. Consulte o artigo intitulado [gerenciar um domínio do Azure AD Domain Services](manage-domain.md) para obter instruções.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Criar uma Unidade Organizacional no domínio gerenciado
Agora que as Ferramentas Administrativas do AD estão instaladas na máquina virtual ingressada no domínio, podemos usar essas ferramentas para criar uma unidade organizacional no domínio gerenciado. Execute as seguintes etapas:

> [!NOTE]
> Somente os membros do grupo 'Administradores de DC do AAD' têm os privilégios necessários para criar uma nova UO. Certifique-se de executar as etapas a seguir como um usuário pertencente a esse grupo.
>
>

1. Na tela inicial, clique em **Ferramentas Administrativas**. Você deve ver as ferramentas administrativas do AD instaladas na máquina virtual.

    ![Ferramentas Administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Clique em **Centro Administrativo do Active Directory**.

    ![Centro Administrativo do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Para exibir o domínio, clique no nome de domínio no painel à esquerda (por exemplo, 'contoso100.com').

    ![ADAC - exibir domínio](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. No lado direito do painel **Tarefas**, clique em **Novo** no nó de nome de domínio. Neste exemplo, vamos clicar em **Novo** no nó 'contoso100(local)' no lado direito do painel **Tarefas**.

    ![ADAC - nova UO](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Você deve ver a opção de criar uma unidade organizacional. Clique em **Unidade Organizacional** para iniciar o diálogo **Criar Unidade Organizacional**.
6. Na caixa de diálogo **Criar Unidade Organizacional**, especifique um **Nome** para a nova UO. Forneça uma descrição curta para a UO. Você também pode definir o campo **Gerenciado Por** da UO. Para criar a UO personalizada, clique em **OK**.

    ![ADAC - criar caixa de diálogo da UO](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. A UO recém-criada agora deve aparecer no ADAC (Centro Administrativo do AD).

    ![ADAC - UO criada](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Permissões/segurança para UOs recém-criadas
Por padrão, são concedidos ao usuário (membro do grupo 'Administradores de DC do AAD') que criou a UO personalizada privilégios administrativos (controle total) sobre a UO. O usuário pode, em seguida, continuar e conceder privilégios a outros usuários ou ao grupo 'Administradores do AAD DC', conforme desejado. Como visto na captura de tela a seguir, o usuário ‘bob@domainservicespreview.onmicrosoft.com’ que criou a nova unidade organizacional ‘MyCustomOU’ recebe controle total sobre ela.

 ![ADAC - segurança da nova UO](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Notas sobre como administrar UOs personalizadas
Agora que você criou uma UO personalizada, pode criar usuários, grupos, computadores e contas de serviço nessa UO. Você não pode mover usuários ou grupos da UO 'Usuários de DC do AAD' para as UOs personalizadas.

> [!WARNING]
> As contas de usuário, de grupos, as contas de serviço e de objetos de computador que você criar em OUs personalizadas não ficam disponíveis no seu locatário do Azure AD. Em outras palavras, esses objetos não aparecem usando a API do Azure AD Graph ou na interface do usuário do Azure AD. Esses objetos só estarão disponíveis no seu domínio gerenciado dos Azure AD Domain Services.
>
>

## <a name="related-content"></a>Conteúdo relacionado
* [Gerenciar um domínio do Azure AD Domain Services](manage-domain.md)
* [Gerenciar política de grupo para o Azure AD Domain Services](manage-group-policy.md)
* [Centro Administrativo do Active Directory: Introdução](https://technet.microsoft.com/library/dd560651.aspx)
* [Guia passo a passo de contas de serviço](https://technet.microsoft.com/library/dd548356.aspx)
