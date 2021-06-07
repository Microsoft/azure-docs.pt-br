---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Google Cloud (G Suite) Connector | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Google Cloud (G Suite) Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: jeedes
ms.openlocfilehash: a846899ba8f9b9e7c0d2e54744f5e5044ca7a2d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732026"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Google Cloud (G Suite) Connector

Neste tutorial, você aprenderá a integrar o Google Cloud (G Suite) Connector ao Azure AD (Azure Active Directory). Ao integrar o Google Cloud (G Suite) Connector ao Azure AD, você pode:

* Controle, no Azure AD, quem tem acesso ao Google Cloud (G Suite) Connector.
* Permitir que seus usuários entrem automaticamente no Google Cloud (G Suite) Connector com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

- Uma assinatura do Azure AD.
- Assinatura habilitada para SSO (logon único) do Google Cloud (G Suite) Connector.
- Uma assinatura do Google Apps ou uma assinatura do Google Cloud Platform.

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. Este documento foi criado usando o novo usuário, experiência de logon único. Se você ainda estiver usando o antigo, a instalação terá uma aparência diferente. Você pode habilitar a nova experiência nas configurações de logon único do aplicativo do G Suite. Vá para **Azure AD, aplicativos empresariais**, selecione **Google Cloud (G Suite) Connector**, selecione **Logon único** e, em seguida, clique em **Tentar nossa nova experiência**.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. **P: Essa integração dá suporte para integração de SSO do Google Cloud Platform com o Microsoft Azure AD?**

    A: Sim. O Google Cloud Platform e o Google Apps compartilham a mesma plataforma de autenticação. Portanto, para fazer a integração com o GCP, será necessário configurar o SSO com o Google Apps.

2. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com o logon único do AD do AD do Azure?**
  
    A: Sim. Os usuários podem entrar em seus dispositivos Chromebook usando suas credenciais do Azure AD. Confira este [artigo de suporte do Google Cloud (G Suite) Connector](https://support.google.com/chrome/a/answer/6060880) para saber mais sobre o motivo de os usuários receberem uma solicitação por credenciais duas vezes.

3. **P: Se eu habilitar o logon único, os usuários conseguirão usar suas credenciais do Azure AD para entrar em qualquer produto do Google, como Google Sala de aula, GMail, Google Drive, YouTube e assim por diante?**

    A: Sim, dependendo de [quais aplicativos do Google Cloud (G Suite) Connector](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) você optar por habilitar ou desabilitar para sua organização.

4. **P: Posso habilitar o logon único apenas para um subconjunto de meus usuários do Google Cloud (G Suite) Connector?**

    A: Não. A ativação do logon único exige imediatamente que todos os usuários do Google Cloud (G Suite) Connector se autentiquem com suas credenciais do Azure AD. Como o Google Cloud (G Suite) Connector não oferece suporte a vários provedores de identidade, o provedor de identidade para seu ambiente do Google Cloud (G Suite) Connector pode ser o Azure AD ou o Google, mas não ambos ao mesmo tempo.

5. **P: Se um usuário se conectar por meio do Windows, ele será autenticado automaticamente no Google Cloud (G Suite) Connector sem que uma senha seja solicitada?**

    A: Há duas opções para este cenário. Primeiro, os usuários podem entrar em dispositivos com Windows 10 por meio do [Ingresso no Active Directory do Azure](../devices/overview.md). Como alternativa, os usuários podem entrar em dispositivos com Windows que ingressaram em um domínio para um Active Directory local com logon único habilitado no AD do Azure por meio de uma implantação dos [Serviços de Federação do Active Directory (AD FS)](../hybrid/plan-connect-user-signin.md) . Ambas as opções exigem que você realize as etapas no tutorial a seguir para habilitar o logon único entre o Azure AD e o Google Cloud (G Suite) Connector.

6. **P: O que devo fazer quando recebo uma mensagem de erro de "email inválido"?**

    A: Para essa configuração, o atributo de email é necessário para os usuários entrarem. Esse atributo não pode ser definido manualmente.

    O atributo de email é populado automaticamente para qualquer usuário com uma licença válida do Exchange. Se o usuário não estiver habilitado para email, esse erro será recebido, o aplicativo precisa obter esse atributo para fornecer acesso.

    Será possível acessar o portal.office.com usando uma conta do administrador. Em seguida, clique em Centro de Administração > Cobrança > Assinaturas. Selecione a Assinatura do Microsoft 365 e depois clique em Atribuir aos usuários. Selecione os usuários que você deseja verificar a assinatura e clique em Editar licenças no painel direito.

    Depois que a licença do Microsoft 365 for atribuída, a ação poderá levar alguns minutos para ser aplicada. Depois disso, o atributo user.mai será preenchido automaticamente e o problema deve ser resolvido.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Google Cloud (G Suite) Connector dá suporte ao SSO iniciado por **SP**

* O Google Cloud (G Suite) Connector dá suporte ao [provisionamento **automatizado** de usuários](./g-suite-provisioning-tutorial.md)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Como adicionar o Google Cloud (G Suite) Connector por meio da galeria

Para configurar a integração do Google Cloud (G Suite) Connector ao Azure AD, você precisa adicionar o Google Cloud (G Suite) Connector da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Google Cloud (G Suite) Connector** na caixa de pesquisa.
1. Selecione **Google Cloud (G Suite) Connector** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Configurar e testar logon único do Azure AD para o Google Cloud (G Suite) Connector

Configure e teste o SSO do Azure AD com o Google Cloud (G Suite) Connector usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Google Cloud (G Suite) Connector.

Para configurar e testar o SSO do Azure AD com o Conector Google Cloud (G Suite), execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Google Cloud (G Suite) Connector](#configure-google-cloud-g-suite-connector-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Google Cloud (G Suite) Connector](#create-google-cloud-g-suite-connector-test-user)** – para ter um equivalente de B. Fernandes no Google Cloud (G Suite) Connector que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Google Cloud (G Suite) Connector**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje realizar a configuração para o **Gmail**, execute as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão: 

    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

1. Na seção **Configuração Básica do SAML**, caso deseje realizar a configuração para a **Google Cloud Platform**, execute as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão:  
    
    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. O Google Cloud (G Suite) Connector não fornece o valor de ID/Identificador da Entidade na configuração de logon único, de modo que quando você desmarcar a opção **emissor específico a um domínio**, o valor do Identificador será `google.com`. Se você marcar a opção **emissor específico a um domínio**, ele será `google.com/a/<yourdomainname.com>`. Para marcar/desmarcar a opção **emissor específico a um domínio**, você precisa ir para a seção **Configurar SSO do Google Cloud (G Suite) Connector**, que é explicada posteriormente no tutorial. Para obter mais informações, contate a [Equipe de suporte ao cliente do Google Cloud (G Suite) Connector](https://www.google.com/contact/).

1. O aplicativo Google Cloud (G Suite) Connector para o Azure AD espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **Identificador Exclusivo de Usuário** é **user.userprincipalname**, mas o Google Cloud (G Suite) Connector espera que isso seja mapeado com o endereço de email do usuário. Para que você possa usar o atributo **user. mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização.

    ![image](common/default-attributes.png)


1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Google Cloud (G Suite) Connector**, copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso ao Google Cloud (G Suite) Connector.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Google Cloud (G Suite) Connector**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Configurar SSO do Google Cloud (G Suite) Connector

1. Abra uma nova guia no navegador e entre no [Console de Admin do Google Cloud (G Suite) Connector](https://admin.google.com/) usando sua conta de administrador.

2. Clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles** , na parte inferior da tela.

    ![Clique em Segurança.][10]

3. Na página **Segurança**, clique em **Configurar logon único (SSO).**

    ![Clique em SSO.][11]

4. Realize as seguintes alterações de configuração:

    ![Configure o SSO][12]

    a. Selecione **Configurar SSO com um provedor de identidade de terceiros**.

    b. No campo **URL da página de entrada** no Google Cloud (G Suite) Connector, cole o valor da **URL de logon** que você copiou do portal do Azure.

    c. No campo **URL da página de saída** no Google Cloud (G Suite) Connector, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    > [!NOTE]
    > O Google Cloud (G Suite) é baseado no protocolo de Logoff do SAML. Portanto, no campo **URL da página de saída**, precisamos usar a URL de Logoff do SAML, ou seja, a URL de logon como o valor para o mesmo.

    d. No Google Cloud (G Suite) Connector, para o **Certificado de verificação**, carregue o certificado baixado no portal do Azure.   

    e. Marque/desmarque a opção **Usar um emissor específico a um domínio** de acordo com a observação mencionada na seção **Configuração Básica do SAML** anterior no Azure AD.

    f. No campo **URL de Alteração de Senha** do Google Cloud (G Suite) Connector, cole o valor de **URL de alteração de senha** copiado do portal do Azure.

    g. Clique em **Save** (Salvar).

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Criar usuário de teste do Google Cloud (G Suite) Connector

O objetivo desta seção é [criar um usuário no Google Cloud (G Suite) Connector](https://support.google.com/a/answer/33310?hl=en) chamado B. Fernandes. Depois que o usuário for sido criado de modo manual no Conector do Google Cloud (G Suite), ele poderá entrar usando as credenciais de logon do Microsoft 365.

O Google Cloud (G Suite) Connector também dá suporte ao provisionamento automático de usuários. Para configurar o Provisionamento de Usuário automático, primeiro, [configure o Google Cloud (G Suite) Connector para o provisionamento automático de usuários](./g-suite-provisioning-tutorial.md).

> [!NOTE]
> O usuário já deve existir no Google Cloud (G Suite) Connector se o provisionamento no Microsoft Azure AD não foi ativado antes de testar o logon único.

> [!NOTE]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará para a URL de logon do Conector do Google Cloud (G Suite), no qual você pode iniciar o fluxo de logon. 

* Acesse a URL de Logon do Conector do Google Cloud (G Suite) diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Conector do Google Cloud (G Suite) em Meus Aplicativos, isso redirecionará para a URL de logon do Conector do Google Cloud (G Suite). Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Google Cloud (G Suite) Connector, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
