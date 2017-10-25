---
title: "Tutorial: integração do Azure Active Directory com o LinkedIn Elevate | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o LinkedIn Elevate."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.openlocfilehash: 5336543e06d60be555722a615568b12048c2aa2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Tutorial: integração do Azure Active Directory com o LinkedIn Elevate

Neste tutorial, você aprenderá a integrar o LinkedIn Elevate ao Azure AD (Azure Active Directory).

A integração do LinkedIn Elevate com o Azure AD oferece os seguintes benefícios:

- É possível controlar, no Azure AD, quem tem acesso ao LinkedIn Elevate
- É possível permitir que seus usuários façam logon automaticamente no LinkedIn Elevate (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o LinkedIn Elevate, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do LinkedIn Elevate habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o LinkedIn da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Adicionando o LinkedIn da galeria
Para configurar a integração do LinkedIn Elevate ao Azure AD, é necessário adicionar o LinkedIn Elevate por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o LinkedIn Elevate da galeria, siga as etapas abaixo:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **LinkedIn Elevate**. No painel de resultados, clique em **LinkedIn Elevate** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o LinkedIn Elevate, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do LinkedIn Elevate é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LinkedIn Elevate.

Essa relação de vínculo é estabelecida por meio da atribuição do valor de **nome de usuário** no Azure AD como o valor de **Nome de Usuário** no LinkedIn Elevate.

Para configurar e testar o logon único do Azure AD com o LinkedIn Elevate, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do LinkedIn Elevate](#creating-a-linkedin-elevate-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal de Gerenciamento do Azure e configurará o logon único em seu aplicativo LinkedIn Elevate.

**Para configurar o logon único do Azure AD com o LinkedIn Elevate, siga as etapas abaixo:**

1. No portal de Gerenciamento do Azure, na página de integração de aplicativos do **LinkedIn Elevate**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedin_01.png)

3. Em uma janela diferente do navegador da Web, faça logon em seu locatário do LinkedIn Elevate como um administrador.

4. Em **Centro de Contas**, clique em **Configurações Globais** em **Configurações**. Além disso, selecione **Elevar – Elevar teste do AAD** na lista suspensa.

    ![Configurar Logon Único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_01.png)

5. Clique em **OU Clique aqui para carregar e copiar campos individuais do formulário** e copie a **ID da entidade** e **URL ACS (acesso do consumidor de declaração)**

    ![Configurar Logon Único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_03.png)

6. No Portal do Azure, em **Domínio e URLs do LinkedIn Elevate**, siga as etapas abaixo para configurar o SSO no modo **iniciado pelo IdP**

    ![Configurar Logon Único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_01.png)

    a. Na caixa de texto **Identificador**, insira a **ID da Entidade** copiada do Portal do LinkedIn 

    b. Na caixa de texto **URL de resposta**, insira a **URL ACS (acesso do consumidor de declaração)** copiada do Portal do LinkedIn

7. Se você desejar configurar o SSO em **Iniciado pelo SP**, clique na opção Mostrar configurações avançadas de URL na seção de configuração e configure o logon na URL com o seguinte padrão:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 
    
    ![Configurar Logon Único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_02.png) 
    
8. Seu aplicativo LinkedIn Elevate espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **identificador de usuário** é **user.userprincipalname**, mas o LinkedIn Elevate espera que isso seja mapeado com o endereço de email do usuário. Para que você possa usar o atributo **user. mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização. 

    ![Configurar Logon Único](./media/active-directory-saas-linkedinElevate-tutorial/updateusermail.png)

9. Na seção **Atributos de Usuário**, clique em **Exibir e editar todos os outros atributos de usuário** e defina os atributos. É necessário adicionar outra declaração denominada **departamento** e o valor deve ser mapeado para **user.department**.

    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | department| user.department |

      ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-linkedinElevate-tutorial/userattribute.png)

      a. Clique em Adicionar atributo para abrir a página de detalhes do atributo. Adicione o atributo department conforme mostrado abaixo.

      ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-linkedinElevate-tutorial/adduserattribute.png)

      b. Clique em **Ok** para salvar o atributo.

      c. Altere o nome do atributo **emailaddress** para **email**.


10. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_certificate.png) 

11. Clique em **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_400.png)

12. Vá para a seção **Configurações de administração do LinkedIn**. Carregue o arquivo XML que você acabou de baixar no portal do Azure clicando na opção Carregar arquivo XML.

    ![Configurar Logon Único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_metadata_03.png)

13. Clique em **Ativar** para habilitar o SSO. O status do SSO será alterado de **Não conectado** para **Conectado**

    ![Configurar Logon Único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 

### <a name="creating-a-linkedin-elevate-test-user"></a>Criando um usuário de teste do LinkedIn Elevate

O aplicativo Linked Elevate dá suporte ao provisionamento de usuário just-in-time e, após a autenticação, os usuários serão automaticamente criados no aplicativo. Na página de configurações de administração no portal do LinkedIn Elevate, coloque a opção **Atribuir licenças automaticamente** como ativa para habilitar o provisionamento just-in-time e isso também atribuirá uma licença ao usuário.

   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-linkedinElevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao LinkedIn Elevate.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao LinkedIn Elevate, siga as etapas abaixo:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **LinkedIn Elevate**.

    ![Configurar Logon Único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_0001.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco LinkedIn Elevate no Painel de Acesso, você deve obter a página de logon do Azure e, após o logon bem-sucedido, você deve entrar em seu aplicativo LinkedIn Elevate.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutorial: Configurar o LinkedIn Elevate para o provisionamento automático de usuário com o Azure Active Directory](active-directory-saas-linkedinelevate-provisioning-tutorial.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_203.png
