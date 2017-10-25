---
title: "Tutorial: Integração do Azure Active Directory com MBC (Mercer BenefitsCentral) | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o MBC (Mercer BenefitsCentral)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3788b28c-49aa-4208-9acd-630362008e89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: jeedes
ms.openlocfilehash: c577a0163af04bec5737a215e788c3fb92c653c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mercer-benefitscentral-mbc"></a>Tutorial: Integração do Azure Active Directory com o MBC (Mercer BenefitsCentral)

Neste tutorial, você aprende como integrar o MBC (Mercer BenefitsCentral) com o Azure AD (Azure Active Directory).

A integração do MBC (Mercer BenefitsCentral) com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao MBC (Mercer BenefitsCentral).
- Você pode habilitar seus usuários para obterem logon automaticamente no MBC (Mercer BenefitsCentral) (Logon Único) com as contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o MBC (Mercer BenefitsCentral), os seguintes itens serão necessários:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do MBC (Mercer BenefitsCentral)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Mercer BenefitsCentral (MBC) da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-mercer-benefitscentral-mbc-from-the-gallery"></a>Adicionar o Mercer BenefitsCentral (MBC) da galeria
Para configurar a integração do Mercer BenefitsCentral (MBC) no Azure AD, será necessário adicionar o Mercer BenefitsCentral (MBC) da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Mercer BenefitsCentral (MBC) da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Mercer BenefitsCentral (MBC)**, selecione **Mercer BenefitsCentral (MBC)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![MBC (Mercer BenefitsCentral) na lista de resultados](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o MBC (Mercer BenefitsCentral) com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuários no MBC (Mercer BenefitsCentral) é equivalente a um usuário no Azure AD. Ou seja, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário no MBC (Mercer BenefitsCentral) relacionado.

No MBC (Mercer BenefitsCentral), atribua um valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** para estabelecer a relação de vinculação.

Para configurar e testar o logon único do Azure AD com o MBC (Mercer BenefitsCentral), você precisa completar os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do MBC (Mercer BenefitsCentral)](#create-a-mercer-benefitscentral-mbc-test-user)** - para ter um equivalente de Brenda Fernandes no MBC (Mercer BenefitsCentral (MBC) que está vinculado à representação do usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no seu aplicativo MBC (Mercer BenefitsCentral).

**Para configurar o logon único com o MBC (Mercer BenefitsCentral), execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **MBC (Mercer BenefitsCentral)**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_samlbase.png)

3. Na seção **Domínio e URLs do MBC (Mercer BenefitsCentral)** execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do MBC (Mercer BenefitsCentral)](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `stg.mercerhrs.com/saml2.0`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://ssous-stg.mercerhrs.com/SP2/Saml2AssertionConsumer.aspx`

    > [!NOTE] 
    > O valor da URL de Resposta não é real. Atualize esse valor com a URL de Resposta real. Contate a equipe de suporte do [ MBC (Mercer BenefitsCentral)](https://www.mercer.com/contact-us.html) para obter esse valor.

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_400.png)

6. Na seção de  **Configuração do MBC (Mercer BenefitsCentral)** , clique em **Configurar Mercer BenefitsCentral (MBC)** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do MBC (Mercer BenefitsCentral)](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_configure.png) 

7. Para configurar o logon único no **MBC (Mercer BenefitsCentral)**, você precisa enviar o **XML de Metadados** e a **URL do Serviço de Logon Único do SAML** baixados para a equipe de suporte do [MBC (Mercer BenefitsCentral)](https://www.mercer.com/contact-us.html). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-mercer-benefitscentral-mbc-test-user"></a>Crie um usuário do teste do MBC (Mercer BenefitsCentral)

Nesta seção, você cria um usuário chamado Brenda Fernandes no Mercer HRS. Trabalhe com a equipe de suporte do [ MBC (Mercer BenefitsCentral)](https://www.mercer.com/contact-us.html) para adicionar os usuários na plataforma Mercer HRS. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilita Brenda Fernandes a usar o logon único do Azure, concedendo acesso ao MBC (Mercer BenefitsCentral).

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao MBC (Mercer BenefitsCentral), execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione o **MBC (Mercer BenefitsCentral)**.

    ![Link do MBC (Mercer BenefitsCentral) na lista de Aplicativos](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do MBC (Mercer BenefitsCentral) no Painel de Acesso, você deverá obter o logon automaticamente para seu aplicativo MBC (Mercer BenefitsCentral).
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_203.png

