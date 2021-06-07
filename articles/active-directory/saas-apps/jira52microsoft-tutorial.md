---
title: 'Tutorial: Integração do Azure Active Directory ao SSO do SAML para o JIRA da Microsoft (V5.2) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do SAML para o JIRA da Microsoft (V5.2).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 7b85cc064babf44b14e80abc02669573b4730da2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736878"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Tutorial: Integração do Azure Active Directory ao SSO do SAML para o JIRA da Microsoft (V5.2)

Neste tutorial, você aprenderá a integrar o SSO do JIRA SAML da Microsoft (V5.2) ao Azure AD (Azure Active Directory). Ao integrar o JIRA SAML SSO by Microsoft (V5.2) ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao JIRA SAML SSO by Microsoft (V5.2).
* Permitir que os usuários sejam conectados automaticamente ao JIRA SAML SSO by Microsoft (V5.2) com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="description"></a>Descrição

Use sua conta do Microsoft Azure Active Directory com o servidor do Atlassian JIRA para habilitar o logon único. Dessa forma, todos os usuários de sua organização podem usar as credenciais do Azure AD para entrar no aplicativo JIRA. Este plug-in usa o SAML 2.0 para federação.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao SSO do SAML para o JIRA da Microsoft (V5.2), você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- O JIRA Core e o Software 5.2 devem estar instalados e configurados na versão de 64 bits do Windows
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
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Versões com suporte do JIRA

* JIRA Core e Software: 5.2
* O JIRA também fornece suporte a 6.0 para 7.12. Para obter mais detalhes, clique em [SSO do SAML para o JIRA da Microsoft](jiramicrosoft-tutorial.md)

> [!NOTE]
> Observe que o nosso plug-in do JIRA também funciona no Ubuntu versão 16.04.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SSO de SAML do JIRA da Microsoft (V5.2) é compatível com SSO iniciado por **SP**

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Adicionando o SSO do SAML para o JIRA da Microsoft (V5.2) por meio da galeria

Para configurar a integração do JIRA SAML SSO pela Microsoft (V5.2) ao Azure AD, você precisa adicionar o JIRA SAML SSO da Microsoft (V5.2) da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **JIRA SAML SSO by Microsoft (V5.2)** na caixa de pesquisa.
1. Selecione **JIRA SAML SSO by Microsoft (V5.2)** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft-v52"></a>Configurar e testar o SSO do Azure AD para JIRA SAML SSO by Microsoft (V5.2)

Nesta seção, você configura e testa o logon único do Azure AD com o JIRA SAML SSO by Microsoft (V5.2) com base em um usuário de teste chamado **Brenda Fernandes**. Para que o logon único funcione, estabeleça um relacionamento vinculado entre um usuário do Azure AD e o usuário relacionado do JIRA SAML SSO by Microsoft (V5.2).

Para configurar e testar o logon único do Azure AD com o JIRA SAML SSO by Microsoft (V5.2), realize as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do JIRA SAML SSO by Microsoft (V5.2)](#configure-jira-saml-sso-by-microsoft-v52-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do SSO de SAML para o JIRA da Microsoft (V5.2)](#create-jira-saml-sso-by-microsoft-v52-test-user)** – para ter um equivalente de Brenda Fernandes no SSO de SAML para o JIRA da Microsoft (V5.2) que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

1. No portal do Azure, na página de integração de aplicativos do **JIRA SAML SSO by Microsoft (V5.2)** , localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<domain:port>/`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. A porta é opcional, caso seja uma URL nomeada. Esses valores são recebidos durante a configuração do plug-in do Jira, que é explicada adiante no tutorial.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
    1. No campo **Nome**, insira `B.Simon`.  
    1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
    1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
    1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao JIRA SAML SSO by Microsoft (V5.2).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SSO do SAML para o JIRA da Microsoft (V5.2)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-jira-saml-sso-by-microsoft-v52-sso"></a>Configurar o SSO do JIRA SAML SSO by Microsoft (V5.2)

1. Em outra janela do navegador da Web, entre na instância do JIRA como administrador.

2. Passe o cursor do mouse sobre a engrenagem e clique em **Complementos**.

    ![A captura de tela mostra os Complementos selecionados no menu Configurações.](./media/jira52microsoft-tutorial/addon1.png)

3. Na seção da guia Complementos, clique em **Gerenciar complementos**.

    ![A captura de tela mostra a opção Gerenciar complementos selecionada na guia Complementos.](./media/jira52microsoft-tutorial/addon7.png)

4. Baixe o plug-in no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=56521). Carregar manualmente o plug-in fornecido pela Microsoft usando o menu **Carregar complemento**. O download do plug-in está coberto pelo [Contrato de Serviço da Microsoft](https://www.microsoft.com/servicesagreement/).

    ![A captura de tela mostra Gerenciar complementos com o link Carregar complemento destacado.](./media/jira52microsoft-tutorial/addon12.png)

5. Depois que o plug-in for instalado, ele aparecerá na seção de complementos **Instalado pelo Usuário**. Clique em **Configurar** para configurar o novo plug-in.

    ![A captura de tela mostra a seção Logon Único do SAML do Azure AD para Jira com Configurar selecionado.](./media/jira52microsoft-tutorial/addon13.png)

6. Realize as seguintes etapas na página de configuração:

    ![A captura de tela mostra a página de configuração do Conector de SSO do Jira da Microsoft.](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Verifique se há apenas um certificado mapeado no aplicativo, para que não haja nenhum erro na resolução dos metadados. Se houver vários certificados, após a resolução dos metadados, o administrador receberá um erro.

    a. Na caixa de texto **URL de Metadados**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure e clique no botão **Resolver**. Ele lê a URL de metadados do IdP e popula todas as informações dos campos.

    b. Copie os valores de **Identificador, URL de Resposta e URL de Logon** e cole-os nas caixas de texto **Identificador, URL de Resposta e URL de Logon**, respectivamente, na seção **Configuração Básica do SAML** do portal do Azure.

    c. Em **Nome do Botão de Logon**, digite o nome do botão que sua organização deseja que os usuários vejam na tela de logon.

    d. Em **Locais da ID de Usuário SAML**, selecione **A ID de Usuário está no elemento NameIdentifier da instrução Subject** ou **A ID de Usuário está em um elemento Attribute**.  Essa ID deve ser a ID de usuário do JIRA. Se a ID de usuário não tiver uma correspondência, o sistema não permitirá que os usuários entrem.

    > [!Note]
    > O local padrão da ID de Usuário SAML é o Identificador de Nome. Você pode alterar isso para uma opção de atributo e inserir o nome de atributo apropriado.

    e. Se você selecionar a opção **A ID de Usuário está em um elemento de atributo**, na caixa de texto **Nome do atributo**, digite o nome do atributo em que a ID de Usuário é esperada. 

    f. Se estiver usando o domínio federado (como o ADFS, etc.) com o Azure AD, clique na opção **Habilitar Descoberta de Realm Inicial** e configure o **Nome de Domínio**.

    g. Em **Nome de Domínio**, digite o nome de domínio aqui, no caso do logon baseado no ADFS.

    h. Marque a opção **Habilitar Logoff Único** se desejar fazer logoff do Azure AD quando um usuário fizer logoff do JIRA. 

    i. Clique no botão **Salvar** para salvar as alterações.

    > [!NOTE]
    > Para obter mais informações sobre instalação e solução de problemas, visite o [Guia do Administrador do Conector de SSO para MS JIRA](./ms-confluence-jira-plugin-adminguide.md). Além disso, também há as [perguntas frequentes](./ms-confluence-jira-plugin-adminguide.md) para auxiliá-lo.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Criar usuário de teste do SSO do SAML para o JIRA da Microsoft (V5.2)

Para permitir que os usuários do Azure AD entrem no servidor local do JIRA, eles devem ser provisionados no servidor local do JIRA.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no JIRA no servidor local como administrador.

2. Passe o cursor do mouse sobre a engrenagem e clique em **Gerenciamento de usuário**.

    ![A captura de tela mostra o Gerenciamento do usuário selecionado no menu Configurações.](./media/jira52microsoft-tutorial/user1.png)

3. Você será redirecionado à página de acesso de administrador para inserir a **Senha** e clicar no botão **Confirmar**.

    ![A captura de tela mostra a página Acesso de Administrador em que você insere suas credenciais.](./media/jira52microsoft-tutorial/user2.png)

4. Na seção da guia **Gerenciamento de usuário**, clique em **criar usuário**.

    ![A captura de tela mostra a guia Gerenciamento de usuário em que você pode criar um usuário.](./media/jira52microsoft-tutorial/user3.png) 

5. Na página da caixa de diálogo **"Criar novo usuário"** , execute as seguintes etapas:

    ![A captura de tela mostra a caixa de diálogo Criar usuário em que você pode inserir as informações nesta etapa.](./media/jira52microsoft-tutorial/user4.png)

    a. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

    b. Na caixa de texto **Nome completo**, digite o nome completo do usuário, como Brenda Fernandes.

    c. Na caixa de texto **Nome de usuário**, digite o email do usuário, como Brittasimon@contoso.com.

    d. Na caixa de texto **Senha**, digite a senha do usuário.

    e. Clique em **Criar usuário**.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso será redirecionado para o SSO do SAML do JIRA pela URL de logon da Microsoft (V5.2), na qual você pode iniciar o fluxo de logon. 

* Acesse o SSO do SAML do JIRA pela URL de logon da Microsoft (V5.2) diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clica no bloco JIRA SAML SSO by Microsoft (V5.2) em Meus Aplicativos, isso redirecionará para a URL de logon do JIRA SAML SSO by Microsoft (V5.2). Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o JIRA SAML SSO by Microsoft (V5.2), você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).