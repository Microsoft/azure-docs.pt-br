---
title: "Tutorial: Integração do Azure Active Directory ao SAP Business ByDesign | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o SAP Business ByDesign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: ab76a0ac1ef954efd3c66e6f565514b889ed9444
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: integração do Azure Active Directory ao SAP Business ByDesign

Neste tutorial, você aprenderá a integrar o SAP Business ByDesign ao Azure AD (Azure Active Directory).

A integração do SAP Business ByDesign ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao SAP Business ByDesign.
- Você pode permitir que os usuários façam logon automaticamente no SAP Business ByDesign (Logon Único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP Business ByDesign, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do SAP Business ByDesign habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o SAP Business ByDesign da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Adicionar o SAP Business ByDesign da galeria
Para configurar a integração do SAP Business ByDesign ao Azure AD, você precisará adicionar o SAP Business ByDesign da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP Business ByDesign da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SAP Business ByDesign**, selecione **SAP Business ByDesign** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SAP Business ByDesign na lista de resultados](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SAP Business ByDesign, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SAP Business ByDesign é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no SAP Business ByDesign.

No SAP Business ByDesign, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o SAP Business ByDesign, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)** – para ter um equivalente de Brenda Fernandes no SAP Business ByDesign vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo SAP Business ByDesign.

**Para configurar o logon único do Azure AD com o SAP Business ByDesign, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **SAP Business ByDesign**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

3. Na seção **Domínio e URLs do SAP Business ByDesign**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<servername>.sapbydesign.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para obter esses valores.

4. Na seção **Atributos de Usuário**, realize as seguintes etapas:

    ![Seção Atributo do SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. Na lista **Identificador de Usuário**, selecione a função **ExtractMailPrefix()**.
    
    b. Na lista de **Email** , selecione o atributo de usuário que você deseja usar na implementação. Por exemplo, se você quiser usar EmployeeID como identificador exclusivo de usuário e tiver armazenado o valor do atributo em ExtensionAttribute2, selecione user.extensionattribute2.     

5. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_400.png)

7. Na seção **Configuração do SAP Business ByDesign** do Portal do Azure, clique em **Configurar o SAP Business ByDesign** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

8. Para que o SSO seja configurado para o aplicativo, execute as seguintes etapas:
   
    a. Faça logon no portal do SAP Business ByDesign com direitos de administrador.
   
    b. Navegue até **Tarefa Comum de Gerenciamento de Aplicativos e Usuários** e clique na guia **Provedor de Identidade**.
   
    c. Clique em **Novo Provedor de Identidade** e selecione o arquivo XML de metadados baixado no Portal do Azure. Com a importação dos metadados, o sistema carrega automaticamente o certificado de assinatura e o certificado de criptografia necessários.
   
    ![Configurar Logon Único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Para incluir a **URL de Serviço de Consumidor de Declaração** na solicitação SAML, selecione **Incluir URL de Serviço de Consumidor de Declaração**.
   
    e. Clique em **Ativar Logon Único**.
   
    f. Salve suas alterações.
   
    g. Clique na guia **Meu Sistema** .
   
    ![Configurar Logon Único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Cole a **URL do Serviço de Logon Único SAML** copiado do Portal do Azure na caixa de texto **URL de Logon do Azure AD**.
   
    ![Configurar Logon Único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Especifique se o funcionário pode escolher manualmente entre fazer logon com uma ID de usuário e senha ou SSO, selecionando **Seleção de Provedor de Identidade Manual**.
   
    j. Na seção **URL de SSO** , especifique a URL que deve ser usada pelo funcionário para fazer logon no sistema. 
    Na lista suspensa URL Enviada ao Funcionário, você pode escolher entre as seguintes opções:
   
    **URL não SSO**
   
    O sistema envia apenas a URL normal do sistema ao funcionário. O funcionário não pode fazer logon usando o SSO e deve usar uma senha ou um certificado em vez disso.
   
    **URL de SSO** 
   
    O sistema envia apenas a URL de SSO ao funcionário. O funcionário pode fazer logon usando o SSO. A solicitação de autenticação é redirecionada por meio do IdP.
   
    **Seleção Automática**
   
    Se o SSO não estiver ativo, o sistema enviará a URL normal do sistema ao funcionário. Se o SSO estiver ativo, o sistema verificará se o funcionário tem uma senha. Se uma senha estiver disponível, a URL de SSO e a URL não SSO serão enviadas ao funcionário. No entanto, se o funcionário não tiver uma senha, apenas a URL de SSO será enviada ao funcionário.
   
    k. Salve suas alterações.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Criar um usuário de teste do SAP Business ByDesign

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SAP Business ByDesign. Trabalhe com a [equipe de suporte ao cliente do SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para adicionar os usuários à plataforma SAP Business ByDesign. 

> [!NOTE]
> Verifique se o valor de NameID corresponde ao campo de nome de usuário na plataforma SAP Business ByDesign.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SAP Business ByDesign.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao SAP Business ByDesign, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SAP Business ByDesign**.

    ![O link do SAP Business ByDesign na lista de Aplicativos](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SAP Business ByDesign no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo SAP Business ByDesign.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png

