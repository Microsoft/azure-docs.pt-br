---
title: "Tutorial: integração do Azure Active Directory com o Zscaler One | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler One."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 7d655c482a16c991a819eec84c84556d2f288a75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Tutorial: integração do Azure Active Directory com o Zscaler One

Neste tutorial, você aprenderá como integrar o Zscaler One ao Azure AD (Azure Active Directory).

A integração do Zscaler One com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Zscaler One
- É possível permitir que seus usuários entrem automaticamente no Zscaler One (Logon Único) com as contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler One, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Zscaler One habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Zscaler One da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-zscaler-one-from-the-gallery"></a>Adicionar o Zscaler One da galeria
Para configurar a integração do Zscaler One ao Azure AD, é necessário adicionar o Zscaler One da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler One da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Zscaler One**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_search.png)

5. No painel de resultados, selecione **Zscaler One** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Zscaler One com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Zscaler One é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Zscaler One.

No Zscaler One, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Zscaler One, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Definir configurações de proxy](#configuring-proxy-settings)** – para definir as configurações de proxy no Internet Explorer
3. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Zscaler One](#creating-a-zscaler-one-test-user)** – para ter um equivalente de Brenda Fernandes no Zscaler One que esteja vinculado à representação de usuário no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
6. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo Zscaler One.

**Para configurar o logon único do Azure AD com o Zscaler One, siga as etapas abaixo:**

1. No Portal do Azure, na página de integração de aplicativos do **Zscaler One**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_samlbase.png)

3. Na seção **URLs e Domínio do Zscaler One**, siga as etapas abaixo:

    ![Configurar Logon Único](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_url.png)

    Na caixa de texto URL de Entrada, digite a URL usada pelos usuários para entrar no aplicativo Zscaler One.

    > [!NOTE] 
    > Você precisa atualizar esse valor com a URL de Entrada real. Contate a [equipe de suporte ao cliente do Zscaler One](https://www.zscaler.com/company/contact) para obter esses valores.

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Zscaler One**, clique em **Configurar o Zscaler One** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_configure.png) 

7. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zscaler One como administrador.

8. No menu na parte superior, clique em **Administração**.
   
    ![Administração](./media/active-directory-saas-zscaler-one-tutorial/ic800206.png "Administração")

9. Em **Gerenciar Administradores e Funções**, clique em **Gerenciar Usuários e Autenticação**.   
            
    ![Gerenciar usuários e autenticação](./media/active-directory-saas-zscaler-one-tutorial/ic800207.png "Gerenciar usuários e autenticação")

10. Na seção **Escolher Opções de Autenticação para a sua Organização** , realize as seguintes etapas:   
                
    ![Autenticação](./media/active-directory-saas-zscaler-one-tutorial/ic800208.png "Autenticação")
   
    a. Selecione **Autenticar usando o Logon Único do SAML**.

    b. Clique em **Configurar Parâmetros de Logon Único do SAML**.

11. Na página da caixa de diálogo **Configurar Parâmetros de Logon Único do SAML**, execute as seguintes etapas e, em seguida, clique em **Concluído**

    ![Logon Único](./media/active-directory-saas-zscaler-one-tutorial/ic800209.png "Logon Único")
    
    a. Cole o valor **URL do Serviço de Logon Único SAML**, copiado do Portal do Azure para a caixa de texto **URL do Portal SAML ao qual os usuários são enviados para autenticação**.
    
    b. Na caixa de texto **Atributo que contém o Nome de Logon**, digite **NameID**.
    
    c. Para carregar seu certificado baixado, clique em **Zscaler pem**.
    
    d. Selecione **Habilitar Provisionamento Automático do SAML**.

12. Na página de caixa de diálogo **Configurar Autenticação de Usuário** , execute as seguintes etapas:

    ![Administração](./media/active-directory-saas-zscaler-one-tutorial/ic800210.png "Administração")
    
    a. Clique em **Salvar**.

    b. Clique em **Ativar Agora**.

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet**.   
    
     ![Opções da Internet](./media/active-directory-saas-zscaler-one-tutorial/ic769492.png "Opções da Internet")

3. Clique na guia **Conexões** .   
  
     ![Conexões](./media/active-directory-saas-zscaler-one-tutorial/ic769493.png "Conexões")

4. Clique em **Configurações da LAN** para abrir a caixa de diálogo **Configurações da LAN**.

5. Na seção Servidor de proxy, execute as seguintes etapas:   
   
    ![Servidor proxy](./media/active-directory-saas-zscaler-one-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Usar um servidor proxy para LAN**.

    b. Na caixa de texto Endereço, digite **gateway.zscalerone.net**.

    c. Na caixa de texto Porta, digite **80**.

    d. Selecione **Ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a caixa de diálogo **Configurações da Rede Local (LAN)**.

6. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscaler-one-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-zscaler-one-test-user"></a>Criação de um usuário de teste do Zscaler One

Para permitir que os usuários do Azure AD façam logon no Zscaler One, eles devem ser provisionados no Zscaler One. No caso do Zscaler One, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. Faça logon em seu locatário do **ZScaler One** .

2. Clique em **Administração**.   
   
    ![Administração](./media/active-directory-saas-zscaler-one-tutorial/ic781035.png "Administração")

3. Clique em **Gerenciamento de Usuários**.   
        
     ![Adicionar](./media/active-directory-saas-zscaler-one-tutorial/ic781036.png "Adicionar")

4. Na guia **Usuários**, clique em **Adicionar**.
      
    ![Adicionar](./media/active-directory-saas-zscaler-one-tutorial/ic781037.png "Adicionar")

5. Na seção Adicionar Usuário, execute as seguintes etapas:
        
    ![Adicionar Usuário](./media/active-directory-saas-zscaler-one-tutorial/ic781038.png "Adicionar Usuário")
   
    a. Digite **UserID**, **Nome de Exibição do Usuário**, **Senha**, **Confirmar Senha** e, em seguida, selecione **Grupos** e o **Departamento** de uma conta válida do Azure AD que você deseja provisionar.

    b. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação de conta de usuário do Zscaler One ou APIs fornecidas pelo Zscaler One para provisionar as contas de usuário do Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Zscaler One.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Zscaler One, siga as etapas abaixo:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Zscaler One**.

    ![Configurar Logon Único](./media/active-directory-saas-zscaler-one-tutorial/tutorial_zscalerone_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Zscaler One no Painel de Acesso, você deverá entrar automaticamente no aplicativo Zscaler One.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-one-tutorial/tutorial_general_203.png

