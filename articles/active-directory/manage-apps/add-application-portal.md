---
title: Adicionar um aplicativo a seu locatário do Azure Active Directory | Microsoft Docs
description: Este início rápido usa o portal do Azure para adicionar um aplicativo de galeria ao locatário do Azure AD (Azure Active Directory).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 04/09/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466660a1e064ef41eb330b36107dbdcb1d097498
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477313"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Início Rápido: Adicionar um aplicativo a seu locatário do Azure Active Directory

O Azure AD (Azure Active Directory) tem uma galeria que contém milhares de aplicativos pré-integrados. Alguns dos aplicativos que sua organização usa provavelmente estão na galeria. Este início rápido usa o portal do Azure para adicionar um aplicativo de galeria ao locatário do Azure AD (Azure Active Directory).

Depois que um aplicativo é adicionado ao locatário do Azure AD, você pode:

- Gerenciar o acesso de usuário ao aplicativo com uma política de acesso condicional.
- Configurar usuários para logon único no aplicativo com suas contas do Azure AD.

## <a name="before-you-begin"></a>Antes de começar

Para adicionar um aplicativo ao locatário, você precisará de:

- Uma assinatura do Azure AD
- Uma assinatura habilitada com logon único para seu aplicativo

Entre no [portal do Azure](https://portal.azure.com) como administrador global do locatário do Azure AD, administrador do aplicativo de nuvem ou administrador do aplicativo.

Para testar as etapas deste tutorial, recomendamos o uso de um ambiente que não seja de produção. Se não tiver um ambiente do Azure AD que não seja de produção, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Adicionar um aplicativo ao locatário do Azure AD

Para adicionar um aplicativo de galeria ao locatário do Azure AD:

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.
1. No painel do **Azure Active Directory**, selecione **Aplicativos empresariais**.
1. O painel **Todos os aplicativos** é aberto para mostrar uma amostra aleatória dos aplicativos em seu locatário do Azure AD. Selecione **Novo aplicativo** na parte superior do painel **Todos os aplicativos** para adicionar um aplicativo da galeria ao seu locatário.

    ![Selecione Novo aplicativo para adicionar um aplicativo da galeria ao seu locatário](media/add-application-portal/new-application.png)

1. No painel **Categorias**, você verá ícones na área **Aplicativos em destaque** que são uma amostra aleatória de aplicativos da galeria. Para ver mais aplicativos, você pode selecionar **Mostrar mais**, mas não recomendamos pesquisar dessa forma, porque há milhares de aplicativos na galeria.

    ![Pesquisar por um aplicativo por nome ou categoria](media/add-application-portal/categories.png)

1. Para pesquisar um aplicativo, em **Adicionar da galeria**, insira o nome do aplicativo que você deseja adicionar. Selecione o aplicativo nos resultados e selecione **Adicionar**. O exemplo a seguir mostra o formulário **Adicionar aplicativo** que aparece depois da pesquisa de github.com.

    ![Mostra como adicionar um aplicativo da galeria](media/add-application-portal/add-an-application.png)

1. No formulário específico do aplicativo, você pode alterar as informações de propriedade. Por exemplo, você pode editar o nome do aplicativo para atender às necessidades de sua organização. Este exemplo usa o nome **GitHub-test**.
1. Quando você terminar de fazer alterações nas propriedades, selecione **Adicionar**.
1. Uma página de introdução é exibida com as opções para configurar o aplicativo para a organização.

Você terminou de adicionar seu aplicativo. Fique à vontade para fazer um intervalo. As seções a seguir mostram como alterar o logotipo e editar outras propriedades do aplicativo.

## <a name="find-your-azure-ad-tenant-application"></a>Localizar seu aplicativo de locatário do Azure AD

Vamos supor que você teve que sair e agora você está retornando para continuar a configurar o aplicativo. A primeira coisa a fazer é localizar o aplicativo.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.
1. No painel do **Azure Active Directory**, selecione **Aplicativos empresariais**.
1. No menu suspenso **Tipo de Aplicativo**, selecione **Todos os Aplicativos** e, sem seguida, **Aplicar**. Para saber mais sobre as opções de exibição, consulte [Exibir aplicativos de locatário](view-applications-portal.md).
1. Agora você pode ver uma lista de todos os aplicativos em seu locatário do Azure AD. A lista é uma amostra aleatória. Para ver mais aplicativos, selecione **Mostrar mais** uma ou mais vezes.
1. Para localizar rapidamente um aplicativo no locatário, insira o nome dele na caixa de pesquisa e selecione **Aplicar**. Este exemplo localiza o aplicativo de teste do GitHub adicionado anteriormente.

    ![Mostra como encontrar um aplicativo usando a caixa de pesquisa](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Configurar propriedades de logon do usuário

Agora que você localizou o aplicativo, pode abri-lo e configurar suas propriedades.

Para editar as propriedades do aplicativo:

1. Selecione o aplicativo para abri-lo.
1. Selecione **Propriedades** para abrir o painel de propriedades para edição.

    ![Mostra a tela Propriedades e as propriedades editáveis do aplicativo](media/add-application-portal/edit-properties.png)

1. Reserve um tempo para entender as opções de entrada. As opções determinam como os usuários que foram atribuídos ou não ao aplicativo podem entrar nele. Além disso, as opções também determinam se um usuário pode ver o aplicativo no painel de acesso.

    - **Habilitado para que os usuários entrem** determina se os usuários atribuídos ao aplicativo podem entrar nele.
    - A **atribuição de usuário obrigatória** determina se os usuários não atribuídos ao aplicativo podem entrar nele.
    - **Visível para usuário** determina se os usuários atribuídos a um aplicativo podem vê-lo no painel de acesso e no inicializador do O365.

1. Use as tabelas a seguir para ajudar você a escolher as melhores opções para suas necessidades.

   - Comportamento de usuários **atribuídos**:

       | Configurações de propriedade do aplicativo | | | Experiência do usuário atribuído | |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição de usuário obrigatória? | Visível para os usuários? | Os usuários atribuídos podem entrar? | Os usuários atribuídos podem ver o aplicativo?* |
       | Sim | Sim | Sim | Sim | Sim  |
       | Sim | Sim | não  | Sim | não   |
       | Sim | não  | Sim | Sim | Sim  |
       | Sim | não  | não  | Sim | não   |
       | não  | Sim | Sim | não  | não   |
       | não  | Sim | não  | não  | não   |
       | não  | não  | Sim | não  | não   |
       | não  | não  | não  | não  | não   |

   - Comportamento de usuários **não atribuídos**:

       | Configurações de propriedade do aplicativo | | | Experiência de usuário não atribuído | |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição de usuário obrigatória? | Visível para os usuários? | Os usuários não atribuídos podem entrar? | Os usuários não atribuídos podem ver o aplicativo?* |
       | Sim | Sim | Sim | não  | não   |
       | Sim | Sim | não  | não  | não   |
       | Sim | não  | Sim | Sim | não   |
       | Sim | não  | não  | Sim | não   |
       | não  | Sim | Sim | não  | não   |
       | não  | Sim | não  | não  | não   |
       | não  | não  | Sim | não  | não   |
       | não  | não  | não  | não  | não   |

     *O usuário pode ver o aplicativo no painel de acesso e no iniciador de aplicativos do Office 365?

## <a name="use-a-custom-logo"></a>Usar um logotipo personalizado

Para usar um logotipo personalizado:

1. Crie um logotipo de 215 x 215 pixels e salve-o no formato PNG.
1. Como você já localizou seu aplicativo, selecione-o.
1. No painel esquerdo, selecione **Propriedades**.
1. Carregue o logotipo.
1. Quando terminar, selecione **Salvar**.

    ![Mostra como alterar o logotipo da página Propriedades do aplicativo](media/add-application-portal/change-logo.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você adicionou o aplicativo à sua organização do Azure AD, [escolha um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que deseja usar e veja o artigo apropriado abaixo:

- [Configurar o logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurar o logon único com senha](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar o logon vinculado](configure-linked-sign-on.md)
