---
title: "Tutorial: Integração do Azure Active Directory com o Image Relay | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Image Relay."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 0bfbbaee7a74df6508584b7c8846fd07c2dc15c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Tutorial: Integração do Azure Active Directory com o Image Relay

Neste tutorial, você aprende a integrar o Image Relay ao Azure AD (Azure Active Directory).

A integração do Image Relay ao Azure AD proporciona os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Image Relay
- Você pode permitir que seus usuários façam logon automaticamente no Image Relay (Logon Único) com as contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Image Relay, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Image Relay

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Image Relay da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-image-relay-from-the-gallery"></a>Adicionar o Image Relay da galeria
Para configurar a integração do Image Relay ao Azure AD, você precisará adicionar o Image Relay da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Image Relay da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Image Relay**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_search.png)

5. No painel de resultados, selecione **Image Relay** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Image Relay com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Image Relay é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Image Relay.

No Image Relay, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Image Relay, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Como criar um usuário de teste do Image Relay](#creating-an-image-relay-test-user)** – para ter um equivalente de Brenda Fernandes no Image Relay que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Image Relay.

**Para configurar o logon único do Azure AD com o Image Relay, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Image Relay**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

3. Na seção **URLs e Domínio do Image Relay**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.imagerelay.com/`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com [equipe de suporte do cliente do Image Relay](http://support.imagerelay.com/) para obter esses valores. 
 


4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Image Relay**, clique em **Configurar Image Relay** para abrir a janela **Configurar logon**. Copie a **URL do Serviço de Logoff e a URL de Logon Único do SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_configure.png) 

7. Em outra janela do navegador, entre em seu site de empresa do Image Relay como administrador.

8. Na barra de ferramentas na parte superior, clique na carga de trabalho **Usuários e Permissões**.
   
    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

9. Clique em **Criar Nova Permissão**.
   
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)

10. Na carga de trabalho **Configurações de Logon Único**, marque a caixa de seleção **Este Grupo pode apenas entrar via logon único** e clique em **Salvar**.
   
    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

11. Vá para **Configurações da Conta**.
   
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

12. Vá para a carga de trabalho **Configurações de Logon Único** .
    
     ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

13. Na caixa de diálogo **Configurações de SAML** , execute as seguintes etapas:
    
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. Na caixa de texto **URL de Logon**, cole o valor da **URL de Serviço de Logon Único** que você copiou do Portal do Azure.

    b. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Serviço de Logoff Único** que você copiou do Portal do Azure.

    c. Em **Formato da Id de Nome**, selecione **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. Em **Opções de Vinculação para Solicitações do Provedor de Serviço (Image Relay)**, selecione **Associação POST**.

    e. Em **Certificado x.509**, clique em **Atualizar Certificado**.

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Abra o certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto Certificado x.509.

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Na seção **Provisionamento do Usuário Just-In-Time**, marque **Habilitar o Provisionamento do Usuário Just-In-Time**.

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selecione o grupo de permissão (por exemplo, **SSO Básico**) que tem permissão para entrar somente por meio de logon único.

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-an-image-relay-test-user"></a>Criar um usuário de teste do Image Relay

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Image Relay.

**Para criar um usuário chamado Brenda Fernandes no Image Relay, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Image Relay como administrador.

2. Acesse **Usuários e Permissões** e selecione **Criar Usuário SSO**.
   
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Insira o **Email**, o **Nome**, o **Sobrenome** e a **Empresa** do usuário que você deseja provisionar e selecione o grupo de permissões (por exemplo, SSO Básico), que é o grupo que pode entrar somente por meio de logon único.
   
    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

4. Clique em **Criar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Image Relay.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Image Relay, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Image Relay**.

    ![Configurar Logon Único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.    

Ao clicar no bloco Image Relay no Painel de Acesso, você deverá ser automaticamente conectado ao seu aplicativo Image Relay.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png

