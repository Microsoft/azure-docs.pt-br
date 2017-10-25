---
title: "Tutorial: Integração do Azure Active Directory ao Google Apps no Azure | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 065841d6b4fe50e953f01bba4d3f23de82b82726
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>Tutorial: integração do Azure Active Directory ao Google Apps

Neste tutorial, você aprende a integrar o Google Apps ao Azure AD (Azure Active Directory).

A integração do Google Apps ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Google Apps
- É possível permitir que os usuários se conectem automaticamente ao Google Apps (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Caso deseje obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Google Apps, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Google Apps

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="video-tutorial"></a>Tutorial em vídeo
Como habilitar o logon único no Google Apps em dois minutos:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]

## <a name="frequently-asked-questions"></a>Perguntas frequentes
1. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com o logon único do AD do AD do Azure?**
   
    R: Sim. Os usuários podem entrar em seus dispositivos Chromebook usando suas credenciais do Azure AD. Confira este [artigo de suporte do Google Apps](https://support.google.com/chrome/a/answer/6060880) para saber mais sobre o motivo de os usuários receberem uma solicitação por credenciais duas vezes.

2. **P: Se eu habilitar o logon único, os usuários conseguirão usar suas credenciais do Azure AD para entrar em qualquer produto do Google, como Google Sala de aula, GMail, Google Drive, YouTube e assim por diante?**
   
    R: Sim, dependendo de [quais aplicativos do Google](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) você optar por habilitar ou desabilitar para sua organização.

3. **P: Posso habilitar o logon único apenas para um subconjunto de meus usuários do Google Apps?**
   
    R: Não. A ativação do logon único exige imediatamente que todos os usuários do Google Apps se autentiquem com suas credenciais do Azure AD. Como o Google Apps não oferece suporte a vários provedores de identidade, o provedor de identidade para seu ambiente do Google Apps pode ser o AD do Azure ou o Google, mas não ambos ao mesmo tempo.

4. **P: Se um usuário se conectar por meio do Windows, ele será autenticado automaticamente no Google Apps sem que uma senha seja solicitada?**
   
    R: Há duas opções para este cenário. Primeiro, os usuários podem entrar em dispositivos com Windows 10 por meio do [Ingresso no Active Directory do Azure](active-directory-azureadjoin-overview.md). Como alternativa, os usuários podem entrar em dispositivos com Windows que ingressaram em um domínio para um Active Directory local com logon único habilitado no AD do Azure por meio de uma implantação dos [Serviços de Federação do Active Directory (AD FS)](active-directory-aadconnect-user-signin.md) . Ambas as opções exigem que você realize as etapas no tutorial a seguir para habilitar o logon único entre o Azure AD e o Google Apps.

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Google Apps por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-google-apps-from-the-gallery"></a>Adicionando o Google Apps por meio da galeria
Para configurar a integração do Google Apps ao Azure AD, é necessário adicionar o Google Apps à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Google Apps por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Google Apps**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_search.png)

5. No painel de resultados, selecione **Google Apps** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Google Apps, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Google Apps é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Google Apps.

Essa relação de vínculo é estabelecida com a atribuição do valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Google Apps.

Para configurar e testar o logon único do Azure AD com o Google Apps, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Google Apps](#creating-a-google-apps-test-user)** – para ter um equivalente de Brenda Fernandes no Google Apps que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Google Apps.

**Para configurar o logon único do Azure AD com o Google Apps, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Google Apps**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_samlbase.png)

3. Na seção **Domínio e URLs do Google Apps**, realize as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://mail.google.com/a/<yourdomain>`

    > [!NOTE] 
    > Esse valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte do Google](https://www.google.com/contact/).
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado** e, em seguida, salve o certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-google-apps-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Google Apps**, clique em **Configurar o Google Apps** para abrir a janela **Configurar logon**. Copie a **URL de Saída, a URL do Serviço de Logon Único SAML e a URL de Alteração de Senha** da **seção Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_configure.png) 

7. Abra uma nova guia no navegador e entre no [Console de Admin do Google Apps](http://admin.google.com/) usando sua conta de administrador.

8. Clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles** , na parte inferior da tela.
   
    ![Clique em Segurança.][10]

9. Na página **Segurança**, clique em **Configurar logon único (SSO).**
   
    ![Clique em SSO.][11]

10. Realize as seguintes alterações de configuração:
   
    ![Configure o SSO][12]
   
    a. Selecione **Configurar SSO com um provedor de identidade de terceiros**.

    b. No campo **URL da página de entrada** do Google Apps, cole o valor da **URL do Serviço de Logon Único** copiado do portal do Azure.

    c. No campo **URL da página de saída** do Google Apps, cole o valor da **URL de Saída** copiado do portal do Azure. 

    d. No campo **URL de Alteração de Senha** do Google Apps, cole o valor da **URL de alteração de senha** copiado do portal do Azure. 

    e. No Google Apps, para o **Certificado de verificação**, carregue o certificado baixado no portal do Azure.

    f. Clique em **Salvar Alterações**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-google-apps-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-google-apps-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-google-apps-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-google-apps-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-google-apps-test-user"></a>Criando um usuário de teste do Google Apps

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Google Apps Software. O Google Apps dá suporte ao provisionamento automático, que está habilitado por padrão. Não há nenhuma ação para você nesta seção. Se um usuário ainda não existir no Google Apps Software, um novo será criado quando você tentar acessar o Google Apps Software.

>[!NOTE] 
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Google](https://www.google.com/contact/).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Google Apps.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Google Apps, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Google Apps**.

    ![Configurar Logon Único](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, para testar as configurações de logon único, abra o Painel de Acesso em [https://myapps.microsoft.com](active-directory-saas-access-panel-introduction.md) e, depois, entre na conta de teste e clique no bloco **Google Apps** no Painel de Acesso.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Provisionamento de Usuário](active-directory-saas-google-apps-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_203.png

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png

[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png