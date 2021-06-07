---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ServiceNow | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: c90234249f3cf7eb6ed4793110d61e1f8190ed60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99092594"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ServiceNow

Neste tutorial, você aprenderá como integrar o ServiceNow ao Azure Active Directory (Azure AD). Ao integrar o ServiceNow ao Azure AD, você pode:

* No Azure AD, controlar quem tem acesso ao ServiceNow.
* Permitir que os usuários sejam conectados automaticamente ao ServiceNow com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Jao6]

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do ServiceNow habilitada para SSO (logon único).
* Para o ServiceNow, uma instância ou locatário do ServiceNow dá suporte às versões Calgary, Kingston, London, Madrid, New York, Orlando e Paris ou posteriores.
* Para o ServiceNow Express, uma instância do ServiceNow Express, versão Helsinki ou posterior.
* O locatário ServiceNow deve ter o [Plug-in de Logon Único de Provedor Múltiplo](https://old.wiki/index.php/Multiple_Provider_Single_Sign-On#gsc.tab=0) habilitado.
* Para configuração automática, habilite o plug-in do multi-provedor para ServiceNow.
* Para instalar o aplicativo ServiceNow Clássico (móvel), acesse o armazenamento apropriado e pesquise pelo aplicativo ServiceNow Clássico. Em seguida, baixe-o.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

* O ServiceNow dá suporte a SSO iniciado por **SP**.

* O ServiceNow dá suporte ao [Provisionamento de usuário automatizado](servicenow-provisioning-tutorial.md).

* Você pode configurar o aplicativo ServiceNow Clássico (móvel) com o Azure AD para habilitar o SSO. Ele é compatível com usuários Android e iOS. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

## <a name="add-servicenow-from-the-gallery"></a>Adicionar o ServiceNow da galeria

Para configurar a integração do ServiceNow no Azure AD, você precisa adicionar o ServiceNow da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta Microsoft pessoal.
1. No painel esquerdo, selecione o serviço **Azure Active Directory**.
1. Acesse **Aplicativos Empresariais** e selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **ServiceNow** na caixa de pesquisa.
1. Selecione **ServiceNow** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-servicenow"></a>Configurar e testar o SSO do Azure AD para ServiceNow

Configure e teste o SSO do Azure AD com o ServiceNow usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ServiceNow.

Para configurar e testar o SSO do Azure AD com o ServiceNow, execute as seguintes etapas:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B.Fernandes.
    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o logon único do Azure AD.
    1. [Configure o SSO do Azure AD para o ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) a fim de permitir que seus usuários usem esse recurso.
2. [Configure ServiceNow](#configure-servicenow) para definir as configurações de SSO do lado do aplicativo.
    1. [Crie um usuário de teste no ServiceNow](#create-servicenow-test-user) para ter um equivalente de B.Fernandes no ServiceNow que esteja vinculado à representação do usuário no Azure AD.
    1. [Configure o SSO do ServiceNow Express](#configure-servicenow-express-sso) para definir as configurações de logon único do lado do aplicativo.  
3. [Teste o SSO](#test-sso) para verificar se a configuração funciona.
4. [Teste o SSO para o ServiceNow Clássico (móvel)](#test-sso-for-servicenow-classic-mobile) a fim de verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **ServiceNow**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar logon único com SAML, com o ícone de caneta realçado](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, realize as seguintes etapas:

    a. Em **URL de Logon**, insira uma URL que adote o seguinte padrão: `https://<instancename>.service-now.com/navpage.do`

    b. Em **Identificador (ID da Entidade)** , insira uma URL que adote o seguinte padrão: `https://<instance-name>.service-now.com`

    c. Para a **URL de Resposta**, insira um dos seguintes padrões de URLs:

    | URL de resposta|
    |----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` | 

    d. Em **URL de Logoff**, insira uma URL que adote o seguinte padrão: `https://<instancename>.service-now.com/navpage.do`

    > [!NOTE]
    > Se "/" for adicionado ao valor de Identificador, remova-o manualmente.

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, a URL de Resposta, a URL de Logoff e o identificador, que são explicados mais adiante no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação do SAML**, localize **Certificado (Base64)** . 

   ![Captura de tela da seção do Certificado de Autenticação do SAML, com o Download realçado](common/certificatebase64.png)

   a. Clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas. Essa URL será usada posteriormente no tutorial.

    b. Selecione **Download** para baixar o **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no seu computador.

1. Na seção **Configurar o ServiceNow**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Captura de tela da seção Configurar o ServiceNow, com as URLs realçadas](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. Para **Nome**, insira `B.Simon`.  
   1. Para **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar Senha** e, em seguida, anote o valor mostrado na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder a ela o acesso ao ServiceNow.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ServiceNow**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários e, em seguida, escolha **Selecionar**.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Configurar o SSO do Azure AD para o ServiceNow Express

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **ServiceNow**, clique em **Logon único**.

    ![Captura de tela da página de integração do aplicativo ServiceNow com o logon único realçado](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Captura de tela de Selecionar um método de logon único, com o SAML realçado](common/select-saml-option.png)

3. Na página **Configurar o logon único com o SAML**, selecione o ícone de caneta para abrir a caixa de diálogo **Configuração Básica de SAML**.

    ![Captura de tela da página Configurar o logon único com o SAML, com o ícone de caneta realçado](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, realize as seguintes etapas:

    a. Para **URL de Logon**, insira uma URL que adote o seguinte padrão: `https://<instancename>.service-now.com/navpage.do`

    b. Para **Identificador (ID da Entidade)** , insira uma URL que adote o seguinte padrão: `https://<instance-name>.service-now.com`

    c. Para a **URL de Resposta**, insira uma das seguintes URLs:

    | URL de resposta |
    |-----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` |

    d. Em **URL de Logoff**, insira uma URL que adote o seguinte padrão: `https://<instancename>.service-now.com/navpage.do`
    
    > [!NOTE]
    > Se "/" for adicionado ao valor de Identificador, remova-o manualmente.

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, a URL de Resposta, a URL de Logoff e o identificador, que são explicados mais adiante no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com o SAML**, na seção **Certificado de Autenticação do SAML**, selecione **Download** para baixar o **Certificado (Base64)** das opções especificadas, de acordo com seus requisitos. Salve-o no computador.

    ![Captura de tela da seção Certificado de Autenticação SAML, com Download realçado](common/certificatebase64.png)

6. Você pode fazer com que o Azure AD configure automaticamente o ServiceNow para autenticação com base no SAML. Para habilitar esse serviço, acesse a seção **Configurar o ServiceNow** e selecione **Exibir instruções passo a passo** para abrir a janela **Configurar logon**.

    ![Captura de tela da seção Configurar o ServiceNow, com Exibir instruções passo a passo realçada](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

7. No formulário **Configurar logon**, insira o nome da instância, o nome de usuário do administrador e a senha de administrador do ServiceNow. Selecione **Configurar agora**. O nome de usuário do administrador informado deve ter a função **security_admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow para usar o Azure AD como um provedor de identidade do SAML, selecione **Configurar manualmente o logon único**. Copie **a URL de logoff, o identificador do Azure AD e a URL de logon** da seção Referência Rápida.

    ![Captura de tela do formulário Configurar logon, com "Configurar Agora" realçado](./media/servicenow-tutorial/configure.png "Configurar a URL do aplicativo")

## <a name="configure-servicenow"></a>Configurar o ServiceNow

1. Entre no seu aplicativo ServiceNow como administrador.

1. Ative o plug-in **Integração – Instalador de logon único para vários provedores** executando as seguintes etapas:

    a. No painel esquerdo, pesquise a seção **Definição de Sistema** na caixa de pesquisa e, em seguida, selecione **plug-ins**.

    ![Captura de tela da seção Definição de Sistema, com "Definição de Sistema" e "Plug-ins" realçados](./media/servicenow-tutorial/tutorial-servicenow-03.png "Ativar plug-in")

    b. Procure **Integração – Instalador de Logon Único de Vários Provedores**.

     ![Captura de tela da página Plug-ins do Sistema, com "Integração – Instalador de Logon Único de Vários Provedores" realçado](./media/servicenow-tutorial/tutorial-servicenow-04.png "Ativar plug-in")

    c. Selecione o plug-in. Clique com o botão direito do mouse e selecione **Ativar/Atualizar**.

     ![Captura de tela do menu do plug-in acionado ao clicar com o botão direito do mouse, com "Ativar/Atualizar" realçado](./media/servicenow-tutorial/tutorial-activate.png "Ativar plug-in")

    d. Selecione **Ativar**.

     ![Captura de tela da caixa de diálogo Ativar plug-in, com "Ativar" realçado](./media/servicenow-tutorial/tutorial-activate-1.png "Ativar plug-in")

1. No painel à esquerda, pesquise a seção **SSO para vários provedores** na barra de pesquisa e, em seguida, selecione **Propriedades**.

    ![Captura de tela da seção de SSO para vários provedores, com "Propriedades" e "SSO para Vários Provedores" realçados](./media/servicenow-tutorial/tutorial-servicenow-06.png "Configurar a URL do aplicativo")

1. Na caixa de diálogo **Propriedades de SSO para vários provedores**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Propriedades de SSO para Vários Provedores](./media/servicenow-tutorial/ic7694981.png "Configurar a URL do aplicativo")

    * Para **Habilitar SSO para vários provedores**, selecione **Sim**.
  
    * Para **Habilitar importação automática de usuários de todos os provedores de identidade para a tabela do usuário**, selecione **Sim**.

    * Para **Habilitar o log de depuração para a integração do SSO para vários provedores**, selecione **Sim**.

    * Para **O campo da tabela do usuário que...** , insira **email**.
  
    * Clique em **Salvar**.

1. Você pode configurar o ServiceNow automaticamente ou manualmente. Para configurar o ServiceNow automaticamente, execute as seguintes etapas:

    1. Volte para a página de logon único do **ServiceNow** no portal do Azure.

    1. O serviço de configuração com um clique é fornecido para o ServiceNow. Para habilitar esse serviço, acesse a seção **Configuração do ServiceNow** e selecione **Configurar o ServiceNow** para abrir a janela **Configurar logon**.

        ![Captura de tela de Configurar o ServiceNow, com Exibir instruções passo a passo realçada](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

    1. No formulário **Configurar logon**, insira o nome da instância, o nome de usuário do administrador e a senha de administrador do ServiceNow. Selecione **Configurar agora**. O nome de usuário do administrador informado precisa ter a função **security-admin** atribuída no ServiceNow para que isso funcione. Caso contrário, para configurar manualmente o ServiceNow para usar o Azure AD como um provedor de identidade do SAML, selecione **Configurar manualmente o logon único**. Copie **a URL de saída, a ID da Entidade do SAML e a URL do serviço de logon único do SAML** da seção de Referência Rápida.

        ![Captura de tela do formulário Configurar logon, com "Configurar Agora" realçado](./media/servicenow-tutorial/configure.png "Configurar a URL do aplicativo")

    1. Entre no seu aplicativo ServiceNow como administrador.

       * Na configuração automática, todas as configurações necessárias são definidas do lado do **ServiceNow**, mas o **Certificado X.509** não é habilitado por padrão e o valor de **Script de Logon Único** deve ser **MultiSSOv2_SAML2_custom**. Você precisa mapeá-lo manualmente para o provedor de identidade no ServiceNow. Siga estas etapas:

         1. No painel à esquerda, pesquise a seção **SSO para vários provedores** na caixa de pesquisa e selecione **Provedores de identidade**.

            ![Captura de tela da seção de SSO para Vários Provedores, com "Provedores de Identidade" realçado](./media/servicenow-tutorial/tutorial-servicenow-07.png "Configurar o logon único")

         1. Selecione o provedor de identidade gerado automaticamente.

            ![Captura de tela dos provedores de identidade com o provedor de identidade gerado automaticamente realçado](./media/servicenow-tutorial/tutorial-servicenow-08.png "Configurar o logon único")

         1.  Na seção **Provedor de Identidade**, execute as seguintes etapas:

             ![Captura de tela da seção Provedor de Identidade](./media/servicenow-tutorial/automatic-config.png "Configurar o logon único")

               a. Para **Nome**, insira um nome para a configuração (por exemplo, **Logon Único Federado do Microsoft Azure**).

               b. Copie o valor da **Página inicial do ServiceNow** e cole-o na **URL de logon** da seção **Configuração de SAML Básica do ServiceNow** do portal do Azure.

                > [!NOTE]
                > A home page da instância de ServiceNow é uma concatenação da **URL do locatário do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

              c. Copie o valor **da ID de Entidade/do Emissor** e cole-o no **Identificador** da seção **Configuração de SAML Básica do ServiceNow** do portal do Azure.

              d. Verifique se a **Política NameID** está definida com o valor `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. 

              e. Clique em **Avançado** e forneça o valor do **Script de Logon Único** de **MultiSSOv2_SAML2_custom**.

         1. Role para baixo até a seção **Certificado X.509** e selecione **Editar**.

             ![Captura de tela da seção Certificado X.509, com "Editar" realçado](./media/servicenow-tutorial/tutorial-servicenow-09.png "Configurar o logon único")

         1. Selecione o certificado e clique no ícone de seta à direita para adicionar o certificado

            ![Captura de tela da Coleção, com o certificado e o ícone de seta para a direita realçados](./media/servicenow-tutorial/tutorial-servicenow-11.png "Configurar o logon único")

          1. Clique em **Salvar**.

          1. Selecione **Testar conexão**, no canto superior direito da página.

             ![Captura de tela da página, com "Testar Conexão" realçado](./media/servicenow-tutorial/tutorial-activate-2.png "Ativar plug-in")

             > [!NOTE]
             > Se a conexão de teste estiver falhando e você não puder ativar esta conexão, o ServiceNow oferecerá a opção de substituição. Você precisa inserir **Sys_properties.LIST** em **Navegação de Pesquisa**, que abrirá a nova página de Propriedades do Sistema. Aqui, você precisa criar uma propriedade com o nome **glide.authenticate.multisso.test.connection.mandatory** com **datatype** como **True/False** e, em seguida, definir **value** como **False**.

             > ![Captura de tela da página Testar conexão](./media/servicenow-tutorial/test-connection-fail.png "Configurar o logon único")
        
          1. Quando suas credenciais forem solicitadas, insira-as. Você verá a página a seguir. O erro nos **Resultados de Teste de Logoff do SSO** é esperado. Ignore o erro e selecione **Ativar**.

             ![Captura de tela da página de credenciais](./media/servicenow-tutorial/servicenow-activate.png "Configurar o logon único")
  
1. Para configurar o **ServiceNow** manualmente, execute as seguintes etapas:

    1. Entre no seu aplicativo ServiceNow como administrador.

    1. No painel esquerdo, selecione **Provedores de Identidade**.

        ![Captura de tela de SSO para Vários Provedores, com "Provedores de Identidade" realçado](./media/servicenow-tutorial/tutorial-servicenow-07.png "Configurar o logon único")

    1. Na caixa de diálogo **Provedores de Identidade**, selecione **Novo**.

        ![Captura de tela da caixa de diálogo Provedores de Identidade, com "Novo" realçado](./media/servicenow-tutorial/ic7694977.png "Configurar o logon único")

    1. Na caixa de diálogo **Provedores de Identidade**, selecione **SAML**.

        ![Captura de tela da caixa de diálogo Provedores de Identidade, com "SAML" realçado](./media/servicenow-tutorial/ic7694978.png "Configurar o logon único")

    1. Em **Importar metadados do Provedor de Identidade**, execute as seguintes etapas:

        ![Captura de tela de Importar Metadados do Provedor de Identidade, com "URL" e "Importar" realçados](./media/servicenow-tutorial/idp.png "Configurar o logon único")

        1. Insira a **URL de Metadados de Federação de Aplicativos** que você copiou do portal do Azure.

        1. Selecione **Importar**.

    1. A URL de metadados do IdP será lida e todas as informações dos campos serão populadas.

        ![Captura de tela de Provedor de Identidade](./media/servicenow-tutorial/ic7694982.png "Configurar o logon único")

        a. Para **Nome**, insira um nome para a configuração (por exemplo, **Logon Único Federado do Microsoft Azure**).

        b. Copie o valor da **Página inicial do ServiceNow**. Cole-o na **URL de logon**, na seção **Configuração de SAML Básica do ServiceNow** do portal do Azure.

        > [!NOTE]
        > A home page da instância de ServiceNow é uma concatenação da **URL do locatário do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

        c. Copie o valor **da ID da Entidade/do Emissor**. Cole-o em **Identificador**, na seção **Configuração de SAML Básica do ServiceNow** do portal do Azure.

        d. Verifique se a **Política NameID** está definida com o valor `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        e. Selecione **Avançado**. No **Campo do Usuário**, insira **email**.

        > [!NOTE]
        > Você pode configurar o Azure AD para emitir a ID de usuário do Azure AD (nome UPN) ou o endereço de email como o identificador exclusivo no token SAML. Faça isso acessando a seção **ServiceNow** > **Atributos** > **Logon único** do portal do Azure e mapeando o campo desejado para o atributo **nameidentifier**. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome UPN) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, nome_do_usuário).

        g. Selecione **Testar conexão**, no canto superior direito da página.

        > [!NOTE]
        > Se a conexão de teste estiver falhando e você não puder ativar esta conexão, o ServiceNow oferecerá a opção de substituição. Você precisa inserir **Sys_properties.LIST** em **Navegação de Pesquisa**, que abrirá a nova página de Propriedades do Sistema. Aqui, você precisa criar uma propriedade com o nome **glide.authenticate.multisso.test.connection.mandatory** com **datatype** como **True/False** e, em seguida, definir **value** como **False**.

          > ![Captura de tela de Testar conexão](./media/servicenow-tutorial/test-connection-fail.png "Configurar o logon único")

        h. Quando suas credenciais forem solicitadas, insira-as. Você verá a página a seguir. O erro nos **Resultados de Teste de Logoff do SSO** é esperado. Ignore o erro e selecione **Ativar**.

          ![credentials](./media/servicenow-tutorial/servicenow-activate.png "Configurar o logon único")

### <a name="create-servicenow-test-user"></a>Criar um usuário de teste do ServiceNow

O objetivo desta seção é criar um usuário chamado B.Fernandes no ServiceNow. O ServiceNow é compatível com o provisionamento automático de usuários, que está habilitado por padrão.

> [!NOTE]
> Se for necessário criar um usuário manualmente, entre em contato com a [Equipe de suporte ao cliente do ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Configurar o SSO do ServiceNow Express

1. Entre no seu aplicativo ServiceNow Express como administrador.

2. No painel à esquerda, selecione **Logon Único**.

    ![Captura de tela do aplicativo ServiceNow Express, com "Logon Único" realçado](./media/servicenow-tutorial/ic7694980ex.png "Configurar a URL do aplicativo")

3. Na caixa de diálogo **Logon Único**, selecione o ícone de configuração no canto superior direito e defina as seguintes propriedades:

    ![Captura de tela da caixa de diálogo Logon Único](./media/servicenow-tutorial/ic7694981ex.png "Configurar a URL do aplicativo")

    a. Ative/desative **Habilitar SSO de vários provedores** à direita.

    b. Ative/desative **Habilitar registro em log de depuração para a integração de SSO de vários provedores** à direita.

    c. Em **O campo da tabela do usuário que...** , insira **nome_do_usuário**.

4. Na caixa de diálogo **Logon Único**, selecione **Adicionar Novo Certificado**.

    ![Captura de tela da caixa de diálogo Logon Único, com "Adicionar Novo Certificado" realçado](./media/servicenow-tutorial/ic7694973ex.png "Configurar o logon único")

5. Na caixa de diálogo **Certificados X.509**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Certificados X.509](./media/servicenow-tutorial/ic7694975.png "Configurar o logon único")

    a. Para **Nome**, insira um nome para a configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **Ativo**.

    c. Para **Formato**, selecione **PEM**.

    d. Para **Tipo**, selecione **Confiar nos Certificados do Repositório**.

    e. Abra no bloco de notas o certificado codificado com Base64 baixado do portal do Azure. Copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado PEM**.

    f. Selecione **Atualizar**

6. Na caixa de diálogo **Logon Único**, selecione **Adicionar Novo IdP**.

    ![Captura de tela da caixa de diálogo Logon Único, com "Adicionar Novo IdP" realçado](./media/servicenow-tutorial/ic7694976ex.png "Configurar o logon único")

7. Na caixa de diálogo **Adicionar Novo Provedor de Identidade**, em **Configurar Provedor de Identidade**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Adicionar Novo Provedor de Identidade](./media/servicenow-tutorial/ic7694982ex.png "Configurar o logon único")

    a. Para **Nome**, insira um nome para a configuração (por exemplo: **SAML 2.0**).

    b. Para **URL do Provedor de Identidade**, cole o valor da ID do Provedor de Identidade que você copiou do portal do Azure.

    c. Para **AuthnRequest do Provedor de Identidade**, cole o valor da URL da solicitação de autenticação que você copiou do portal do Azure.

    d. Para **SingleLogoutRequest do Provedor de Identidade**, cole o valor da URL de logoff que você copiou do portal do Azure.

    e. Para **Certificado de Provedor de Identidade**, selecione o certificado que você criou na etapa anterior.

8. Selecione **Configurações avançadas**. Em **Propriedades Adicionais do Provedor de Identidade**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Adicionar Novo Provedor de Identidade, com "Configurações Avançadas" realçado](./media/servicenow-tutorial/ic7694983ex.png "Configurar o logon único")

    a. Para **Associação de protocolo para o SingleLogoutRequest do IDP**, insira **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Para **Política NameID**, insira **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. Para **Método AuthnContextClassRef**, insira `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Para **Criar uma AuthnContextClass**, alterne o campo para desativado (desmarcado).

9. Em **Propriedades Adicionais do Provedor de Serviço**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Adicionar Novo Provedor de Identidade, com diversas propriedades realçadas](./media/servicenow-tutorial/ic7694984ex.png "Configurar o logon único")

    a. Para **Página Inicial do ServiceNow**, insira a URL da página inicial da sua instância do ServiceNow.

    > [!NOTE]
    > A home page da instância de ServiceNow é uma concatenação da **URL do locatário do ServiceNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. Para **ID da Entidade/Emissor**, insira a URL do seu locatário do ServiceNow.

    c. Para **URI do Público-alvo**, insira a URL do seu locatário do ServiceNow.

    d. Para **Defasagem Horária**, insira **60**.

    e. Para **Campo do usuário**, insira **email**.

    > [!NOTE]
    > Você pode configurar o Azure AD para emitir a ID de usuário do Azure AD (nome UPN) ou o endereço de email como o identificador exclusivo no token SAML. Faça isso acessando a seção **ServiceNow** > **Atributos** > **Logon único** do portal do Azure e mapeando o campo desejado para o atributo **nameidentifier**. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome UPN) deve corresponder ao valor armazenado no ServiceNow para o campo inserido (por exemplo, nome_do_usuário).

    f. Clique em **Salvar**.

## <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do ServiceNow no Painel de Acesso, você deverá ser conectado automaticamente ao ServiceNow no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testar o SSO para o ServiceNow Clássico (móvel)

1. Abra o seu aplicativo **ServiceNow Clássico (móvel)** e execute as seguintes etapas:

    a. Selecione o sinal de adição no canto inferior direito.

    ![Captura de tela do aplicativo ServiceNow Clássico, com o sinal de adição realçado](./media/servicenow-tutorial/test-03.png)

    b. Digite o nome da Instância do ServiceNow e selecione **Continuar**.

    ![Captura de tela da página Adicionar Instância, com Continuar realçado](./media/servicenow-tutorial/test-04.png)

    c. Na página de **Logon**, execute as seguintes etapas:

    ![Captura de tela da página de logon, com Usar o logon externo realçado](./media/servicenow-tutorial/test-01.png)

    *  Insira o **Nome de usuário**, como B.simon@contoso.com.

    *  Selecione **USAR LOGON EXTERNO**. Você será redirecionado à página do Azure AD para entrar.

    *  Insira suas credenciais. Se houver alguma autenticação de terceiros ou qualquer outro recurso de segurança habilitado, o usuário deverá responder de acordo. O aplicativo **Página Inicial** será exibido.

        ![Captura de tela da Página Inicial do aplicativo](./media/servicenow-tutorial/test-02.png)

## <a name="next-steps"></a>Próximas etapas

Após configurar o ServiceNow, você poderá impor controles de sessão, que protegem contra a exfiltração e a infiltração de dados confidenciais da sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
