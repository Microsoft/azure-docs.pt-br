---
title: 'Tutorial: Integração do Azure Active Directory à SAP Cloud Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13ab9752391519f38e843b9d625164e655348d28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091788"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: Integração do Azure Active Directory ao SAP Cloud Platform

Neste tutorial, você aprende a integrar a SAP Cloud Platform ao Azure Active Directory (Azure AD).
A integração da SAP Cloud Platform ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso à SAP Cloud Platform.
* Você pode permitir que seus usuários entrem automaticamente no SAP Cloud Platform (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD à SAP Cloud Platform, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único da SAP Cloud Platform

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu à SAP Cloud Platform poderão fazer logon único no aplicativo usando a [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Você precisa implantar seu próprio aplicativo ou assinar um aplicativo em sua conta da SAP Cloud Platform para testar o logon único. Neste tutorial, um aplicativo é implantado na conta.
> 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SAP Cloud Platform dá suporte a SSO iniciado por **SP**

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Adicionar a SAP Cloud Platform por meio da galeria

Para configurar a integração da SAP Cloud Platform ao Azure AD, você precisa adicionar a SAP Cloud Platform à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar a SAP Cloud Platform por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SAP Cloud Platform**, selecione **SAP Cloud Platform** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![SAP Cloud Platform na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com a SAP Cloud Platform, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SAP Cloud Platform.

Para configurar e testar o logon único do Azure AD com a SAP Cloud Platform, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do SAP Cloud Platform](#configure-sap-cloud-platform-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste da SAP Cloud Platform](#create-sap-cloud-platform-test-user)** – para ter um equivalente de Brenda Fernandes no SAP Cloud Platform que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com a SAP Cloud Platform, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SAP Cloud Platform**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs da SAP Cloud Platform](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo **SAP Cloud Platform**. Esta é a URL específica da conta de um recurso protegido em seu aplicativo SAP Cloud Platform. A URL é baseada no seguinte padrão: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Esta é a URL em seu aplicativo SAP Cloud Platform que exige que o usuário seja autenticado.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Na caixa de texto **Identificador** que você irá fornecer à sua SAP Cloud Platform digite uma URL usando um dos seguintes padrões: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com [equipe de suporte do cliente da SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) para obter a URL de logon e o Identificador. A URL de resposta você pode obter da seção de gerenciamento de confiança que é explicada no tutorial posteriormente.
    > 
4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com suas necessidades e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Configurar o logon único do SAP Cloud Platform

1. Em uma janela diferente do navegador, inicie uma sessão na ferramenta cockpit da SAP Cloud Platform em `https://account.<landscape host>.ondemand.com/cockpit`(por exemplo: https://account.hanatrial.ondemand.com/cockpit).

2. Clique na guia **Confiar** .
   
    ![Confiança](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Confiança")

3. Na seção Gerenciamento de confiança, em **Provedor de serviços local**, execute as seguintes etapas:

    ![Gerenciamento de Confiança](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Gerenciamento de Confiança")
   
    a. Clique em **Editar**.

    b. Para **Tipo de Configuração**, selecione **Personalizado**.

    c. Para **Nome do Provedor Local**, deixe o valor padrão. Copie esse valor e cole-o no campo **Identificador** na configuração do Azure AD para a SAP Cloud Platform.

    d. Para gerar um par de chaves **Chave de Assinatura** e um **Certificado de Assinatura**, clique em **Gerar Par de Chaves**.

    e. Para **Propagação de Entidade**, selecione **Desabilitado**.

    f. Para **Forçar Autenticação**, selecione **Desabilitado**.

    g. Clique em **Save** (Salvar).

4. Depois de salvar as configurações do **Provedor de serviços Llocal**, faça o seguinte para obter a URL de resposta:
   
    ![Obter Metadados](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Obter Metadados")

    a. Baixe o arquivo de metadados da SAP Cloud Platform, clicando em **Obter metadados**.

    b. Abra o arquivo XML de metadados da SAP Cloud Platform baixado e localize a marca **ns3:AssertionConsumerService** .
 
    c. Copie o valor do atributo **Location** e cole-o no campo**URL de Resposta** na configuração do Azure AD da SAP Cloud Platform.

5. Clique na guia **Provedor de Identidade Confiável** e em **Adicionar Provedor de Identidade Confiável**.
   
    ![Gerenciamento de Confiança](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Gerenciamento de Confiança")
   
    >[!NOTE]
    >Para gerenciar a lista de provedores de identidade confiáveis, será necessário ter escolhido o Tipo de configuração personalizado na seção Provedor de Serviço Local. Para o tipo de configuração Padrão, você terá uma confiança implícita e não editável para o Serviço de ID do SAP. Para Nenhum, não há configurações de confiança.
    > 
    > 

6. Clique na guia **Geral** e em **Procurar** para carregar o arquivo de metadados baixado.
    
    ![Gerenciamento de Confiança](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Gerenciamento de Confiança")
    
    >[!NOTE]
    >Depois de carregar o arquivo de metadados, os valores de **URL de Logon Único**, **URL de Logoff Único** e o **Certificado de Autenticação** serão automaticamente populados.
    > 
     
7. Clique na guia **Atributos** .

8. Na guia **Atributos**, execute a seguinte etapa:
    
    ![Atributos](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Atributos") 

    a. Clique em **Adicionar Atributo Baseado em Declaração** e adicione os seguintes atributos baseados em declaração:
       
    | Atributo de Asserção | Atributo de Entidade |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |nome |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |sobrenome |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >A configuração dos Atributos depende de como os aplicativos na HCP são desenvolvidos, isto é, quais atributos eles esperam ter na resposta do SAML e por qual nome (Atributo de Entidade de Segurança) eles acessam esse atributo no código.
     > 
    
    b. O **Atributo Padrão** na captura de tela serve apenas para fins de ilustração. Ele não é necessário para que o cenário funcione.  
 
    c. Os nomes e valores do **Atributo de Entidade** mostrados na captura de tela dependerão de como o aplicativo foi desenvolvido. É possível que o seu aplicativo precise de mapeamentos diferentes.

### <a name="assertion-based-groups"></a>Grupos baseados em asserção

Como uma etapa opcional, você pode configurar grupos com base na asserção para seu Provedor de Identidade do Azure Active Directory.

Usar grupos na SAP Cloud Platform permite que você atribua dinamicamente um ou mais usuários a uma ou mais funções em seus aplicativos da SAP Cloud Platform, determinados pelos valores de atributos na asserção SAML 2.0. 

Por exemplo, se a declaração contém o atributo "*contract=temporary*", talvez seja conveniente que todos os usuários afetados sejam adicionados ao grupo "*TEMPORARY*". O grupo “*TEMPORARY*” pode conter uma ou mais funções de um ou mais aplicativos implantados em sua conta da SAP Cloud Platform.
 
Use os grupos baseados em declarações se desejar atribuir vários usuários simultaneamente a uma ou mais funções de aplicativos em sua conta da SAP Cloud Platform. Se você quer atribuir apenas um único usuário ou alguns deles a funções específicas, recomendamos atribuí-los diretamente na guia "**Autorizações**" da ferramenta cockpit da SAP Cloud Platform.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso à SAP Cloud Platform.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **SAP Cloud Platform**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **SAP Cloud Platform**.

    ![Link da SAP Cloud Platform na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sap-cloud-platform-test-user"></a>Criar um usuário da SAP Cloud Platform

Para permitir que os usuários do Azure AD façam logon na SAP Cloud Platform, atribua funções a eles na SAP Cloud Platform.

**Para atribuir uma função a um usuário, execute as seguintes etapas:**

1. Faça logon em sua ferramenta cockpit da **SAP Cloud Platform** .

2. Realize o que é descrito a seguir:
   
    ![Autorizações](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autorizações")
   
    a. Clique em **Autorização**.

    b. Clique na guia **Usuários** .

    c. Na caixa de texto **Usuário** , digite o endereço de email do usuário.

    d. Clique em **Atribuir** para atribuir uma função ao usuário.

    e. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SAP Cloud Platform no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo SAP Cloud Platform para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

