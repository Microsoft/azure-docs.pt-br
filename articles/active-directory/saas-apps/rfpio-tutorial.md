---
title: 'Tutorial: Integração do Azure Active Directory com o RFPIO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6b8109c8d3834f932ba492eddb8d6332acc1707
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61079725"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Tutorial: Integração do Azure Active Directory com o RFPIO

Neste tutorial, você aprenderá a integrar o RFPIO ao Azure AD (Azure Active Directory).

A integração do RFPIO ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao RFPIO.
- Você pode permitir que usuários façam logon automaticamente no RFPIO (Logon Único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao RFPIO, você precisará dos seguintes itens:

- Uma assinatura do Azure AD.
- Uma assinatura habilitada para logon único do RFPIO.

> [!NOTE]
> Não é recomendável que você use um ambiente de produção para testar as etapas neste tutorial.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o RFPIO da galeria.
1. Configurar e testar o logon único do Azure AD.

## <a name="add-rfpio-from-the-gallery"></a>Adicionar o RFPIO da galeria
Para configurar a integração do RFPIO ao Azure AD, você precisará adicionar o RFPIO da galeria à sua lista de aplicativos SaaS gerenciados.

### <a name="to-add-rfpio-from-the-gallery"></a>Para adicionar o RFPIO da galeria

1. No **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, selecione o ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Selecione **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![Aplicativos][2]
    
1. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Aplicativos][3]

1. Na caixa de pesquisa, digite **RFPIO**.

    ![Criação de um usuário de teste do AD do Azure](./media/rfpio-tutorial/tutorial_rfpio_search.png)

1. No painel de resultados, selecione **RFPIO** e selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o RFPIO, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual é a relação entre o usuário do RFPIO equivalente e um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do RFPIO.

No RFPIO, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o RFPIO, você precisará concluir os seguintes blocos de construção:

1. **Configurar o logon único do Azure AD** – para habilitar o uso desse recurso pelos seus usuários.
1. **Criar um usuário de teste do Azure AD** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **Criar um usuário de teste do RFPIO** – para ter um equivalente de Brenda Fernandes no RFPIO que esteja vinculado à representação do usuário no Azure AD.
1. **Atribuir o usuário de teste do Azure AD** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **Testar o logon único** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo RFPIO.

**Para configurar o logon único do Azure AD com o RFPIO, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **RFPIO**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/rfpio-tutorial/tutorial_rfpio_samlbase.png)

1. Na seção **Domínio e URLs do RFPIO**, se você desejar configurar o aplicativo em modo iniciado pelo **IDP**:

    ![Configurar o logon único](./media/rfpio-tutorial/tutorial_rfpio_url.png)

     a. Na caixa de texto **Identificador**, digite a URL: `https://www.rfpio.com`

    ![Configurar o logon único](./media/rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Marque **Mostrar configurações de URL avançadas**.

    c. Na caixa de texto **Estado de Retransmissão** insira um valor de cadeia de caracteres. Entre em contato com a [equipe de suporte do RFPIO](https://www.rfpio.com/contact/) para obter esse valor. 

1. Marque **Mostrar configurações de URL avançadas**. Se quiser configurar o aplicativo no modo iniciado em **SP**: 

    ![Configurar o logon único](./media/rfpio-tutorial/tutorial_rfpio_url2.png)

    Na caixa de texto **URL de Entrada**, digite a URL: `https://www.app.rfpio.com`

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/rfpio-tutorial/tutorial_rfpio_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/rfpio-tutorial/tutorial_general_400.png)

1. Em outra janela do navegador da Web, faça logon no site do **RFPIO** como administrador.

1. Clique no menu suspenso de canto inferior esquerdo.

    ![Configurar o logon único](./media/rfpio-tutorial/app1.png)

1. Clique em **Configurações da Organização**. 

    ![Configurar o logon único](./media/rfpio-tutorial/app2.png)

1. Clique no **RECURSOS E INTEGRAÇÃO**.

    ![Configurar o logon único](./media/rfpio-tutorial/app4.png)

1. Em **Configuração de SSO de SAML**, clique **Editar**.

    ![Configurar o logon único](./media/rfpio-tutorial/app3.png)

1. Nesta seção, execute as seguintes ações:

    ![Configurar o logon único](./media/rfpio-tutorial/app5.png)
    
     a. Copie o conteúdo do **XML de Metadados baixado** e cole-o no campo de **configuração de identidade**.

    > [!NOTE]
    >Para copiar o conteúdo do **XML de Metadados** baixado, use o **Notepad++** ou o **Editor XML** adequado. 

    b. Clique em **Validar**.

    c. Depois de clicar em **Validar**, inverta **SAML (Habilitado)** para ativado.

    d. Clique em **Enviar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/rfpio-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/rfpio-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/rfpio-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/rfpio-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-rfpio-test-user"></a>Criar um usuário de teste do RFPIO

Para permitir que os usuários do Azure AD façam logon no RFPIO, eles devem ser provisionados no RFPIO.  
No caso do RFPIO, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site da empresa do RFPIO como administrador.

1. Clique no menu suspenso de canto inferior esquerdo.

    ![Configurar o logon único](./media/rfpio-tutorial/app1.png)

1. Clique em **Configurações da Organização**. 

    ![Configurar o logon único](./media/rfpio-tutorial/app2.png)

1. Clique em **MEMBROS DA EQUIPE**.

    ![Configurar o logon único](./media/rfpio-tutorial/app6.png)

1. Clique em **ADICIONAR MEMBROS**.

    ![Configurar o logon único](./media/rfpio-tutorial/app7.png)

1. Na seção **Adicionar Novos Membros**. Execute as seguintes ações:

    ![Configurar o logon único](./media/rfpio-tutorial/app8.png)

     a. Insira **Endereço de email** no campo **Inserir um email por linha**.

    b. Selecione **função** de acordo com suas necessidades.

    c. Clique em **ADICIONAR MEMBROS**.
        
    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao RFPIO.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao RFPIO, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **RFPIO**.

    ![Configurar o logon único](./media/rfpio-tutorial/tutorial_rfpio_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco RFPIO no Painel de Acesso, deverá fazer logon automaticamente no seu aplicativo RFPIO.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/rfpio-tutorial/tutorial_general_01.png
[2]: ./media/rfpio-tutorial/tutorial_general_02.png
[3]: ./media/rfpio-tutorial/tutorial_general_03.png
[4]: ./media/rfpio-tutorial/tutorial_general_04.png

[100]: ./media/rfpio-tutorial/tutorial_general_100.png

[200]: ./media/rfpio-tutorial/tutorial_general_200.png
[201]: ./media/rfpio-tutorial/tutorial_general_201.png
[202]: ./media/rfpio-tutorial/tutorial_general_202.png
[203]: ./media/rfpio-tutorial/tutorial_general_203.png

