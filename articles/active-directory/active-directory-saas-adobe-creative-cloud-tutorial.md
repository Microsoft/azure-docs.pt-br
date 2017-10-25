---
title: "Tutorial: integração do Azure Active Directory com a Adobe Creative Cloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e a Adobe Creative Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: ad2c3999ac8bfc8dfe0fc662ee37b41fff38444f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Tutorial: integração do Azure Active Directory com a Adobe Creative Cloud

Neste tutorial, você aprenderá a integrar a Adobe Creative Cloud ao Azure AD (Azure Active Directory).

A integração da Adobe Creative Cloud ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso à Adobe Creative Cloud.
- É possível permitir que seus usuários façam logon automaticamente na Adobe Creative Cloud (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a Adobe Creative Cloud, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura da Creative Cloud habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Adobe Creative Cloud da Galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adicionando o Adobe Creative Cloud da Galeria
Para configurar a integração da Adobe Creative Cloud com o Azure AD, será necessário adicionar a Creative Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a Adobe Creative Cloud da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Adobe Creative Cloud**, selecione **Adobe Creative Cloud** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adobe Creative Cloud na lista de resultados](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com a Adobe Creative Cloud, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário da Adobe Creative Cloud é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado da Adobe Creative Cloud.

Na Adobe Creative Cloud, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com a Adobe Creative Cloud, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste da Adobe Creative Cloud](#create-an-adobe-creative-cloud-test-user)** – para ter um equivalente de Brenda Fernandes na Adobe Creative Cloud que esteja vinculado à representação da usuária no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único em seu aplicativo Adobe Creative Cloud.

**Para configurar o logon único do Azure AD com a Adobe Creative Cloud, siga as etapas abaixo:**

1. No portal do Azure, na página de integração de aplicativos do **Adobe Creative Cloud**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. Na seção **Domínio e URLs da Adobe Creative Cloud**, se você desejar configurar o aplicativo em modo iniciado pelo IDP, siga as etapas abaixo:

    ![Informações de logon único em Domínio e URLs da Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://www.okta.com/saml2/service-provider/<token>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao Cliente da Adobe Creative Cloud](https://helpx.adobe.com/in/contact/support.html) para obter esses valores. 

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único em Domínio e URLs da Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    Na caixa de texto **URL de Logon**, digite o valor como: `https://adobe.com`

5. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. O aplicativo Adobe Creative Cloud espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Gerencie os valores desses atributos na guia **Atributo de Usuário** do aplicativo. A captura de tela a seguir mostra um exemplo disso.

    ![Configurar Logon Único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:

    | Nome do atributo | Valor do atributo |
    | ---------------| ----------------|
    | Nome |user.givenname |
    | Sobrenome |user.surname |
    | Email |user.mail |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.
    
    ![Configurar o logon único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)
    
    ![Configurar Logon Único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.
    
8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)
    
9. Na seção **Configuração do Adobe Creative Cloud**, clique em **Configurar a Adobe Creative Cloud** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida**.

    ![Configuração da Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)
    
10. Em uma janela diferente do navegador da Web, faça logon em seu locatário da Adobe Creative Cloud como administrador.

11. Vá para **Identidade** no painel de navegação esquerdo e clique em seu domínio. Em seguida, siga as etapas na seção **Configuração do logon único necessária**.

    ![Configurações](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "Configurações")
    
    a. Clique em **Procurar** para carregar o certificado baixado do Azure AD para o **Certificado do IdP**.
    
    b. Na caixa de texto **Emissor IdP**, coloque o valor da **ID da entidade SAML** que você copiou da seção **Configurar logon** no portal do Azure.
    
    c. Na caixa de texto **URL de logon IdP**, coloque o valor da **URL de serviço do SAML SSO** que você copiou da seção **Configurar logon** no portal do Azure.
    
    d. Selecione **Redirecionamento HTTP** como **Associação IdP**.
    
    e. Selecione **Endereço de email** como **Configuração de logon do usuário**.
    
    f. Clique no botão **Salvar** .

12. Agra o painel apresentará o arquivo XML **"Baixar metadados"**. Ele contém a URL EntityDescriptor e a URL AssertionConsumerService da Adobe. Abra o arquivo e configure-os no aplicativo Azure AD.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Use o valor EntityDescriptor que a Adobe forneceu a você para **Identificador** na caixa de diálogo **Definir configurações de aplicativo**.

    b. Use o valor AssertionConsumerService que a Adobe forneceu a você para **URL de resposta** na caixa de diálogo **Definir configurações de aplicativo**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Criar um usuário de teste da Adobe Creative Cloud

Para que os usuários do Azure AD façam logon na Adobe Creative Cloud, eles devem ser provisionados na Adobe Creative Cloud. No caso da Adobe Creative Cloud, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa da Adobe Creative Cloud como administrador.

2. Adicione o usuário no console da Adobe como ID Federada e o atribua a um grupo para autorização

    ![O link da Adobe Creative Cloud na lista de aplicativos](./media/active-directory-saas-adobe-creative-cloud-tutorial/users.png)  

3. Neste ponto, digite seu endereço de email/upn no formulário signin da Adobe, pressione tab e você deve ser federado de volta ao Azure AD:
    * Acesso via Web: www.adobe.com > sign-in
    * No utilitário de aplicativo de área de trabalho > entrar
    * No aplicativo > ajuda > entrar

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo acesso à Adobe Creative Cloud.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes à Adobe Creative Cloud, siga as etapas abaixo:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Adobe Creative Cloud**.

    ![O link da Adobe Creative Cloud na lista de aplicativos](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Adobe Creative Cloud no Painel de Acesso, seu logon deverá ser feito automaticamente no aplicativo Adobe Creative Cloud.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png

