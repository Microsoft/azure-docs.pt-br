---
title: Como configurar o logon único com senha para um aplicativo da Galeria do Azure AD | Microsoft Docs
description: Como configurar um aplicativo para logon único baseado em senha quando ele já estiver listado na Galeria de Aplicativos do Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: eb37fe247901b799a845ce75723a4a6b535cbb28
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422589"
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o logon único com senha para um aplicativo da Galeria do Azure AD

Quando adiciona um aplicativo da [Galeria de Aplicativos do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), você tem a opção de como deseja que os usuários façam logon nesse aplicativo. É possível configurar essa opção a qualquer momento, selecionando o item de navegação **Logon Único** em um Aplicativo Empresarial no [Portal do Azure](https://portal.azure.com/).

Um dos métodos de logon único disponíveis para você é a opção [Logon único baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Trata-se de uma ótima maneira de começar a integrar rapidamente aplicativos no Azure AD, e que lhe permite:

-   Habilitar o **logon único para os usuários** armazenando de forma segura e reproduzindo os nomes de usuário e senhas do aplicativo integrado ao Azure AD

-   **Dar suporte a aplicativos que exigem vários campos de entrada**, para aplicativos que exigem mais do que apenas os campos de nome de usuário e senha para entrar

-   **Personalizar os rótulos** dos campos de entrada de nome de usuário e senha que seus usuários veem no [Painel de Acesso do Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando digitam suas credenciais

-   Permitir que os **usuários** forneçam seus próprios nomes de usuário e senhas para as contas de aplicativos existentes que eles estiverem digitando manualmente no [Painel de Acesso do Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que um **membro do grupo de negócios** especifique os nomes de usuário e senhas atribuídos a um usuário usando o recurso de [Autoatendimento de Acesso ao Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Permitir que um **administrador** especifique os nomes de usuário e senhas atribuídos a um usuário usando o recurso Atualizar Credenciais ao [atribuir um usuário a um aplicativo](#assign-a-user-to-an-application-directly)

-   Permitir que um **administrador** especifique a senha ou nome de usuário compartilhado por um grupo de pessoas usando o recurso Atualizar Credenciais ao [atribuir um grupo a um aplicativo](#assign-an-application-to-a-group-directly)

A seção a seguir descreve como habilitar o [Logon único baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) para um aplicativo que já está na [Galeria de Aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-steps-required"></a>Visão geral das etapas necessárias
Para configurar um aplicativo da galeria do Azure AD, será necessário:

-   [Adicionar um aplicativo da galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

-   Atribuir o aplicativo a um usuário ou a um grupo

    -   [Atribuir um usuário diretamente a um aplicativo](#assign-a-user-to-an-application-directly)

    -   [Atribuir um aplicativo diretamente a um grupo](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar um aplicativo da galeria do Azure AD

Para adicionar um aplicativo da Galeria do Azure Active Directory, siga estas etapas:

1.  Abra o [Portal do Azure](https://portal.azure.com) e entre como um **Administrador Global** ou **Coadministrador**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6.  Na caixa de texto **Inserir um nome** da seção **Adicionar da galeria**, digite o nome do aplicativo.

7.  Selecione o aplicativo para o qual você deseja configurar o logon único.

8.  Antes de adicionar o aplicativo, é possível alterar seu nome pela caixa de texto **Nome**.

9.  Clique no botão **Adicionar** para adicionar o aplicativo.

Após um curto período, você poderá ver o painel de configuração do aplicativo.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga estas etapas:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual deseja configurar o logon único

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione o modo **Logon baseado em senha.**

9. [Atribuir usuários ao aplicativo](#assign-a-user-to-an-application-directly).

10. Além disso, também é possível fornecer credenciais em nome do usuário selecionando as linhas dos usuários, clicando em **Atualizar Credenciais** e digitando o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.

## <a name="assign-a-user-to-an-application-directly"></a>Atribuir um usuário diretamente a um aplicativo

Para atribuir um ou mais usuários a um aplicativo diretamente, siga estas etapas:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7. Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8. Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

9. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo** ou o **endereço de email** do usuário que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado do logotipo ou da foto de perfil do usuário para adicioná-lo à lista **Selecionado**.

12. **Opcional:** Caso queira **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse usuário à lista **Selecionado**.

13. Ao concluir a seleção dos usuários, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos usuários selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

## <a name="assign-an-application-to-a-group-directly"></a>Atribuir um aplicativo diretamente a um grupo

Para atribuir um ou mais usuários diretamente a um aplicativo, siga estas etapas:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7. Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8. Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

9. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo do grupo** que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **grupo** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção próxima ao logotipo ou ao perfil do grupo para adicionar o usuário na lista **Selecionado**.

12. **Opcional:** Caso queira **adicionar mais de um grupo**, digite outro **nome de grupo completo** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse grupo à lista **Selecionado**.

13. Ao concluir a seleção dos grupos, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos grupos selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos grupos selecionados.

Após um breve período, os usuários selecionados poderão iniciar esses aplicativos no Painel de Acesso.

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
