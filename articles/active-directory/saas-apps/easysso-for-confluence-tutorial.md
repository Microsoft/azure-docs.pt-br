---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EasySSO for Confluence | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EasySSO for Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 325f6ad7d9685fac17e17b28c4ffbe31b1245cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734522"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-confluence"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EasySSO for Confluence

Neste tutorial, você aprenderá a integrar o SSO do EasySSO for Confluence ao Azure AD (Azure Active Directory). Com a integração do EasySSO for Confluence ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Confluence.
* Permitir que os usuários sejam conectados automaticamente ao Confluence com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do EasySSO for Confluence habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O EasySSO for Confluence dá suporte ao SSO iniciado por **SP e IDP**
* O EasySSO for Confluence é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-easysso-for-confluence-from-the-gallery"></a>Adicionando o EasySSO for Confluence por meio da galeria

Para configurar a integração do EasySSO for Confluence ao Azure AD, é necessário adicionar o EasySSO for Confluence à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **EasySSO for Confluence** na caixa de pesquisa.
1. Escolha **EasySSO for Confluence** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-confluence"></a>Configurar e testar o SSO do Azure AD para o EasySSO for Confluence

Configure e teste o SSO do Azure AD com o EasySSO for Confluence usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do EasySSO for Confluence.

Para configurar e testar o SSO do Azure AD com o EasySSO for Confluence, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do EasySSO for Confluence](#configure-easysso-for-confluence-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do EasySSO for Confluence](#create-easysso-for-confluence-test-user)** – para ter um equivalente de B.Fernandes no EasySSO for Jira que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **EasySSO for Confluence**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do EasySSO](mailto:support@techtime.co.nz) para obter esses valores em caso de dúvida. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Seu aplicativo EasySSO for Confluence espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo EasySSO for Confluence espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome | Atributo de Origem|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn: oid:2.5.4.4 | user.surname |
    | urn: oid:2.5.4.42 | user.givenname |
    
    Caso os usuários do Azure AD tenham o **sAMAccountName** configurado, mapeie **urn:oid:0.9.2342.19200300.100.1.1** para o atributo **sAMAccountName**.
    
1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique nos links **Baixar** do **Certificado (Base64)** ou nas opções de **XML de Metadados de Federação** e salve um deles ou todos eles no computador. Você precisará disso mais tarde para configurar Confluence EasySSO.

    ![O link de download do Certificado](./media/easysso-for-confluence-tutorial/certificate.png)
    
    Se você planeja executar o EasySSO para a configuração do Confluence manualmente com o certificado, copie também a **URL de Logon** e o **Identificador do Azure AD** na seção abaixo e salve-os no computador.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo-lhe acesso ao EasySSO for Confluence.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **EasySSO for Confluence**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-easysso-for-confluence-sso"></a>Configurar o SSO do EasySSO for Confluence

1. Para automatizar a configuração no EasySSO for Confluence, você precisa instalar a **extensão do navegador de Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o EasySSO for Confluence** para ser direcionado ao aplicativo do EasySSO for Confluence. De lá, forneça as credenciais de administrador para entrar no EasySSO for Confluence. A extensão do navegador vai configurar automaticamente o aplicativo e automatizar as etapas de 3 a 9.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o EasySSO for Confluence manualmente, entre na instância do Atlassian Confluence com privilégios de administrador e navegue até a seção **Gerenciar aplicativos**. 

    ![Gerenciar Aplicativos](./media/easysso-for-confluence-tutorial/confluence-admin-1.png)

2. No lado esquerdo, localize **EasySSO** e clique nele. Em seguida, clique no botão **Configurar**.

    ![SSO Fácil](./media/easysso-for-confluence-tutorial/confluence-admin-2.png)

3. Selecione a opção **SAML**. Isso levará para a seção de configuração do SAML.

    ![SAML](./media/easysso-for-confluence-tutorial/confluence-admin-3.png)

4. Selecione a guia **Certificados** na parte superior e você verá a seguinte tela: 

    ![URL de metadados](./media/easysso-for-confluence-tutorial/confluence-admin-4.png)

5. Agora, localize o **Certificado (Base64)** ou o **Arquivo de Metadados** que você salvou nas etapas anteriores da configuração do **SSO do Azure AD**. Você tem as seguintes opções:

    a. Use o **Arquivo de Metadados** da Federação do Aplicativo que você baixou para o arquivo local no computador. Selecione o botão de opção **Upload** e siga a caixa de diálogo de upload de arquivo específica para seu sistema operacional

    **OR**

    b. Abra o **Arquivo de Metadados** da Federação de Aplicativo para ver o conteúdo (em qualquer editor de texto sem formatação) do arquivo e copie-o na área de transferência. Selecione a opção **Entrada** e cole o conteúdo da área de transferência no campo de texto.
 
    **OR**

    c. Configuração totalmente manual. Abra o **Certificado (Base64)** da Federação de Aplicativo para ver o conteúdo (em qualquer editor de texto sem formatação) do arquivo e copie-o na área de transferência. Cole-o no campo de texto **Certificados de Autenticação de Tokens IdP**. Em seguida, navegue até a guia **Geral** e preencha os campos **URL de Associação POST** e **ID da Entidade** com os respectivos valores da **URL de Logon** e do **Identificador do Azure AD** salvos anteriormente.
 
6. Na parte inferior da página, clique no botão **Salvar**. Você verá que o conteúdo dos arquivos de Metadados ou Certificado é analisado nos campos de configuração. A configuração do EasySSO for Confluence foi concluída.

7. Para ter a melhor experiência de teste, navegue até a guia **Aparência** e habilite a opção **Botão de Logon SAML**. Isso habilitará o botão separado na tela de logon do Confluence, especificamente para testar a integração do SAML do Azure AD de ponta a ponta. Você também pode deixar esse botão ligado e configurar seu posicionamento, cor e tradução para o modo de produção.

    ![Aparência](./media/easysso-for-confluence-tutorial/confluence-admin-5.png)

    > [!NOTE]
    > Se tiver problemas, entre em contato com a [Equipe de suporte do EasySSO](mailto:support@techtime.co.nz).

### <a name="create-easysso-for-confluence-test-user"></a>Criar usuário de teste do EasySSO for Confluence

Nesta seção, um usuário chamado Brenda Fernandes será criado no Confluence. O EasySSO for Confluence dá suporte ao provisionamento de usuário Just-In-Time, que fica **desabilitado** por padrão. Para habilitar o provisionamento do usuário, você precisa marcar explicitamente a opção **Criar usuário no logon bem-sucedido** na seção Geral da configuração do plug-in EasySSO. Se um usuário ainda não existir no Confluence, um novo será criado após a autenticação.

No entanto, se você não deseja habilitar o provisionamento automático do usuário no primeiro logon do usuário, os usuários devem existir nos Diretórios de Usuários de back-end que a instância do Confluence usa, como o LDAP ou o Atlassian Crowd.

![Provisionamento do usuário](./media/easysso-for-confluence-tutorial/confluence-admin-6.png)

## <a name="test-sso"></a>Testar o SSO 

### <a name="idp-initiated-workflow"></a>Fluxo de trabalho iniciado por IdP

Nesta seção, você testará a configuração de logon único do Azure AD usando Meus Aplicativos.

Ao clicar no bloco do EasySSO for Confluence em Meus Aplicativos, você deverá ser conectado automaticamente à instância do Confluence para a qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

### <a name="sp-initiated-workflow"></a>Fluxo de trabalho iniciado por SP

Nesta seção, você testará a configuração de logon único do Azure AD usando o botão **Logon do SAML** do Confluence.

![Logon do SAML do Usuário](./media/easysso-for-confluence-tutorial/confluence-admin-7.png)

Este cenário presume que você habilitou o **Botão de Logon do SAML** na guia **Aparência** da página de configuração do Confluence EasySSO (veja acima). Abra a URL de logon do Confluence no modo anônimo do navegador para evitar qualquer interferência com as sessões existentes. Clique no botão **Logon do SAML** e você será redirecionado para o fluxo de autenticação do usuário do Azure AD. Após a conclusão com êxito, você será redirecionado novamente para a instância do Confluence como um usuário autenticado via SAML.

Há uma possibilidade de que você veja a tela a seguir depois de ser redirecionado novamente para o Azure AD

![Tela de falha do EasySSO](./media/easysso-for-confluence-tutorial/confluence-admin-8.png)

Nesse caso, você precisará seguir as [instruções desta página]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) para obter acesso ao arquivo **atlassian-confluence.log**. Os detalhes do erro estarão disponíveis pela ID de referência encontrada na página de erros do EasySSO.

Caso tenha problemas no resumo das mensagens de log, entre em contato com a [equipe de suporte do EasySSO](mailto:support@techtime.co.nz).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o EasySSO for Confluence, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e a infiltração de dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).