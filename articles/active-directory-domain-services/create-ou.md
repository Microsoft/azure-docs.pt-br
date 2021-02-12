---
title: Criar uma UO (unidade organizacional) no Azure AD Domain Services | Microsoft Docs '
description: Saiba como criar e gerenciar uma UO (unidade organizacional) personalizada em um Azure AD Domain Services domínio gerenciado.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fbdfcc23553a27aaa4d7bbd4bff9c4f33cb8fd19
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620011"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>Criar uma UO (unidade organizacional) em um domínio gerenciado Azure Active Directory Domain Services

As UOs (unidades organizacionais) em um domínio gerenciado Active Directory Domain Services (AD DS) permitem agrupar logicamente objetos, como contas de usuário, contas de serviço ou contas de computador. Em seguida, você pode atribuir administradores a UOs específicas e aplicar a política de grupo para impor as definições de configuração de destino.

Os domínios gerenciados AD DS do Azure incluem as duas UOs internas a seguir:

* *Computadores AADDC* – contém objetos de computador para todos os computadores que ingressaram no domínio gerenciado.
* *Usuários do AADDC* – inclui usuários e grupos sincronizados no do locatário do Azure AD.

Conforme você cria e executa cargas de trabalho que usam o Azure AD DS, talvez seja necessário criar contas de serviço para que os aplicativos se autentiquem. Para organizar essas contas de serviço, você geralmente cria uma UO personalizada no domínio gerenciado e, em seguida, cria contas de serviço dentro dessa UO.

Em um ambiente híbrido, as UOs criadas em um ambiente de AD DS local não são sincronizadas com o domínio gerenciado. Os domínios gerenciados usam uma estrutura de UO simples. Todas as contas de usuário e grupos são armazenados no contêiner *AADDC Users* , apesar de serem sincronizados de diferentes domínios ou florestas locais, mesmo que você tenha configurado uma estrutura de UO hierárquica lá.

Este artigo mostra como criar uma UO em seu domínio gerenciado.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar um Azure Active Directory Domain Services domínio gerenciado][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é unida ao domínio gerenciado AD DS do Azure.
    * Se necessário, conclua o tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Considerações e limitações de UO personalizadas

Ao criar UOs personalizadas em um domínio gerenciado, você obterá flexibilidade de gerenciamento adicional para o gerenciamento de usuários e a aplicação da diretiva de grupo. Em comparação com um ambiente de AD DS local, há algumas limitações e considerações ao criar e gerenciar uma estrutura de UO personalizada em um domínio gerenciado:

* Para criar UOs personalizadas, os usuários devem ser membros do grupo de *Administradores do AAD DC* .
* Um usuário que cria uma UO personalizada recebe privilégios administrativos (controle total) nessa UO e é o proprietário do recurso.
    * Por padrão, o grupo de *Administradores de DC do AAD* também tem controle total da UO personalizada.
* Uma UO padrão para *usuários AADDC* é criada contendo todas as contas de usuário sincronizadas do seu locatário do Azure AD.
    * Não é possível mover usuários ou grupos da UO *usuários do AADDC* para as UOs personalizadas que você criar. Somente contas de usuário ou recursos criados no domínio gerenciado podem ser movidos para UOs personalizadas.
* Contas de usuário, grupos, contas de serviço e objetos de computador que você cria em UOs personalizadas não estão disponíveis no seu locatário do Azure AD.
    * Esses objetos não aparecem usando a API Microsoft Graph ou na interface do usuário do Azure AD; Eles estão disponíveis somente em seu domínio gerenciado.

## <a name="create-a-custom-ou"></a>Criar uma UO personalizada

Para criar uma UO personalizada, use as ferramentas administrativas Active Directory de uma VM ingressada no domínio. O Centro Administrativo do Active Directory permite exibir, editar e criar recursos em um domínio gerenciado, incluindo UOs.

> [!NOTE]
> Para criar uma UO personalizada em um domínio gerenciado, você deve estar conectado a uma conta de usuário que seja membro do grupo de *Administradores de DC do AAD* .

1. Entre na sua VM de gerenciamento. Para obter as etapas sobre como se conectar usando o portal do Azure, consulte [conectar-se a uma VM do Windows Server][connect-windows-server-vm].
1. Na tela iniciar, selecione **Ferramentas administrativas**. É mostrada uma lista de ferramentas de gerenciamento disponíveis que foram instaladas no tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
1. Para criar e gerenciar UOs, selecione **centro administrativo do Active Directory** na lista de ferramentas administrativas.
1. No painel esquerdo, escolha seu domínio gerenciado, como *aaddscontoso.com*. Uma lista de UOs e recursos existentes é mostrada:

    ![Selecione seu domínio gerenciado na Centro Administrativo do Active Directory](./media/create-ou/create-ou-adac-overview.png)

1. O painel **tarefas** é mostrado no lado direito da centro administrativo do Active Directory. No domínio, como *aaddscontoso.com*, selecione **novo > unidade organizacional**.

    ![Selecione a opção para criar uma nova UO no Centro Administrativo do Active Directory](./media/create-ou/create-ou-adac-new-ou.png)

1. Na caixa de diálogo **criar unidade organizacional** , especifique um **nome** para a nova UO, como *MyCustomOu*. Forneça uma breve descrição para a UO, como *UO personalizada para contas de serviço*. Se desejar, você também pode definir o campo **gerenciado por** para a UO. Para criar a UO personalizada, selecione **OK**.

    ![Criar uma UO personalizada da Centro Administrativo do Active Directory](./media/create-ou/create-ou-dialog.png)

1. De volta ao Centro Administrativo do Active Directory, a UO personalizada agora está listada e está disponível para uso:

    ![UO personalizada disponível para uso no Centro Administrativo do Active Directory](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar as ferramentas administrativas ou criar e usar contas de serviço, consulte os seguintes artigos:

* [Centro Administrativo do Active Directory: introdução](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [Guia passo a passo de contas de serviço](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm