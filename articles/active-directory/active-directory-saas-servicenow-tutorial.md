---
title: "Tutorial: Integração do Azure Active Directory ao ServiceNow | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o ServiceNow e o ServiceNow Express."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a91fab90a94b655b93c8ae9064ea4836b80d7678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Tutorial: Integração do Active Directory do Azure com o ServiceNow
Neste tutorial, você aprenderá a integrar o ServiceNow e o ServiceNow Express ao Azure AD (Azure Active Directory).

A integração do ServiceNow e do ServiceNow Express ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao ServiceNow e ao ServiceNow Express
* Você pode habilitar seus usuários a fazerem logon automaticamente no ServiceNow e ServiceNow Express (logon único) com suas contas do Azure AD
* Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao ServiceNow e ao ServiceNow Express, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Para o ServiceNow, uma instância ou um locatário do ServiceNow, versão Calgary ou superior
* Para o ServiceNow Express, uma instância do ServiceNow Express, versão Helsinki ou superior
* O locatário ServiceNow deve ter o [Plug-in de Logon Único de Provedor Múltiplo](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) habilitado. Isso pode ser feito [enviando uma solicitação de serviço](https://hi.service-now.com). 

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o ServiceNow da galeria
2. Configurando e testando o logon único do Azure AD para o ServiceNow ou para o ServiceNow Express

## <a name="adding-servicenow-from-the-gallery"></a>Adicionando o ServiceNow da galeria
Para configurar a integração do ServiceNow ou do ServiceNow Express no Azure AD, você precisa adicionar o ServiceNow da galeria à sua lista de aplicativos SaaS gerenciados. 

**Para adicionar o ServiceNow da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite**ServiceNow**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. No painel de resultados, selecione **ServiceNow** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o ServiceNow ou o ServiceNow Express, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ServiceNow é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ServiceNow.
Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como sendo o valor de **Nome de Usuário** no ServiceNow. Para configurar e testar o logon único do Azure AD com o ServiceNow, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do logon único do Azure AD para o ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** - para habilitar seus usuários a usar esse recurso.
2. **[Configuração do logon único do Azure AD para o ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - para habilitar seus usuários a usar esse recurso.
3. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do ServiceNow](#creating-a-servicenow-test-user)** - para ter um equivalente de Brenda Fernandes no ServiceNow que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
6. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

> [!NOTE]
> Se você quiser configurar o ServiceNow, ignore a etapa 2. Da mesma forma, se você quiser configurar o ServiceNow Express, ignore a etapa 1.
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configuração do logon único do Azure AD para o ServiceNow
1. No portal clássico do Azure AD, na página de integração de aplicativos do **ServiceNow**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar logon único")

2. Na página **Como você deseja que os usuários façam logon no ServiceNow**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar logon único")

3. Na página **Definir Configurações do Aplicativo** , execute as seguintes etapas:
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar URL do Aplicativo")
   
    a. na caixa de texto **URL de Entrada no ServiceNow**, digite a URL usada pelos usuários para entrar no seu aplicativo ServiceNow seguindo o padrão: `https://<instance-name>.service-now.com`.
   
    b. Na caixa de texto **Identificador**, digite a URL usada pelos usuários para entrar em seu aplicativo do ServiceNow usando o seguinte padrão: `https://<instance-name>.service-now.com`.
   
    c. Clique em **Avançar**

4. Para que o Azure AD configure automaticamente o ServiceNow para autenticação baseada em SAML, insira o nome da instância do ServiceNow, o nome de usuário do administrador e a senha de administrador no formulário **Configurar automaticamente o logon único** e clique em *Configurar*. Observe que o nome de usuário do administrador informado deve ter a função **security_admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow para usar o Azure AD como provedor de identidade SAML, clique em **Configurar manualmente o aplicativo para o logon único**, em **Avançar** e conclua as etapas a seguir.
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar URL do Aplicativo")

5. Na página **Configurar logon único no ServiceNow**, clique em **Baixar certificado**, salve o arquivo de certificado no computador.
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar logon único")

6. Entre no seu aplicativo ServiceNow como administrador.

7. Ative o plug-in *Integração - Instalador de Logon Único de Vários Provedores* ao seguir as próximas etapas:
   
    a. No painel de navegação à esquerda, vá para a seção **Definição do Sistema** e, em seguida, clique em **Plug-ins**.
   
    ![Configurar URL do aplicativo](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Ativar o plug-in")
   
    b. Procure *Integração - Instalador de Logon Único de Vários Provedores*.
   
    ![Configurar URL do aplicativo](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Ativar o plug-in")
   
    c. Selecione o plugin. Clique com o botão direito do mouse e selecione **Ativar/Atualizar**.
   
    d. Clique no botão **Ativar**.

8. No painel de navegação à esquerda, clique em **Propriedades**.  
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurar URL do Aplicativo")

9. No diálogo **Várias propriedades de SSO do provedor** , execute as seguintes etapas:
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurar URL do Aplicativo")
   
    a. Como **Habilitar vários provedores SSO**, selecione **Sim**.
   
    b. Como **Habilitar log de depuração com integração SSO de vários provedores**, selecione **Sim**.
   
    c. Na caixa de texto **O campo na tabela de usuário que...**, digite **nome_de_usuário**.
   
    d. Clique em **Salvar**.

10. No painel de navegação à esquerda, clique em **Certificados x509**.
    
     ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurar logon único")

11. No diálogo **Certificados x. 509**, clique em **Novo**.
    
     ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurar logon único")

12. No diálogo **Certificados x. 509** , execute as seguintes etapas:
    
     ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar logon único")
    
     a. Clique em **Novo**.
    
     b. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **TestSAML2.0**).
    
     c. Selecione **Ativo**.
    
     d. Para **Formato**, selecione **PEM**.
    
     e. Como **Tipo**, selecione **Confiar nos Certificados do Repositório**.
    
     f. Abra seu certificado codificado base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado PEM**.
    
     g. Clique em **Atualizar**.

13. No painel de navegação à esquerda, clique em **Provedores de Identidade**.
    
     ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurar logon único")

14. No diálogo **Provedores de Identidade**, clique em **Novo**:
    
     ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurar logon único")

15. No diálogo **Provedores de Identidade**, clique em **SAML2 Update1?**:
    
     ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurar logon único")

16. No diálogo Propriedades de SAML2 Atualização1, execute as seguintes etapas:
    
     ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurar logon único")

    a. Na caixa de texto **Nome** , digite um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. Na caixa de texto **Campo de Usuário**, digite **email** ou **user_name**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow. 

    > [!NOTE] 
    > Você pode configurar o Azure AD para emitir a ID de usuário (nome UPN) do Azure AD ou o endereço de email como o identificador exclusivo no token SAML acessando a seção **ServiceNow > Atributos > Logon Único** do portal clássico do Azure e mapeando o campo desejado para o atributo **nameidentifier**. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome UPN) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user_name)

    c. No portal clássico do Azure AD, copie o valor de **ID do Provedor de Identidade** e cole-o na caixa de texto **URL do Provedor de Identidade**.

    d. No portal clássico do Azure AD, copie o valor de **URL de Solicitação de Autenticação** e cole-o na caixa de texto **Solicitação de Autenticação do Provedor de Identidade**.

    e. No portal clássico do Azure AD, copie o valor de **URL de Serviço de Saída Única** e cole-o na caixa de texto **Solicitação de Logoff Único do Provedor de Identidade**.

    f. Na caixa de texto **Home page do ServiceNow** , digite a URL da sua página inicial de instância do ServiceNow.

    > [!NOTE] 
    > A home page da instância do ServiceNow é uma concatenação da **URL do locatário do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    g. Na caixa de texto **ID da entidade/emissor** , digite a URL do seu locatário do ServiceNow.

    h. Na caixa de texto **URL do Público-alvo** , digite a URL do seu locatário do ServiceNow. 

    i. Na caixa de texto **associação de protocolo SingleLogoutRequest do IDP**, digite **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. Na caixa de texto Política da NameID, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Desmarque **Criar um AuthnContextClass**.

    l. No **Método AuthnContextClassRef**, digite `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Isso só será necessário se você for uma organização que esteja somente na nuvem. Se você estiver usando o ADFS ou o MFA local para autenticação, não deverá configurar esse valor. 

    m. Na caixa de texto **Distorção do Relógio**, digite **60**.

    n. Como **Script de Logon Único**, selecione **MultiSSO_SAML2_Update1**.

    o. Como **Certificado x509**, selecione o certificado que você criou na etapa anterior.

    p. Clique em **Enviar**. 

1. No portal clássico do Azure AD, selecione a confirmação de configuração do logon único e clique em **Avançar**. 
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar logon único")

2. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar logon único")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configuração do logon único do Azure AD para o ServiceNow Express
1. No portal clássico do Azure AD, na página de integração de aplicativos do **ServiceNow**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar logon único")

2. Na página **Como você deseja que os usuários façam logon no ServiceNow**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar logon único")

3. Na página **Definir Configurações do Aplicativo** , execute as seguintes etapas:
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar URL do Aplicativo")
   
    a. na caixa de texto **URL de Entrada no ServiceNow**, digite a URL usada pelos usuários para entrar no seu aplicativo ServiceNow seguindo o padrão: `https://<instance-name>.service-now.com`.
   
    b. Na caixa de texto **URL do Emissor**, digite a URL usada pelos usuários para entrar no seu aplicativo ServiceNow seguindo o padrão `https://<instance-name>.service-now.com`.
   
    c. Clique em **Avançar**

4. Clique em **Configurar manualmente o aplicativo para logon único**, então clique em **Avançar** e conclua as etapas a seguir.
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar URL do Aplicativo")

5. Na página **Configurar logon único no ServiceNow**, clique em **Baixar certificado**, salve o arquivo de certificado no computador e clique em **Avançar**.
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar logon único")

6. Entre no seu aplicativo ServiceNow Express como administrador.

7. No painel de navegação à esquerda, clique em **Logon Único**.  
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurar URL do Aplicativo")

8. No diálogo **Logon Único**, clique no ícone de configuração no canto superior direito e defina as seguintes propriedades:
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurar URL do Aplicativo")
   
    a. Ative/desative **Habilitar SSO de vários provedores** à direita.
   
    b. Ative/desative **Habilitar registro em log de depuração para a integração de SSO de vários provedores** à direita.
   
    c. Na caixa de texto **O campo na tabela de usuário que...**, digite **nome_de_usuário**.
9. No diálogo **Logon Único**, clique em **Adicionar Novo Certificado**.
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurar logon único")
10. No diálogo **Certificados x. 509** , execute as seguintes etapas:
    
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar logon único")
    
    a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: **TestSAML2.0**).
    
    b. Selecione **Ativo**.
    
    c. Para **Formato**, selecione **PEM**.
    
    d. Como **Tipo**, selecione **Confiar nos Certificados do Repositório**.
    
    e. Crie um arquivo codificado em Base64 usando o certificado baixado.
    
    > [!NOTE]
    > Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).
    > 
    > 
    
    f. Abra seu certificado codificado base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado PEM**.
    
    g. Clique em **Atualizar**.
11. No diálogo **Logon Único**, clique em **Adicionar novo IdP**.
    
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurar logon único")
12. No diálogo **Adicionar Novo Provedor de Identidade**, em **Configurar Provedor de Identidade**, execute as seguintes etapas:
    
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurar logon único")

    a. Na caixa de texto **Nome** , digite um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. No portal clássico do Azure AD, copie o valor de **ID do Provedor de Identidade** e cole-o na caixa de texto **URL do Provedor de Identidade**.

    c. No portal clássico do Azure AD, copie o valor de **URL de Solicitação de Autenticação** e cole-o na caixa de texto **Solicitação de Autenticação do Provedor de Identidade**.

    d. No portal clássico do Azure AD, copie o valor de **URL de Serviço de Saída Única** e cole-o na caixa de texto **Solicitação de Logoff Único do Provedor de Identidade**.

    e. Como **Certificado de Provedor de Identidade**, selecione o certificado que você criou na etapa anterior.


1. Clique em **Configurações Avançadas** e, em **Propriedades Adicionais do Provedor de Identidade**, execute as seguintes etapas:
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurar logon único")
   
    a. Na caixa de texto **associação de protocolo SingleLogoutRequest do IDP**, digite **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.
   
    b. Na caixa de texto **Política da NameID**, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.    
   
    c. No **método AuthnContextClassRef**, digite **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
   
    d. Desmarque **Criar um AuthnContextClass**.

2. Em **Propriedades Adicionais do Provedor de Serviço**, execute as seguintes etapas:
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurar logon único")
   
    a. Na caixa de texto **Home page do ServiceNow** , digite a URL da sua página inicial de instância do ServiceNow.
   
    > [!NOTE]
    > A home page da instância do ServiceNow é uma concatenação da **URL do locatário do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).
    > 
    > 
   
    b. Na caixa de texto **ID da entidade/emissor** , digite a URL do seu locatário do ServiceNow.
   
    c. Na caixa de texto **URI do Público-alvo** , digite a URL do seu locatário do ServiceNow. 
   
    d. Na caixa de texto **Distorção do Relógio**, digite **60**.
   
    e. Na caixa de texto **Campo de Usuário**, digite **email** ou **user_name**, dependendo de qual campo é usado para identificar exclusivamente os usuários em sua implantação do ServiceNow.
   
    > [!NOTE]
    > Você pode configurar o Azure AD para emitir a ID de usuário (nome UPN) do Azure AD ou o endereço de email como o identificador exclusivo no token SAML acessando a seção **ServiceNow > Atributos > Logon Único** do portal clássico do Azure e mapeando o campo desejado para o atributo **nameidentifier**. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome UPN) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, user_name)
    > 
    > 
   
    f. Clique em **Salvar**. 

3. No portal clássico do Azure AD, selecione a confirmação de configuração do logon único e clique em **Avançar**. 
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar logon único")

4. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![Configurar logon único](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar logon único")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no ServiceNow.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. No portal clássico de Gerenciamento do Azure, na página de integração de aplicativos do **ServiceNow**, clique em **Configurar provisionamento de usuários**. 
   
    ![Provisionamento do usuário](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Provisionamento do usuário")

2. Na página **Inserir suas credenciais do ServiceNow para habilitar o provisionamento automático de usuário**, forneça as seguintes configurações:
   
     a. Na caixa de texto **Nome de Instância do ServiceNow** , digite o nome da instância do ServiceNow.
   
     b. Na caixa de texto **Nome de Usuário Administrador do ServiceNow** , digite o nome da conta de administrador do ServiceNow.
   
     c. Na caixa de texto **Senha de Administrador do ServiceNow** , digite a senha desta conta.
   
     d. Clique em **validar** para verificar sua configuração.
   
     e. Clique no botão **Avançar** para abrir a página **Próximas etapas**.
   
     f. Se você deseja provisionar todos os usuários neste aplicativo, escolha "**Provisionar automaticamente todas as contas de usuário no diretório para este aplicativo**". 
   
    ![Próximas etapas](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Próximas etapas")
   
     g. Na página **Próximas etapas**, clique em **Concluir** para salvar sua configuração.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Avançar**.

6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Avançar**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-servicenow-test-user"></a>Criar um usuário de teste do ServiceNow
Nesta seção, você criará uma usuária chamada Brenda Fernandes no ServiceNow. Nesta seção, você criará uma usuária chamada Brenda Fernandes no ServiceNow. Se você não souber como adicionar um usuário em sua conta do ServiceNow ou do ServiceNow Express, contate a equipe de suporte do ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao ServiceNow.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao ServiceNow, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ServiceNow**.
   
    ![Configurar Logon Único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 

4. Na lista Todos os Usuários, escolha **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ServiceNow no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo ServiceNow.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
