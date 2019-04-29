---
title: 'Tutorial: Integração do Azure Active Directory com a Área Restrita do Salesforce | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6794d7eaccb488bb345227161f0bca02f14bc518
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104929"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Azure Active Directory com a Área Restrita Salesforce

Neste tutorial, você aprende a integrar o Salesforce Sandbox ao Azure AD (Azure Active Directory).

As áreas restritas oferecem a capacidade de criar várias cópias da sua organização em ambientes separados por uma variedade de finalidades, como desenvolvimento, testes e treinamento, sem comprometer os dados e os aplicativos em sua organização de produção do Salesforce.
Para obter mais detalhes, confira  [Visão geral da Área Restrita](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

A integração do Salesforce Sandbox ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso à Área Restrita Salesforce.
- Você pode permitir que os usuários se conectem automaticamente à Área Restrita Salesforce (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Salesforce Sandbox, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único na Área Restrita Salesforce

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Salesforce Sandbox por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionando o Salesforce Sandbox por meio da galeria

Para configurar a integração do Salesforce Sandbox ao Azure AD, é necessário adicionar o Salesforce Sandbox à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Salesforce Sandbox por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Área Restrita Salesforce**, selecione **Área Restrita Salesforce** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Área Restrita Salesforce na lista de resultados](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com a Área Restrita Salesforce, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Salesforce Sandbox é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Salesforce Sandbox.

Na Área Restrita Salesforce, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Salesforce Sandbox, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste da Área Restrita Salesforce](#create-a-salesforce-sandbox-test-user)** – para ter um equivalente de Brenda Fernandes na Área Restrita Salesforce vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Salesforce Sandbox.

**Para configurar o logon único do Azure AD com o Salesforce Sandbox, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Salesforce Sandbox**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Clique em **Alterar modo de logon único** na parte superior da tela para selecionar o modo **SAML**.

      ![Link Configurar logon único](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Link Configurar logon único](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.
   
    ![Link Configurar logon único](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

     a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > Você obterá o arquivo de metadados do provedor de serviços no portal de administração do Salesforce Sandbox, explicado posteriormente no tutorial.

    c. Depois que o arquivo de metadados for enviado, o **URL de resposta** será preenchido automaticamente na caixa de texto **URL de resposta**.

    ![Informações sobre logon único de URLs e Domínio da a Área Restrita Salesforce](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. Sobre o **certificado de autenticação SAML** seção, clique em **baixar** para fazer o dowload**XML de metadados de Federação** e, em seguida, salve o arquivo xml em seu computador.

    ![O link de download do Certificado](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Abra uma nova guia no navegador e faça logon em sua conta Administrador do Salesforce Sandbox.

8. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure1.png)

9. Role para baixo até **CONFIGURAÇÕES** no painel de navegação esquerdo e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure3.png)

11. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Para configurar as configurações de logon único do SAML, clique em **Novo do arquivo de metadados**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Clique em **Escolher Arquivo** para carregar o arquivo XML de metadados que você baixou do portal do Azure e clique em **Criar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. Na página **Configurações de logon único do SAML**, os campos são preenchidos automaticamente, clique em Salvar.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. Na página **Configurações de Logon Único**, clique no botão **Baixar Metadados** para baixar o arquivo de metadados do provedor de serviços. Usar esse arquivo a **básicas de configuração de SAML** seção no portal do Azure para configurar as URLs necessárias, conforme explicado acima.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure4.png)

16. Se você desejar configurar o aplicativo no modo iniciado **SP**, os pré-requisitos disso estão a seguir:

     a. Você deve ter um domínio verificado.

    b. Você precisa configurar e habilitar seu domínio na Área Restrita do Salesforce, as etapas para isso serão explicadas posteriormente neste tutorial.

    c. No portal do Azure, sobre o **básicas de configuração de SAML** seção, clique em **definir URLs adicionais** e execute a seguinte etapa:
  
    ![Informações sobre logon único de URLs e Domínio da a Área Restrita Salesforce](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Esse valor deve ser copiado do portal da Área Restrita Salesforce depois que você tiver habilitado o domínio.

17. Sobre o **certificado de autenticação SAML** seção, clique em **XML de metadados de Federação** e, em seguida, salve o arquivo xml em seu computador.

    ![O link de download do Certificado](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Abra uma nova guia no navegador e faça logon em sua conta Administrador do Salesforce Sandbox.

19. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure1.png)

20. Role para baixo até **CONFIGURAÇÕES** no painel de navegação esquerdo e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure3.png)

22. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Para configurar as configurações de logon único do SAML, clique em **Novo do arquivo de metadados**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Clique em **Escolher arquivo** para carregar o arquivo XML de metadados e clique em **criar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. Na página **Configurações do SAML Single Sign-On**, em que os campos são preenchidos automaticamente, digite o nome da configuração (por exemplo: *SPSSOWAAD_Test*) Na caixa de texto **Nome** e clique em Salvar.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Para habilitar seu domínio na Área Restrita Salesforce, execute as etapas a seguir:

    > [!NOTE]
    > Antes de habilitar o domínio, você precisará criar o mesmo na Área Restrita Salesforce. Para obter mais informações, consulte [Definindo o nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Depois que o domínio for criado, certifique-se de que ele esteja configurado corretamente.

    * No painel de navegação à esquerda na Área Restrita Salesforce, clique em **Configurações da Empresa** para expandir a seção correspondente e clique em **Meu Domínio**.

         ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * Na seção **Configuração de Autenticação**, clique em **Editar**.

        ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * Na seção **Configuração de Autenticação**, como **Serviço de Autenticação**, selecione o nome da Configuração de Logon Único do SAML que você definiu durante a configuração de SSO na Área Restrita Salesforce e clique em **Salvar**.

        ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Criar um usuário de teste da Área Restrita Salesforce

Nesta seção, um usuário chamado Brenda Fernandes é criado no Salesforce Sandbox. O Salesforce Sandbox dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Salesforce Sandbox, um novo será criado quando você tentar acessar o Salesforce Sandbox. A área restrita do Salesforce também dá suporte ao provisionamento automático de usuário. É possível encontrar [aqui](salesforce-sandbox-provisioning-tutorial.md) detalhes de como configurar o provisionamento automático de usuário.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Salesforce Sandbox.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Salesforce Sandbox, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Salesforce Sandbox**.

    ![O link da Área Restrita Salesforce na lista de Aplicativos](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar usuário**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco da Área Restrita Salesforce no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Área Restrita Salesforce.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Provisionamento de Usuário](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
