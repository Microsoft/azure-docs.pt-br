---
title: 'Tutorial: Integração do Azure Active Directory com o SSO de SAML para o JIRA da Microsoft | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do SAML para o JIRA da Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80f6cdea0c4e68f2ddbbe4f50989a34452a048c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099617"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft"></a>Tutorial: Integração do Azure Active Directory com o SSO de SAML para o JIRA da Microsoft

Neste tutorial, você aprende a integrar o SSO do SAML para o JIRA da Microsoft ao Azure AD (Azure Active Directory).
A integração do SSO do SAML para o JIRA da Microsoft ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao SSO do SAML para o JIRA da Microsoft.
* Você pode permitir que os usuários sejam conectados automaticamente ao SSO de SAML para o JIRA da Microsoft (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="description"></a>DESCRIÇÃO

Use sua conta do Microsoft Azure Active Directory com o servidor do Atlassian JIRA para habilitar o logon único. Dessa forma, todos os usuários de sua organização podem usar as credenciais do Azure AD para entrar no aplicativo JIRA. Este plug-in usa o SAML 2.0 para federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SSO do SAML para o JIRA da Microsoft, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- O JIRA Core e o Software 6.4 a 8.0 ou o JIRA Service Desk 3.0 a 3.5 devem ser instalados e configurados na versão de 64 bits do Windows
- O servidor do JIRA é habilitado para HTTPS
- Observe que as versões com suporte no Plug-in do JIRA são mencionadas na seção abaixo.
- O servidor do JIRA é acessível pela Internet, especialmente na página de Logon do Azure AD para autenticação e deve conseguir receber o token do Azure AD
- As credenciais do administrador são configuradas no JIRA
- O WebSudo está desabilitado no JIRA
- Usuário de teste criado no aplicativo para servidores do JIRA

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção do JIRA. Teste a integração primeiro no ambiente de desenvolvimento ou de preparo do aplicativo e, em seguida, use o ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, é possível obter uma avaliação por um mês aqui: [Oferta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Versões com suporte do JIRA

* JIRA Core e Software: 6.4 a 8.0
* Jira Service Desk: 3.0.0 a 3.5.0
* O JIRA também fornece suporte para 5.2. Para obter mais detalhes, clique em [Logon único do Microsoft Azure Active Directory para JIRA 5.2](jira52microsoft-tutorial.md)

> [!NOTE]
> Observe que o nosso plug-in do JIRA também funciona no Ubuntu versão 16.04

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SSO de SAML do JIRA da Microsoft é compatível com SSO iniciado por **SP**

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Adicionando o SSO do SAML para o JIRA da Microsoft por meio da galeria

Para configurar a integração do SSO do SAML para o JIRA da Microsoft ao Azure AD, é necessário adicionar o SSO do SAML para o JIRA da Microsoft à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o SSO do SAML para o JIRA da Microsoft por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SSO do SAML para o JIRA da Microsoft**, selecione **SSO do SAML para o JIRA da Microsoft** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![SSO do SAML para o JIRA da Microsoft na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o SSO de SAML para o JIRA da Microsoft com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SSO de SAML para o JIRA da Microsoft.

Para configurar e testar o logon único do Azure AD com o SSO do SAML para o JIRA da Microsoft, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do SSO de SAML para o JIRA da Microsoft](#configure-jira-saml-sso-by-microsoft-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SSO de SAML para o JIRA da Microsoft](#create-jira-saml-sso-by-microsoft-test-user)** – para ter um equivalente de Brenda Fernandes no SSO do SAML para o JIRA da Microsoft que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SSO de SAML para o JIRA da Microsoft, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SSO de SAML para o JIRA da Microsoft**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único e domínio do SSO do SAML para o JIRA da Microsoft](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<domain:port>/`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. A porta é opcional, caso seja uma URL nomeada. Esses valores são recebidos durante a configuração do plug-in do Jira, que é explicada adiante no tutorial.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-single-sign-on"></a>Configurar o Logon Único do SSO de SAML para o JIRA da Microsoft

1. Em outra janela do navegador da Web, entre na instância do JIRA como administrador.

2. Passe o cursor do mouse sobre a engrenagem e clique em **Complementos**.

    ![Configurar o logon único](./media/jiramicrosoft-tutorial/addon1.png)

3. Baixe o plug-in no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=56506). Carregar manualmente o plug-in fornecido pela Microsoft usando o menu **Carregar complemento**. O download do plug-in está coberto pelo [Contrato de Serviço da Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Configurar o logon único](./media/jiramicrosoft-tutorial/addon12.png)

4. Para executar o cenário do proxy reverso do JIRA ou o cenário do balanceador de carga, execute as seguintes etapas:

    > [!NOTE]
    > Você deve estar configurando o servidor primeiro com as instruções abaixo e depois instalar o plugin.

    a. Inclua abaixo o atributo na porta **do conector** no arquivo **server.xml** do aplicativo do servidor JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Configurar o logon único](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Altere o **URL base** em **Configurações do sistema** de acordo com o balanceador de proxy/carga.

    ![Configurar o logon único](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Depois que o plug-in for instalado, ele será exibido na seção de complementos **Instalados pelo Usuário** da seção **Gerenciar Complemento**. Clique em **Configurar** para configurar o novo plug-in.

    ![Configurar o logon único](./media/jiramicrosoft-tutorial/addon13.png)

6. Realize as seguintes etapas na página de configuração:

    ![Configurar o logon único](./media/jiramicrosoft-tutorial/addon53.png)

    > [!TIP]
    > Verifique se há apenas um certificado mapeado no aplicativo, para que não haja nenhum erro na resolução dos metadados. Se houver vários certificados, após a resolução dos metadados, o administrador receberá um erro.

    1. Na caixa de texto **URL de Metadados**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure e clique no botão **Resolver**. Ele lê a URL de metadados do IdP e popula todas as informações dos campos.

    1. Copie os valores de **Identificador, URL de Resposta e URL de Logon** e cole-os nas caixas de texto **Identificador, URL de Resposta e URL de Logon**, respectivamente, na seção **Domínio e URLs do SSO do SAML para o JIRA da Microsoft** do portal do Azure.

    1. Em **Nome do Botão de Logon**, digite o nome do botão que sua organização deseja que os usuários vejam na tela de logon.

    1. Em **Locais da ID de Usuário SAML**, selecione **A ID de Usuário está no elemento NameIdentifier da instrução Subject** ou **A ID de Usuário está em um elemento Attribute**.  Essa ID deve ser a ID de usuário do JIRA. Se a ID de usuário não tiver uma correspondência, o sistema não permitirá que os usuários entrem.

       > [!Note]
       > O local padrão da ID de Usuário SAML é o Identificador de Nome. Você pode alterar isso para uma opção de atributo e inserir o nome de atributo apropriado.

    1. Se você selecionar a opção **A ID de Usuário está em um elemento de atributo**, na caixa de texto **Nome do atributo**, digite o nome do atributo em que a ID de Usuário é esperada.

    1. Se estiver usando o domínio federado (como o ADFS, etc.) com o Azure AD, clique na opção **Habilitar Descoberta de Realm Inicial** e configure o **Nome de Domínio**.

    1. Em **Nome de Domínio**, digite o nome de domínio aqui, no caso do logon baseado no ADFS.

    1. Marque a opção **Habilitar Logoff Único** se desejar fazer logoff do Azure AD quando um usuário fizer logoff do JIRA.
    
    1. Habilite a caixa de seleção **Forçar Logon do Azure** caso deseje entrar somente por meio das credenciais do Azure AD.
    
       > [!Note]
       > Para habilitar o formulário de logon padrão para o logon de administrador na página de logon quando a opção Forçar Logon do Azure estiver habilitada, adicione o parâmetro de consulta à URL do navegador.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Clique no botão **Salvar** para salvar as alterações.

       > [!NOTE]
       > Para obter mais informações sobre a instalação e a solução de problemas, acesse o [Guia do Administrador do Conector de SSO para o MS JIRA](../ms-confluence-jira-plugin-adminguide.md). Há também uma seção de [perguntas frequentes](../ms-confluence-jira-plugin-faq.md) para auxiliá-lo.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon\@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SSO do SAML para o JIRA da Microsoft.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **SSO de SAML para o JIRA da Microsoft**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SSO do SAML para o JIRA da Microsoft**.

    ![Link do SSO do SAML para o JIRA da Microsoft na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Criar um usuário de teste do SSO de SAML para o JIRA da Microsoft

Para permitir que os usuários do Azure AD entrem no servidor local do JIRA, eles devem ser provisionados no SSO do SAML para o JIRA da Microsoft. Para o SSO do SAML para o JIRA da Microsoft, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no JIRA no servidor local como administrador.

2. Passe o cursor do mouse sobre a engrenagem e clique em **Gerenciamento de usuário**.

    ![Adicionar Funcionário](./media/jiramicrosoft-tutorial/user1.png)

3. Você será redirecionado à página de acesso de administrador para inserir a **Senha** e clicar no botão **Confirmar**.

    ![Adicionar Funcionário](./media/jiramicrosoft-tutorial/user2.png)

4. Na seção da guia **Gerenciamento de usuário**, clique em **criar usuário**.

    ![Adicionar Funcionário](./media/jiramicrosoft-tutorial/user3.png) 

5. Na página da caixa de diálogo **"Criar novo usuário"** , execute as seguintes etapas:

    ![Adicionar Funcionário](./media/jiramicrosoft-tutorial/user4.png) 

    a. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

    b. Na caixa de texto **Nome completo**, digite o nome completo do usuário, como Brenda Fernandes.

    c. Na caixa de texto **Nome de usuário**, digite o email do usuário, como Brittasimon@contoso.com.

    d. Na caixa de texto **Senha**, digite a senha do usuário.

    e. Clique em **Criar usuário**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SSO de SAML para o JIRA da Microsoft no Painel de Acesso, você deverá ser conectado automaticamente ao SSO de SAML para o JIRA da Microsoft, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
