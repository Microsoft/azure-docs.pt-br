---
title: 'Tutorial: Integração do Azure Active Directory ao Veracode | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e80a2e554d31ad85ddb1111f84a96e1814c3969f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638491"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Tutorial: Integração do Azure Active Directory ao Veracode

Neste tutorial, você aprenderá a integrar o Veracode ao Azure AD (Azure Active Directory).

A integração do Veracode ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Veracode.
- Você pode permitir que os usuários façam logon automaticamente no Veracode (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Veracode, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Veracode habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Veracode da galeria
1. Configurar e testar logon único do Azure AD

## <a name="add-veracode-from-the-gallery"></a>Adicionar o Veracode da galeria
Para configurar a integração do Veracode ao Azure AD, você precisará adicionar o Veracode da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Veracode da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Veracode**, selecione **Veracode** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Veracode na lista de resultados](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Veracode, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Veracode é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Veracode.

No Veracode, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Veracode, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Veracode](#create-a-veracode-test-user)** – para ter um equivalente de Brenda Fernandes no Veracode vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Veracode.

**Para configurar o logon único do Azure AD com o Veracode, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Veracode**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

1. Na seção **URLs e domínio do Veracode**, o usuário não precisa executar nenhuma etapa, uma vez que o aplicativo já está pré-integrado ao Azure. 

    ![Configurar o logon único](./media/veracode-tutorial/tutorial_veracode_url.png)

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

1. O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Veracode com sua conta do AD do Azure usando a federação baseada no protocolo SAML.

    Seu aplicativo Veracode espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de **atributos de token SAML** . A captura de tela a seguir mostra um exemplo disso.
    
    ![Atributos](./media/veracode-tutorial/tutorial_veracode_attr.png "Atributos")

1. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:

    | Nome do atributo | Valor do atributo |
    |--- |--- |
    | nome |User.givenname |
    | sobrenome |User.surname |
    | email |User.mail |
    
     a. Para cada linha de dados na tabela acima, clique em **adicionar atributo do usuário**.
    
    ![Atributos](./media/veracode-tutorial/tutorial_veracode_addattr.png "Atributos")
    
    ![Atributos](./media/veracode-tutorial/tutorial_veracode_addattr1.png "Atributos")
    
    b. Na caixa de texto **Nome do Atributo** , digite o nome do atributo mostrado para essa linha.
    
    c. Na caixa de texto **Valor do Atributo** , selecione o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/veracode-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Veracode**, clique em **Configurar o Veracode** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML** da **seção Referência Rápida.**

    ![Configuração do Veracode](./media/veracode-tutorial/tutorial_veracode_configure.png) 

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Veracode como administrador.

1. No menu na parte superior, clique em **Configurações** e em **Administrador**.
   
    ![Administração](./media/veracode-tutorial/ic802911.png "Administração")

1. Clique na guia **SAML** .

1. Na seção **Configurações do SAML da Organização** , realize as seguintes etapas:
   
    ![Administração](./media/veracode-tutorial/ic802912.png "Administração")
   
     a.  Na caixa de texto **Emissor**, cole o valor da **ID da Entidade SAML** copiado do Portal do Azure.
    
    b. Para carregar seu certificado baixado do Portal do Azure, clique em **Escolher Arquivo**.
   
    c. Selecione **Habilitar Autorregistro**.

1. Na seção **Configurações de Autorregistro**, realize as seguintes etapas e clique em **Salvar**:
   
    ![Administração](./media/veracode-tutorial/ic802913.png "Administração")
   
     a. Para **Ativação de Novo Usuário**, selecione **Sem Ativação Necessária**.
   
    b. Para **Atualizações de Dados do Usuário**, selecione **Dados do Usuário de Preferência do Veracode**.
   
    c. Para **Detalhes de Atributos do SAML**, selecione o seguinte:
      * **Funções de usuário**
      * **Administrador de políticas**
      * **Revisor**
      * **Orientações de Segurança**
      * **Executivo**
      * **Emissor**
      * **Criador**
      * **Todos os Tipos de Verificação**
      * **Associações de Equipe**
      * **Equipe Padrão**

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/veracode-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/veracode-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/veracode-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/veracode-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-veracode-test-user"></a>Criar um usuário de teste do Veracode
Para permitir que os usuários do AD do Azure façam logon no Veracode, eles devem ser provisionados no Veracode. No caso do Veracode, o provisionamento é uma tarefa automatizada. Não há nenhum item de ação para você. Os usuários são criados automaticamente, se necessário, durante a primeira tentativa de logon único.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Veracode ou APIs fornecidas pelo Veracode para provisionar as contas de usuário do Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Veracode.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Veracode, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Veracode**.

    ![O link do Veracode na lista de Aplicativos](./media/veracode-tutorial/tutorial_veracode_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Veracode no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Veracode.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png

