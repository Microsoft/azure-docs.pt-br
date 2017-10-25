---
title: "Tutorial: integração do Azure Active Directory com o M-Files | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o M-Files."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 0f2682cf7cd3e11a5a7156938fbe9d4c7f541312
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Tutorial: integração do Azure Active Directory com o M-Files

Neste tutorial, você aprenderá a integrar o M-Files ao Azure AD (Azure Active Directory).

A integração do M-Files ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao M-Files
- Você pode permitir que os usuários entrem automaticamente no M-Files (Logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o M-Files, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do M-Files

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o M-Files da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-m-files-from-the-gallery"></a>Adicionando o M-Files da galeria
Para configurar a integração do M-Files ao Azure AD, você precisará adicionar o M-Files da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o M-Files da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **M-Files**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_search.png)

5. No painel de resultados, selecione **M-Files** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o M-Files, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do M-Files é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do M-Files.

No M-Files, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o M-Files, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usem esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do M-Files](#creating-a-m-files-test-user)**: para ter um equivalente de Brenda Fernandes no M-Files que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo M-Files.

**Para configurar o logon único do Azure AD com o M-Files, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **M-Files**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_samlbase.png)

3. Na seção **URLs e Domínio do M-Files**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente M-Files](mailto:support@m-files.com) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-m-files-tutorial/tutorial_general_400.png)

6. Para que o SSO seja configurado para o aplicativo, entre em contato com a [equipe de suporte do M-Files](mailto:support@m-files.com) e forneça os metadados baixados.
   
    >[!NOTE]
    >Siga as próximas etapas se quiser configurar o SSO para o aplicativo da área de trabalho do M-Files. Nenhuma etapa adicional é necessária se você quer apenas configurar o SSO para versão Web do M-Files.  

7. Siga as próximas etapas para configurar o aplicativo da área de trabalho do M-Files a fim de habilitar o SSO com o Azure AD. Para baixar o M-Files, vá para a página [Baixar M-Files](https://www.m-files.com/en/download-latest-version).

8. Abra a janela **Configurações da área de trabalho do M-Files**. Em seguida, clique em **Adicionar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)

9. Na janela **Propriedades de Conexão do Cofre de Documentos**, execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)  

    No tipo de seção Servidor, os valores são os seguintes:  

    a. Para **Nome**, digite `<tenant-name>.cloudvault.m-files.com`. 
 
    b. Para **Número de Porta**, digite **4466**. 

    c. Para **Protocolo**, selecione **HTTPS**. 

    d. No campo **Autenticação**, selecione **Usuário específico do Windows**. Em seguida, você verá uma página de entrada. Insira suas credenciais do Azure AD. 

    e. Para o **Cofre no Servidor**, selecione o cofre correspondente no servidor.
 
    f. Clique em **OK**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-m-files-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-m-files-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-m-files-test-user"></a>Criar um usuário de teste do M-Files

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no M-Files. Trabalhe com a [equipe de suporte do M-Files](mailto:support@m-files.com) para adicionar os usuários no M-Files.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao M-Files.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao M-Files, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **M-Files**.

    ![Configurar Logon Único](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco M-Files no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo M-Files.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png

