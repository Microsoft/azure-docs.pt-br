---
title: 'Tutorial: Integração do Azure Active Directory ao SAP HANA | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231b9b6d217a9ad1fe5f4a6478f5e8799257b92b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091621"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: Integração do Azure Active Directory ao SAP HANA

Neste tutorial, você aprenderá a integrar o SAP HANA ao Azure AD (Azure Active Directory).
A integração do SAP HANA ao Azure AD oferece os seguintes benefícios:

* No Azure Active Directory, é possível controlar quem tem acesso ao SAP HANA.
* Você pode permitir que seus usuários entrem automaticamente no SAP HANA (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP HANA, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do SAP HANA com SSO (logon único) habilitado
- Uma instância do HANA em execução em qualquer IaaS público, localmente, em VMs do Azure ou em grandes instâncias SAP no Azure
- A Interface da Web de Administração do XSA, bem como o HANA Studio instalado na instância do HANA

> [!NOTE]
> Não recomendamos o uso de um ambiente de produção do SAP HANA para testar as etapas neste tutorial. Teste a integração primeiro no ambiente de desenvolvimento ou de preparo do aplicativo e, em seguida, use o ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do SAP HANA habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SAP HANA é compatível com SSO iniciado por **IDP**
* O SAP HANA dá suporte ao provisionamento **Just-In-Time** do usuário

## <a name="adding-sap-hana-from-the-gallery"></a>Adicionar o SAP HANA da galeria

Para configurar a integração do SAP HANA ao Azure AD, você precisará adicionar o SAP HANA da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP HANA da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SAP HANA**, selecione **SAP HANA** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![SAP HANA na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SAP HANA, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP HANA.

Para configurar e testar o logon único do Azure AD com o SAP HANA, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do SAP HANA](#configure-sap-hana-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SAP HANA](#create-sap-hana-test-user)** – para ter um equivalente de Brenda Fernandes no SAP HANA vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SAP HANA, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **SAP HANA**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SAP HANA](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite as seguintes informações: `HA100`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do SAP HANA](https://cloudplatform.sap.com/contact.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo SAP HANA espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

6. Na seção **Atributos do usuário** da caixa de diálogo **Declarações e Atributos do Usuário**, realize as seguintes etapas:
 
    a. Clique no **ícone Editar** para abrir a caixa de diálogo **Gerenciar declarações do usuário**.

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Na lista **Transformação**, selecione **ExtractMailPrefix()** .

    c. Na lista **Parâmetro 1**, selecione **user.mail**.

    d. Clique em **Save** (Salvar).

7. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Configurar o logon único do SAP HANA

1. Para configurar o logon único no lado do SAP HANA, faça logon no seu **Console Web XSA do HANA** navegando até o respectivo end-point de HTTPS.

    > [!NOTE]
    > Na configuração padrão, a URL redireciona a solicitação para uma tela de logon, o que exige as credenciais de um usuário de banco de dados SAP HANA autenticado. O usuário que faz logon deve ter permissões para executar tarefas de administração do SAML.

2. Na Interface da Web XSA, acesse **Provedor de identidade do SAML**. A partir daí, selecione o botão **+** na parte inferior da tela para exibir o painel **Adicionar informações do provedor de identidade**. Em seguida, execute as etapas a seguir:

    ![Adicionar Provedor de Identidade](./media/saphana-tutorial/sap1.png)

    a. No painel **Adicionar informações do provedor de identidade**, cole o conteúdo do XML de metadados (que você fez o download do Portal do Azure) na caixa de **Metadados**.

    ![Configurações para Adicionar Provedor de Identidade](./media/saphana-tutorial/sap2.png)

    b. Se o conteúdo do documento XML for válido, o processo de análise extrai as informações necessárias para os campos **Assunto, ID de entidade e emissor** na área da tela de **Dados gerais**. Ele também extrai as informações necessárias para os campos de URL na área da tela de **Destino**, por exemplo, os campos de  **URL de Base e URL de Logon único (*)** .

    ![Configurações para Adicionar Provedor de Identidade](./media/saphana-tutorial/sap3.png)

    c. Na caixa **Nome** da área **Dados Gerais** da tela, insira um nome para o novo provedor de identidade SSO de SAML.

    > [!NOTE]
    > O nome do IDP do SAML é obrigatório e deve ser único. Ele aparece na lista de IDPs do SAML disponíveis que é exibida quando você seleciona o SAML como o método de autenticação para aplicativos do SAP HANA XS. Por exemplo, você pode fazer isso na área da tela **Autenticação** da ferramenta de administração do artefato XS.

3. Selecione **Salvar** para salvar os detalhes do provedor de identidade SAML e adicione o novo IdP de SAML à lista de IdPs de SAML conhecidos.

    ![Botão Salvar](./media/saphana-tutorial/sap4.png)

4. No Studio HANA, dentro das propriedades do sistema do guia **Configuração**, filtre as configurações por **saml**. Em seguida, ajuste **assertion_timeout** de **10 segundos** para **120 segundos**.

    ![configuração assertion_timeout](./media/saphana-tutorial/sap7.png)

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SAP HANA.

1. No portal do Azure, escolha **Aplicativos empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **SAP HANA**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e escolha **SAP HANA**.

    ![O link do SAP HANA na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sap-hana-test-user"></a>Criar um usuário de teste do SAP HANA

Para permitir que os usuários do Azure Active Directory entrem no SAP HANA, você deverá provisioná-los no SAP HANA.
O SAP HANA dá suporte ao **provisionamento Just-In-Time**, que está habilitado por padrão.

Se você precisar criar um usuário manualmente, execute as seguintes etapas:

>[!NOTE]
>Você pode alterar a autenticação externa usada pelo usuário. Eles poderão autenticar com um sistema externo, como o Kerberos. Para obter informações detalhadas sobre identidades externas, contate seu [administrador de domínio](https://cloudplatform.sap.com/contact.html).

1. Abra o [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador e habilite o usuário do BD para SSO do SAML.

    ![Criar usuário](./media/saphana-tutorial/sap5.png)

2. Selecione a caixa de seleção invisível à esquerda do **SAML** e selecione o link **Configurar**.

3. Selecione **Adicionar** para adicionar o IDP do SAML.  Selecione o IDP do SAML apropriado e, em seguida, selecione **OK**.

4. Adicione a **Identidade externa** (nesse caso, BrittaSimon) ou escolha **Qualquer**. Depois, selecione **OK**.

   > [!Note]
   > Se a caixa de seleção **Qualquer** não estiver marcada, o nome de usuário no HANA deverá corresponder exatamente ao nome do usuário no nome UPN antes do sufixo de domínio. (Por exemplo, BrittaSimon@contoso.com se torna BrittaSimon em HANA.)

5. Para fins de teste, atribua todas as funções **XS** ao usuário.

    ![Atribuição de funções](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Você deve conceder as permissões apropriadas apenas para seus casos de uso.

6. Salve o usuário.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SAP HANA no Painel de Acesso, você deverá ser conectado automaticamente ao SAP HANA no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

