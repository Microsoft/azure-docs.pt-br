---
title: Criar e gerenciar a política de grupo no Azure AD Domain Services | Microsoft Docs
description: Saiba como editar os GPOs (objetos de diretiva de grupo) internos e criar suas próprias políticas personalizadas em um Azure Active Directory Domain Services domínio gerenciado.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f1f2499c49c4adf16b632bc75c246a28330ad27b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619378"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Administrar Política de Grupo em um domínio Azure Active Directory Domain Services gerenciado

As configurações para objetos de usuário e computador no Azure Active Directory Domain Services (AD DS do Azure) geralmente são gerenciadas usando objetos Política de Grupo (GPOs). O Azure AD DS inclui GPOs internos para os contêineres *usuários AADDC* e *computadores AADDC* . Você pode personalizar esses GPOs internos para configurar Política de Grupo conforme necessário para o seu ambiente. Os membros do grupo de *Administradores de DC do Azure ad* têm política de grupo privilégios de administração no domínio de AD DS do Azure e também podem criar GPOs personalizados e UOs (unidades organizacionais). Para obter mais informações sobre o que Política de Grupo é e como funciona, consulte [política de grupo visão geral][group-policy-overview].

Em um ambiente híbrido, as políticas de grupo configuradas em um ambiente de AD DS local não são sincronizadas com o AD DS do Azure. Para definir as definições de configuração para usuários ou computadores no Azure AD DS, edite um dos GPOs padrão ou crie um GPO personalizado.

Este artigo mostra como instalar as ferramentas de gerenciamento de Política de Grupo e, em seguida, editar os GPOs internos e criar GPOs personalizados.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar um Azure Active Directory Domain Services domínio gerenciado][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é unida ao domínio gerenciado AD DS do Azure.
    * Se necessário, conclua o tutorial para [criar uma VM do Windows Server e associá-la a um domínio gerenciado][create-join-windows-vm].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

> [!NOTE]
> Você pode usar Política de Grupo Modelos Administrativos copiando os novos modelos para a estação de trabalho de gerenciamento. Copie os arquivos *. admx* no `%SYSTEMROOT%\PolicyDefinitions` e copie os arquivos *. adml* específicos da localidade para `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]` , onde `Language-CountryRegion` o corresponde ao idioma e à região dos arquivos *. adml* .
>
> Por exemplo, copie a versão em inglês Estados Unidos dos arquivos *. adml* para a `\en-us` pasta.
>
> Como alternativa, você pode armazenar centralmente seu modelo administrativo de Política de Grupo nos controladores de domínio que fazem parte do domínio gerenciado. Para obter mais informações, consulte [como criar e gerenciar o armazenamento central para Política de Grupo modelos administrativos no Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="install-group-policy-management-tools"></a>Instalar ferramentas de gerenciamento de Política de Grupo

Para criar e configurar o objeto de Política de Grupo (GPOs), você precisa instalar as ferramentas de gerenciamento de Política de Grupo. Essas ferramentas podem ser instaladas como um recurso no Windows Server. Para obter mais informações sobre como instalar as ferramentas administrativas em um cliente Windows, consulte instalar o [ferramentas de administração de servidor remoto (RSAT)][install-rsat].

1. Entre na sua VM de gerenciamento. Para obter as etapas sobre como se conectar usando o portal do Azure, consulte [conectar-se a uma VM do Windows Server][connect-windows-server-vm].
1. O **Gerenciador do Servidor** deve abrir por padrão ao entrar na VM. Caso contrário, no menu **Iniciar**, selecione **Gerenciador do Servidor**.
1. No painel *Dashboard* da janela **Gerenciador do Servidor**, selecione **Adicionar Funções e Recursos**.
1. Na página **Antes de Você Começar** do *Assistente de Adição de Funções e Recursos*, selecione **Avançar**.
1. Para o *Tipo de Instalação*, deixe a opção **Instalação baseada em função ou recurso** marcada e selecione **Avançar**.
1. Na página **Seleção de Servidor**, escolha a VM atual no pool de servidores, como *myvm.aaddscontoso.com* e, em seguida, selecione **Avançar**.
1. Na página **Funções do Servidor**, clique em **Avançar**.
1. Na página **Recursos**, selecione o recurso **Gerenciamento de Política de Grupo**.

    ![Instalar o ' Gerenciamento de Política de Grupo ' na página de recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Na página **Confirmação**, selecione **Instalar**. Pode levar um minuto ou dois para instalar as ferramentas de gerenciamento de Política de Grupo.
1. Quando a instalação do recurso for concluída, selecione **Fechar** para sair do **Assistente de Adição de Funções e Recursos**.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Abrir o Console de Gerenciamento de Política de Grupo e editar um objeto

Existem objetos de diretiva de grupo (GPOs) padrão para usuários e computadores em um domínio gerenciado. Com o recurso de gerenciamento de Política de Grupo instalado na seção anterior, vamos exibir e editar um GPO existente. Na próxima seção, você criará um GPO personalizado.

> [!NOTE]
> Para administrar a política de grupo em um domínio gerenciado, você deve estar conectado a uma conta de usuário que seja membro do grupo de *Administradores de DC do AAD* .

1. Na tela iniciar, selecione **Ferramentas administrativas**. Uma lista de ferramentas de gerenciamento disponíveis é mostrada, incluindo o **Gerenciamento de política de grupo** instalado na seção anterior.
1. Para abrir o Console de Gerenciamento de Política de Grupo (GPMC), escolha **Gerenciamento de política de grupo**.

    ![O Console de Gerenciamento de Política de Grupo abre pronto para editar objetos de política de grupo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Há dois objetos de Política de Grupo internos (GPOs) em um domínio gerenciado-um para o contêiner de *computadores AADDC* e outro para o contêiner de *usuários do AADDC* . Você pode personalizar esses GPOs para configurar a política de grupo conforme necessário em seu domínio gerenciado.

1. No console de **Gerenciamento do política de grupo** , expanda o nó **floresta: aaddscontoso.com** . Em seguida, expanda os nós **domínios** .

    Existem dois contêineres internos para *computadores AADDC* e *usuários do AADDC*. Cada um desses contêineres tem um GPO padrão aplicado a eles.

    ![GPOs internos aplicados aos contêineres padrão ' AADDC Computers ' e ' AADDC users '](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Esses GPOs internos podem ser personalizados para configurar políticas de grupo específicas em seu domínio gerenciado. Selecione um dos GPOs, como o *GPO computadores AADDC*, e escolha **Editar...**.

    ![Escolha a opção para ' Editar ' um dos GPOs internos](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. A ferramenta Editor de Gerenciamento de Política de Grupo é aberta para permitir que você personalize o GPO, como *políticas de conta*:

    ![Captura de tela da Editor de Gerenciamento de Política de Grupo.](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Quando terminar, escolha **arquivo > salvar** para salvar a política. Os computadores atualizam Política de Grupo por padrão a cada 90 minutos e aplicam as alterações feitas.

## <a name="create-a-custom-group-policy-object"></a>Criar um objeto de Política de Grupo personalizado

Para agrupar configurações de política semelhantes, você geralmente cria GPOs adicionais em vez de aplicar todas as configurações necessárias no GPO único, padrão. Com o Azure AD DS, você pode criar ou importar seus próprios objetos de política de grupo personalizados e vinculá-los a uma UO personalizada. Se você precisar primeiro criar uma UO personalizada, consulte [criar uma UO personalizada em um domínio gerenciado](create-ou.md).

1. No console de **Gerenciamento do política de grupo** , selecione sua UO (unidade organizacional) personalizada, como *MyCustomOU*. Selecione a UO com o botão direito do mouse e escolha **criar um GPO neste domínio e vincule-o aqui...**:

    ![Criar um GPO personalizado no console de gerenciamento de Política de Grupo](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Especifique um nome para o novo GPO, como *meu GPO personalizado* e, em seguida, selecione **OK**. Opcionalmente, você pode basear esse GPO personalizado em um GPO existente e no conjunto de opções de política.

    ![Especifique um nome para o novo GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. O GPO personalizado é criado e vinculado à sua UO personalizada. Para configurar agora as configurações de política, selecione o GPO personalizado e escolha **Editar...**:

    ![Escolha a opção para ' Editar ' seu GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. O **Editor de gerenciamento de política de grupo** é aberto para permitir que você personalize o GPO:

    ![Personalizar o GPO para definir as configurações conforme necessário](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Quando terminar, escolha **arquivo > salvar** para salvar a política. Os computadores atualizam Política de Grupo por padrão a cada 90 minutos e aplicam as alterações feitas.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as configurações de Política de Grupo disponíveis que você pode configurar usando o Console de Gerenciamento de Política de Grupo, consulte [trabalhar com política de grupo itens de preferência][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
