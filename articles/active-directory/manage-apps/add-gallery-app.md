---
title: Adicionar um aplicativo da galeria – Azure Active Directory | Microsoft Docs
description: Saiba como adicionar um aplicativo da galeria do Azure AD aos seus aplicativos empresariais do Azure.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bdf275bbafa9c46cfc4577ac2843da0be74c7ef
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477276"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Adicionar um aplicativo da galeria à sua organização do Azure AD

O Azure AD (Azure Active Directory) tem uma galeria que contém milhares de aplicativos pré-integrados que são habilitados com o logon único empresarial. Este artigo descreve as etapas gerais para adição de um aplicativo da galeria à sua organização do Azure AD.

> [!IMPORTANT]
> Primeiro, verifique para seu aplicativo na [Lista de tutoriais sobre como integrar aplicativos SaaS ao Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Provavelmente, você encontrará diretrizes passo a passo para adicionar e configurar o aplicativo da galeria que deseja adicionar.

## <a name="add-a-gallery-application"></a>Adicionar um aplicativo da galeria

1. Entre no [portal do Azure](https://portal.azure.com) como administrador global do locatário do Azure AD, administrador do aplicativo de nuvem ou administrador do aplicativo.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

1. No painel do **Azure Active Directory**, selecione **Aplicativos empresariais**.

    ![Abrir aplicativos empresariais](media/add-application-portal/open-enterprise-apps.png)

1. Selecione **Novo aplicativo**.

    ![Novo aplicativo](media/add-application-portal/new-application.png)

1. Em **Adicionar por meio da galeria**, na caixa de pesquisa, insira o nome do aplicativo que deseja adicionar. 

    ![Pesquisar por nome ou categoria](media/add-application-portal/categories.png)

1. Selecione o aplicativo nos resultados.

1. (Opcional) No formulário específico do aplicativo, você pode editar o nome do aplicativo de acordo com as necessidades de sua organização.

1. Selecione **Adicionar**. A página **Visão Geral** do aplicativo será aberta.

## <a name="configure-user-sign-in-properties"></a>Configurar propriedades de logon do usuário

1. Selecione **Propriedades** para abrir o painel de propriedades para edição.

    ![Editar painel de propriedades](media/add-application-portal/edit-properties.png)

1. Defina as opções a seguir para determinar como os usuários atribuídos ou não atribuídos ao aplicativo podem entrar no aplicativo e se um usuário pode ver o aplicativo no Painel de Acesso.

    - **Habilitado para que os usuários entrem** determina se os usuários atribuídos ao aplicativo podem entrar nele.
    - A **atribuição de usuário obrigatória** determina se os usuários não atribuídos ao aplicativo podem entrar nele.
    - **Visível para usuário** determina se os usuários atribuídos a um aplicativo podem vê-lo no painel de acesso e no inicializador do O365.

      Comportamento de usuários **atribuídos**:

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

      Comportamento de usuários **não atribuídos**:

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

1. Para usar um logotipo personalizado, crie um logotipo de 215 x 215 pixels e salve-o no formato PNG. Em seguida, procure o logotipo e carregue-o.

    ![Alterar o logotipo](media/add-application-portal/change-logo.png)

1. Quando terminar, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Agora que você adicionou o aplicativo à sua organização do Azure AD, [escolha um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que deseja usar e veja o artigo apropriado abaixo:

- [Configurar o logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurar o logon único com senha](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar o logon vinculado](configure-linked-sign-on.md)

