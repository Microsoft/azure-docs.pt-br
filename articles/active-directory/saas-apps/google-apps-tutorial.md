---
title: 'Tutorial: Integração do Azure Active Directory com o G Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8196576c4b419fdfc8f7e3adf5c474ef128ebbf6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101620"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Tutorial: Integração do Azure Active Directory com o G Suite

Neste tutorial, você aprenderá a integrar o G Suite ao Microsoft Azure AD (Azure Active Directory).
A integração do G Suite ao Microsoft Azure AD oferece os seguintes benefícios:

* No Microsoft Azure AD, você pode controlar quem tem acesso ao G Suite.
* Você pode permitir que seus usuários entrem automaticamente no G Suite (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD com o G Suite, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do G Suite
- Uma assinatura do Google Apps ou uma assinatura do Google Cloud Platform.

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. Este documento foi criado usando o novo usuário, experiência de logon único. Se você ainda estiver usando o antigo, a instalação terá uma aparência diferente. Você pode habilitar a nova experiência nas configurações de logon único do aplicativo do G Suite. Vá para **Azure Active Directory, aplicativos empresariais**, selecione **G Suite**, selecione **Single Sign-on** e, em seguida, clique em **Tentar nossa nova experiência**.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. **P: Essa integração dá suporte para integração de SSO do Google Cloud Platform com o Microsoft Azure AD?**

    R: Sim. O Google Cloud Platform e o Google Apps compartilham a mesma plataforma de autenticação. Portanto, para fazer a integração com o GCP, será necessário configurar o SSO com o Google Apps.

2. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com o logon único do AD do AD do Azure?**
  
    R: Sim. Os usuários podem entrar em seus dispositivos Chromebook usando suas credenciais do Azure AD. Consulte este [artigo de suporte do G Suite](https://support.google.com/chrome/a/answer/6060880) para obter informações sobre o motivo dos usuários receberem solicitações de credenciais duas vezes.

3. **P: Se eu habilitar o logon único, os usuários conseguirão usar suas credenciais do Azure AD para entrar em qualquer produto do Google, como Google Sala de aula, GMail, Google Drive, YouTube e assim por diante?**

    R: Sim, dependendo de [qual G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) você quer habilitar ou desabilitar para sua organização.

4. **P: Eu posso habilitar logon único apenas para um subconjunto dos meus usuários do G Suite?**

    R: Não, ativar o logon único exige que todos os usuários do G Suite autentiquem-se imediatamente com as credenciais do Microsoft Azure AD. Como o G Suite não dá suporte a vários provedores de identidade, o provedor de identidade do ambiente do G Suite pode ser o Microsoft Azure AD ou o Google -- mas não os dois ao mesmo tempo.

5. **P: Se um usuário conectar através do Windows, ele será autenticado automaticamente no G Suite sem ser solicitado a fornecer uma senha?**

    R: Há duas opções para este cenário. Primeiro, os usuários podem entrar em dispositivos com Windows 10 por meio do [Ingresso no Active Directory do Azure](../device-management-introduction.md). Como alternativa, os usuários podem entrar em dispositivos com Windows que ingressaram em um domínio para um Active Directory local com logon único habilitado no AD do Azure por meio de uma implantação dos [Serviços de Federação do Active Directory (AD FS)](../hybrid/plan-connect-user-signin.md) . Ambas as opções exigem que você execute as etapas do tutorial a seguir para ativar o logon único entre o Microsoft Azure AD e o G Suite.

6. **P: O que devo fazer quando recebo uma mensagem de erro de "email inválido"?**

    R: Para essa configuração, o atributo de email é necessário para os usuários entrarem. Esse atributo não pode ser definido manualmente.

    O atributo de email é populado automaticamente para qualquer usuário com uma licença válida do Exchange. Se o usuário não estiver habilitado para email, esse erro será recebido, o aplicativo precisa obter esse atributo para fornecer acesso.

    Você pode ir para portal.office.com com uma conta de Administrador, em seguida, clique no Centro de administração, cobrança, assinaturas, selecione sua assinatura do Office 365 e, em seguida, clique em atribuir a usuários, selecione os usuários que você deseja verificar a sua assinatura e no painel direito, clique em editar licenças.

    Depois que a licença do O365 for atribuída, pode levar alguns minutos para ser aplicado. Depois disso, o atributo user.mai será preenchido automaticamente e o problema deve ser resolvido.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O G Suite dá suporte ao SSO iniciado por **SP**
* O G Suite dá suporte a **[provisionamento automático de usuários](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)**

## <a name="adding-g-suite-from-the-gallery"></a>Adicionar o G Suite da galeria

Para configurar a integração do G Suite ao Microsoft Azure AD, é necessário adicionar o G Suite da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o G Suite da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **G Suite**, selecione **G Suite** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![G Suite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o G Suite, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do G Suite.

Para configurar e testar o logon único do Microsoft Azure AD com o G Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do G Suite](#configure-g-suite-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do G Suite](#create-g-suite-test-user)** – para ter um equivalente de Brenda Fernandes no G Suite vinculado à representação do usuário no Microsoft Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Microsoft Azure AD com o G Suite, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), no **G Suite** página de integração de aplicativos, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje realizar a configuração para o **Gmail**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do G Suite](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do G Suite](https://www.google.com/contact/) para obter esses valores.

5. Na seção **Configuração Básica do SAML**, caso deseje realizar a configuração para a **Google Cloud Platform**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do G Suite](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do G Suite](https://www.google.com/contact/) para obter esses valores.

6. Seu aplicativo G Suite espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **Identificador Exclusivo de Usuário** é **user.userprincipalname**, mas o G Suite espera que isso seja mapeado com o endereço de email do usuário. Para que você possa usar o atributo **user. mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização.

    ![image](common/edit-attribute.png)

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | NOME | Atributo de Origem |
    | ---------------| --------------- |
    | Identificador Exclusivo do Usuário | User.mail |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

9. Na seção **Configurar o G Suite**, copie as URLs apropriadas, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-g-suite-single-sign-on"></a>Configurar o logon único do G Suite

1. Abra uma nova guia no navegador e entre no [Console de Admin do G Suite](https://admin.google.com/) usando sua conta de administrador.

2. Clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles** , na parte inferior da tela.

    ![Clique em Segurança.][10]

3. Na página **Segurança**, clique em **Configurar logon único (SSO).**

    ![Clique em SSO.][11]

4. Realize as seguintes alterações de configuração:

    ![Configure o SSO][12]

    a. Selecione **Configurar SSO com um provedor de identidade de terceiros**.

    b. No campo **URL da página de entrada** no G Suite, cole o valor da **URL de logon** que você copiou do portal do Azure.

    c. No campo **URL da página de logoff** no G Suite, cole o valor da **URL de logoff** que você copiou do portal do Azure.

    d. No campo **URL de alteração de senha** do G Suite, cole o valor da **URL de alteração de senha** copiado do Portal do Azure.

    e. No G Suite, para o **Certificado de verificação**, carregue o certificado baixado do Portal do Azure.

    f. Selecione **Usar um emissor de domínio específico**.

    g. Clique em **Salvar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao G Suite.

1. No portal do Azure, escolha **Aplicativos Empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **G Suite**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **G Suite**.

    ![O link do G Suite na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-g-suite-test-user"></a>Criar um usuário de teste do G Suite

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no G Suite Software. O G Suite dá suporte ao provisionamento automático, que é habilitado por padrão. Não há nenhuma ação para você nesta seção. Se um usuário ainda não existir no G Suite Software, um novo será criado quando você tentar acessar o G Suite Software.

> [!NOTE]
> Verifique se o usuário já existe no G Suite se o provisionamento no Microsoft Azure Active Directory não foi ativado antes de testar o logon único.

> [!NOTE]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Google](https://www.google.com/contact/).

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do G Suite no Painel de Acesso, você deverá ser conectado automaticamente ao G Suite no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Configurar Provisionamento de Usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
