---
title: 'Tutorial: integração do Azure Active Directory com o SAP HANA | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP HANA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1a1e155974b66dce9a036a20cdebe19ded81fed5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727074"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: integração do Azure Active Directory com o SAP HANA

Neste tutorial, você aprenderá a integrar o SAP HANA ao Azure AD (Azure Active Directory). Com a integração do SAP HANA ao Azure AD, você pode:

* Controlar no Azure AD que tem acesso ao SAP HANA.
* Permitir que os usuários sejam conectados automaticamente ao SAP HANA usando suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

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

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.


## <a name="adding-sap-hana-from-the-gallery"></a>Adicionar o SAP HANA da galeria

Para configurar a integração do SAP HANA ao Azure AD, você precisará adicionar o SAP HANA da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, insira **SAP HANA** na caixa de pesquisa.
1. Selecione **SAP HANA** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Configurar e testar o SSO do Azure AD para o SAP HANA

Configure e teste o SSO do Azure AD com o SAP HANA com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP HANA.

Para configurar e testar o SSO do Azure AD com o SAP HANA, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do SAP HANA](#configure-sap-hana-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SAP HANA](#create-sap-hana-test-user)** – para ter um equivalente de Brenda Fernandes no SAP HANA vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SAP HANA**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize o valor com a URL de Resposta real. Contate a [equipe de suporte ao cliente do SAP HANA](https://cloudplatform.sap.com/contact.html) para obter os valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo SAP HANA espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![Captura de tela que mostra a seção "Atributos do Usuário" com o ícone "Editar" selecionado.](common/edit-attribute.png)

6. Na seção **Atributos do usuário** da caixa de diálogo **Declarações e Atributos do Usuário**, realize as seguintes etapas:
 
    a. Clique no **ícone Editar** para abrir a caixa de diálogo **Gerenciar declarações do usuário**.

    ![Captura de tela que mostra a caixa de diálogo "Atributos e Declarações do Usuário" com o ícone "Editar" selecionado.](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Na lista **Transformação**, selecione **ExtractMailPrefix()** .

    c. Na lista **Parâmetro 1**, selecione **user.mail**.

    d. Clique em **Save** (Salvar).

7. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela o acesso ao SAP HANA.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP HANA**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sap-hana-sso"></a>Configurar o SSO do SAP HANA

1. Para configurar o logon único no lado do SAP HANA, faça logon no seu **Console Web XSA do HANA** navegando até o respectivo end-point de HTTPS.

    > [!NOTE]
    > Na configuração padrão, a URL redireciona a solicitação para uma tela de logon, o que exige as credenciais de um usuário de banco de dados SAP HANA autenticado. O usuário que faz logon deve ter permissões para executar tarefas de administração do SAML.

2. Na Interface da Web XSA, acesse **Provedor de identidade do SAML**. A partir daí, selecione o botão **+** na parte inferior da tela para exibir o painel **Adicionar informações do provedor de identidade**. Em seguida, execute as etapas a seguir:

    ![Adicionar Provedor de Identidade](./media/saphana-tutorial/sap1.png)

    a. No painel **Adicionar informações do provedor de identidade**, cole o conteúdo do XML de metadados (que você fez o download do Portal do Azure) na caixa de **Metadados**.

    ![Captura de tela que mostra o painel "Adicionar Informações do Provedor de Identidade" com as caixas "Metadados" e "Nome" realçadas.](./media/saphana-tutorial/sap2.png)

    b. Se o conteúdo do documento XML for válido, o processo de análise extrai as informações necessárias para os campos **Assunto, ID de entidade e emissor** na área da tela de **Dados gerais**. Ele também extrai as informações necessárias para os campos de URL na área da tela de **Destino**, por exemplo, os campos de  **URL de Base e URL de Logon único (*)** .

    ![Configurações para Adicionar Provedor de Identidade](./media/saphana-tutorial/sap3.png)

    c. Na caixa **Nome** da área **Dados Gerais** da tela, insira um nome para o novo provedor de identidade SSO de SAML.

    > [!NOTE]
    > O nome do IDP do SAML é obrigatório e deve ser único. Ele aparece na lista de IDPs do SAML disponíveis que é exibida quando você seleciona o SAML como o método de autenticação para aplicativos do SAP HANA XS. Por exemplo, você pode fazer isso na área da tela **Autenticação** da ferramenta de administração do artefato XS.

3. Selecione **Salvar** para salvar os detalhes do provedor de identidade SAML e adicione o novo IdP de SAML à lista de IdPs de SAML conhecidos.

    ![Botão Salvar](./media/saphana-tutorial/sap4.png)

4. No Studio HANA, dentro das propriedades do sistema do guia **Configuração**, filtre as configurações por **saml**. Em seguida, ajuste **assertion_timeout** de **10 segundos** para **120 segundos**.

    ![configuração assertion_timeout](./media/saphana-tutorial/sap7.png)


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

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao SAP HANA para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do SAP HANA em Meus Aplicativos, você deverá ser conectado automaticamente ao SAP HANA no qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SAP HANA, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).