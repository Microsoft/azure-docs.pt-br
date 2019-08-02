---
title: 'Tutorial: Integração do Azure Active Directory à AWS (Amazon Web Services) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a AWS (Amazon Web Services).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f897653442a3e1b2d6098b3be60c85e75ca54f9a
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551488"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Tutorial: integração da AWS (Amazon Web Services) ao Azure Active Directory

Neste tutorial, você aprenderá a integrar a AWS (Amazon Web Services) ao Azure AD (Azure Active Directory). Quando você integra a AWS (Amazon Web Services) com o Azure Active Directory, pode:

* Controlar no Azure Active Directory quem tem acesso à AWS (Amazon Web Services).
* Habilitar seus usuários a fazer logon automaticamente na AWS (Amazon Web Services) usando as próprias contas do Azure Active Directory.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![AWS (Amazon Web Services)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Você pode configurar vários identificadores para várias instâncias, conforme abaixo.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com esses valores, o Azure AD removerá o valor de **#** e enviará o valor correto `https://signin.aws.amazon.com/saml` como o URL de público no token SAML.

**É recomendável usar essa abordagem pelos seguintes motivos:**

a. Cada aplicativo fornecerá a você um certificado X509 exclusivo. Cada instância da instância de aplicativo da AWS pode ter uma data de expiração do certificado diferente, que pode ser gerenciada individualmente por conta da AWS. A rolagem geral do certificado será mais fácil nesse caso.

b. Você pode ativar o aprovisionamento de usuário com o aplicativo da AWS no Azure AD e, em seguida, nosso serviço buscará todas as funções dessa conta da AWS. Você não precisa adicionar ou atualizar manualmente as funções da AWS no aplicativo.

c. Você pode atribuir o proprietário do aplicativo individualmente para o aplicativo que pode gerenciar o aplicativo diretamente no Azure AD.

> [!Note]
> Verifique se está usando apenas o aplicativo da Galeria

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) da AWS (Amazon Web Services).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. A AWS (Amazon Web Services) dá suporte ao SSO iniciado por **SP e IDP**.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar a AWS (Amazon Web Services) da galeria

Para configurar a integração da AWS (Amazon Web Services) com o Azure AD, você precisa adicionar a AWS (Amazon Web Services), por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Amazon Web Services (AWS)** na caixa de pesquisa.
1. Selecione **AWS (Amazon Web Services)** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com a AWS (Amazon Web Services) usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na AWS (Amazon Web Services).

Para configurar e testar o SSO do Azure AD com a AWS (Amazon Web Services), conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configurar a AWS (Amazon Web Services)](#configure-amazon-web-services-aws)** para definir as configurações de SSO no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste da AWS (Amazon Web Services)](#create-amazon-web-services-aws-test-user)** para ter um equivalente de B.Fernandes na AWS (Amazon Web Services) que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos da **AWS (Amazon Web Services)** , localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

5. Quando você estiver configurando mais de uma instância, forneça o valor do Identificador. De segunda instância em diante, forneça o valor do Identificador no seguinte formato. Use uma **#** entrar para especificar um valor exclusivo de SPN.

    `https://signin.aws.amazon.com/saml#2`

6. O aplicativo da AWS (Amazon Web Services) espera as declarações SAML em um formato específico, que exige que você adicione mapeamentos de atributo personalizado à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone  **Editar** para abrir a caixa de diálogo Atributos do usuário.

    ![image](common/edit-attribute.png)

7. Além do indicado acima, o aplicativo da AWS (Amazon Web Services) espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME  | Atributo de Origem  | Namespace |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Função            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "fornecer um valor entre 900 segundos (15 minutos) para segundos 43200 (12 horas)" |  https://aws.amazon.com/SAML/Attributes |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na caixa de texto **Namespace**, digite o valor de namespace mostrado para essa linha.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

1. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, localize **XML de Metadados de Federação** e escolha **Download** para fazer o download do certificado e salve-o em seu computador.

   ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar a AWS (Amazon Web Services)** , copie a URL apropriada, com base em suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-amazon-web-services-aws"></a>Configurar a AWS (Amazon Web Services)

1. Em uma janela de navegador diferente, entre no site de sua empresa do AWS (Amazon Web Services) como administrador.

2. Clique na **Página inicial do AWS**.

    ![Configurar página inicial de logon único][11]

3. Clique em **Gerenciamento de identidades e acesso**.

    ![Configurar identidade de logon único][12]

4. Clique em **Provedores de identidade**, e, em seguida, clique em **Criar provedor**.

    ![Configurar provedor de logon único][13]

5. Na página da caixa de diálogo **Configurar provedor** , execute as seguintes etapas:

    ![Configurar diálogo logon único][14]

    a. Como **Tipo de provedor**, selecione **SAML**.

    b. Na caixa de texto **Nome do provedor**, digite um nome de provedor (por exemplo: *WAAD*).

    c. Para carregar seu **arquivo de metadados** baixado do Portal do Azure, clique em **Escolher Arquivo**.

    d. Clique em **Próxima etapa**.

6. Na página de diálogo **Verificar informações do provedor**, clique em **Criar**.

    ![Configurar verificação de logon único][15]

7. Clique em **Funções** e, em seguida, clique em **Criar função**.

    ![Configurar funções de logon único][16]

8. Na página **Criar função**, realize as seguintes etapas:  

    ![Configurar confiança de logon único][19]

    a. Selecione **Federação do SAML 2.0** em **Selecionar tipo de entidade confiável**.

    b. Em **Escolher uma seção do provedor do SAML 2.0**, selecione o **provedor do SAML** criado anteriormente (por exemplo: *WAAD*)

    c. Selecione **Permitir acesso do Console de Gerenciamento do AWS e programação**.
  
    d. Clique em **Avançar: Permissões.**

9. Na caixa de diálogo **Anexar políticas de permissão**, anexe a política adequada conforme sua organização. Clique em **Avançar: Análise**.  

    ![Configurar política de logon único][33]

10. Na caixa de diálogo **Examinar** , execute as seguintes etapas:

    ![Configurar revisão de logon único][34]

    a. Na caixa de texto **Nome da função**, insira o nome da função.

    b. Na caixa de texto **Descrição da função**, insira a descrição.

    c. Clique em **Criar função**.

    d. Crie quantas funções forem necessárias e mapeie-as para o Provedor de Identidade.

11. Use as credenciais de conta de serviço AWS para buscar as funções da conta AWS no Provisionamento de Usuário do Microsoft Azure AD. Para isso, abra a página inicial do console AWS.

12. Clique em **Serviços** -> **Segurança, Identidade e Conformidade** -> **IAM**.

    ![buscando as funções da conta AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Selecione a guia **Políticas** na seção IAM.

    ![buscando as funções da conta AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Crie uma nova política, clicando em **Criar política** para efetuar fetch das funções da conta do AWS no Provisionamento de Usuário do Microsoft Azure AD.

    ![Criar nova política](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Crie sua própria política para buscar todas as funções de contas AWS, executando as seguintes etapas:

    ![Criar nova política](./media/amazon-web-service-tutorial/policy1.png)

    a. Na seção **"Criar a política"** clique na guia **"JSON"** .

    b. No documento de política, adicione o JSON abaixo.

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Clique no **botão Examinar política** para validar a política.

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy5.png)

16. Defina a **nova política** executando as etapas a seguir:

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy2.png)

    a. Forneça o **Nome da política** como **AzureAD_SSOUserRole_Policy**.

    b. Você pode fornecer **Descrição** para a política como **Essa política permitirá buscar as funções de contas AWS**.

    c. Clique no botão **"Criar política"** .

17. Crie uma nova conta de usuário no serviço de IAM do AWS executando as etapas a seguir:

    a. Clique na navegação **Usuários** no console IAM do AWS.

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy3.png)

    b. Clique no botão **Adicionar usuário** para criar um novo usuário.

    ![Adicionar usuário](./media/amazon-web-service-tutorial/policy4.png)

    c. Na seção **Adicionar usuário**, execute as etapas a seguir:

    ![Adicionar usuário](./media/amazon-web-service-tutorial/adduser1.png)

    * Digite o nome de usuário como **AzureADRoleManager**.

    * No tipo de acesso, selecione a opção **Acesso programático**. Dessa forma, o usuário pode invocar as APIs e buscar as funções da conta AWS.

    * Clique no botão **Próximas permissões** no canto inferior direito.

18. Agora, crie uma nova política para esse usuário executando as etapas a seguir:

    ![Adicionar usuário](./media/amazon-web-service-tutorial/adduser2.png)

    a. Clique no botão **Anexar políticas existentes diretamente**.

    b. Pesquise a política recém-criada na seção filtro **AzureAD_SSOUserRole_Policy**.

    c. Selecione a **política** e, em seguida, clique no botão **Próximo: Análise**.

19. Examine a política para o usuário conectado executando as etapas a seguir:

    ![Adicionar usuário](./media/amazon-web-service-tutorial/adduser3.png)

    a. Examine o nome de usuário, tipo de acesso e política mapeados para o usuário.

    b. Clique no botão **Criar usuário** no canto inferior direito para criar o usuário.

20. Faça o download das credenciais do usuário de um usuário executando as etapas a seguir:

    ![Adicionar usuário](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copie a **ID de chave de acesso** do usuário e a **Chave de acesso secreta**.

    b. Insira essas credenciais na seção de provisionamento de usuário do Microsoft Azure AD para buscar as funções do console AWS.

    c. Clique no botão **Editar** na parte inferior.

21. Navegue até a seção **Provisionamento de usuário** do aplicativo do Amazon Web Services no Portal de Gerenciamento do Microsoft Azure AD.

    ![Adicionar usuário](./media/amazon-web-service-tutorial/provisioning.png)

22. Insira a **Chave de Acesso** e o **Segredo** nos campos **Segredo do Cliente** e **Token do Segredo** respectivamente.

    ![Adicionar usuário](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Insira a chave de acesso do usuário do AWS no campo **Segredo do cliente**.

    b. Insira o segredo do usuário do AWS no campo **Segredo do Token**.

    c. Clique no botão **Testar Conexão** e você deverá poder testar com êxito essa conexão.

    d. Salve a configuração clicando no botão **Salvar** na parte superior.

23. Agora, certifique-se de habilitar o Status de Provisionamento **Ativo** na seção Configurações ativando o comutador e, em seguida, clicando no botão **Salvar** botão na parte superior.

    ![Adicionar usuário](./media/amazon-web-service-tutorial/provisioning2.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso à AWS (Amazon Web Services).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **AWS (Amazon Web Services)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar**, na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-amazon-web-services-aws-test-user"></a>Criar usuário de teste da AWS (Amazon Web Services)

O objetivo desta seção é criar uma usuária chamada B.Fernandes na AWS (Amazon Web Services). O AWS (Amazon Web Services) não precisa que um usuário seja criado em seu sistema para o SSO, portanto você não precisa realizar nenhuma ação aqui.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco AWS (Amazon Web Services) no Painel de Acesso, você deve ser automaticamente conectado à AWS (Amazon Web Services) para a qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Problemas conhecidos

 * Na seção **Provisionamento**, a subseção **Mapeamentos** mostrará uma mensagem "Carregando..." e nunca exibirá os mapeamentos de atributo. O único fluxo de trabalho de provisionamento compatível hoje é a importação de funções do AWS no Azure AD para seleção durante a atribuição de usuário/grupo. Os mapeamentos de atributo para isso são predeterminados e não configuráveis.
 
 * A seção **Provisionamento** só dá suporte a um conjunto de credenciais para um locatário do AWS por vez. Todas as funções importadas são gravadas na propriedade appRoles do [objeto servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) do Azure AD para o locatário do AWS. Vários locatários do AWS (representados por servicePrincipals) podem ser adicionados ao Azure AD da galeria para provisionamento; no entanto, há um problema conhecido por não ser possível gravar automaticamente todas as funções importadas de várias servicePrincipals do AWS usadas para provisionar na única servicePrincipal usada para logon único. Como alternativa, a [API do Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) pode ser usada para extrair todas as appRoles importadas para cada servicePrincipal do AWS em que o provisionamento foi configurado. Essas cadeias de caracteres de função podem ser adicionadas posteriormente à servicePrincipal do AWS na qual o logon único está configurado.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
