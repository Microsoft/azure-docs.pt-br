---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AWS Single-Account Access | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AWS Single-Account Access.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: jeedes
ms.openlocfilehash: eb469c757e2898a9925dd7d3358cfe95734cb2e9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537722"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-account-access"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AWS Single-Account Access

Neste tutorial, você aprenderá a integrar o AWS Single-Account Access ao Azure AD (Azure Active Directory). Ao integrar o AWS Single-Account Access ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao AWS Single-Account Access.
* Permitir que os usuários sejam conectados automaticamente ao AWS Single-Account Access com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="understanding-the-different-aws-applications-in-the-azure-ad-application-gallery"></a>Compreensão dos diferentes aplicativos do AWS na galeria de aplicativos do Azure AD
Use as informações abaixo para tomar uma decisão entre usar os aplicativos de acesso Logon Único do AWS e a Conta Única do AWS na galeria de aplicativos do Azure AD.

**Logon Único do AWS**

O [Logon Único do AWS](./aws-single-sign-on-tutorial.md) foi adicionado à galeria de aplicativos do Azure AD em fevereiro de 2021. Ele facilita o gerenciamento central do acesso a várias contas e aplicativos do AWS, com a entrada via Microsoft Azure AD. Use o SSO do AWS para federar o Microsoft Azure AD uma vez e para gerenciar permissões em todas as suas contas do AWS em um só lugar. O SSO do AWS provisiona permissões automaticamente e as mantém atualizadas à medida que você atualiza as políticas e atribuições de acesso. Os usuários finais podem se autenticar com suas credenciais do Azure AD para acessar o Console do AWS, a Interface de Linha de Comando e os aplicativos integrados do SSO do AWS.

**Acesso de Conta Única do AWS**

O [Acesso de Conta Única do AWS]() tem sido usado pelos clientes nos últimos anos e permite que você federe o Azure AD a uma única conta do AWS e use o Azure AD para gerenciar o acesso às funções de IAM do AWS. Os administradores de IAM do AWS definem funções e políticas em cada conta do AWS. Para cada conta do AWS, os administradores do Azure AD federam para o IAM do AWS, atribuem usuários ou grupos à conta e configuram o Azure AD para enviar asserções que autorizam o acesso à função.  

| Recurso | Logon Único do AWS | Acesso de Conta Única do AWS |
|:--- |:---:|:---:|
|Acesso condicional| Dá suporte a uma única política de acesso condicional a todas as contas do AWS. | Dá suporte a uma única política de acesso condicional a todas as contas ou políticas personalizadas por conta|
| Acesso à CLI | Com suporte | Com suporte|
| Privileged Identity Management | Ainda não compatível | Ainda não compatível |
| Centralizar o gerenciamento de conta | Centralizar o gerenciamento de conta no AWS. | Centraliza o gerenciamento de conta no Azure AD (provavelmente exigirá um aplicativo empresarial do Azure AD por conta). |
| Certificado SAML| Certificado único| Separar certificados por aplicativo/conta | 

## <a name="aws-single-account-access-architecture"></a>Arquitetura de Acesso de Conta Única do AWS
![Diagrama do relacionamento do Azure AD e do AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Você pode configurar vários identificadores para várias instâncias. Por exemplo:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com esses valores, o Azure AD remove o valor de **#** e envia o valor correto `https://signin.aws.amazon.com/saml` como a URL de público no token SAML.

É recomendável essa abordagem pelos seguintes motivos:

- Cada aplicativo fornece a você um certificado X509 exclusivo. Cada instância de uma instância de aplicativo da AWS pode ter uma data de expiração do certificado diferente, que pode ser gerenciada individualmente por conta da AWS. A rolagem geral do certificado é mais fácil nesse caso.

- Você pode ativar o aprovisionamento de usuário com um aplicativo da AWS no Azure AD e, em seguida, nosso serviço busca todas as funções dessa conta da AWS. Você não precisa adicionar ou atualizar manualmente as funções da AWS no aplicativo.

- Você pode atribuir o proprietário do aplicativo individualmente para o aplicativo. Essa pessoa pode gerenciar o aplicativo diretamente no Azure AD.

> [!Note]
> Use apenas o aplicativo da galeria.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do AWS.

> [!Note]
> As funções não devem ser editadas manualmente no Azure AD ao fazer importações de função.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O AWS Single-Account Access é compatível com SSO iniciado por **SP e IDP**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-aws-single-account-access-from-the-gallery"></a>Adicionando o AWS Single-Account Access por meio da galeria

Para configurar a integração do AWS Single-Account Access ao Azure AD, você precisará adicionar o AWS Single-Account Access da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa, de estudante ou uma conta pessoal da Microsoft.
1. Na portal do Azure, procure e selecione **Azure Active Directory**.
1. No menu de visão geral do Azure Active Directory, escolha **Aplicativos empresariais** > **Todos os aplicativos**.
1. Para adicionar um aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AWS Single-Account Access** na caixa de pesquisa.
1. Selecione **AWS Single-Account Access** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-aws-single-account-access"></a>Configurar e testar o SSO do Azure AD para o AWS Single-Account Access

Configure e teste o SSO do Azure AD com o AWS Single-Account Access usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no AWS Single-Account Access.

Para configurar e testar o SSO do Azure AD com o AWS Single-Account Access, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do AWS Single-Account Access](#configure-aws-single-account-access-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do AWS Single-Account Access](#create-aws-single-account-access-test-user)** : para ter um equivalente de B.Fernandes no AWS Single-Account Access que esteja vinculado à representação de usuário do Azure AD.
    1. **[Como configurar o provisionamento de função no AWS Single-Account Access](#how-to-configure-role-provisioning-in-aws-single-account-access)**
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **AWS Single-Account Access**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML**, atualize **Identificador (ID da Entidade)** e **URL de Resposta** com o mesmo valor padrão: `https://signin.aws.amazon.com/saml`. Você precisa selecionar **Salvar** para salvar as alterações na configuração.

1. Quando você estiver configurando mais de uma instância, forneça um valor de identificador. Da segunda instância em diante, use o seguinte formato, incluindo um sinal **#** para especificar um valor SPN exclusivo.

    `https://signin.aws.amazon.com/saml#2`

1. O aplicativo do AWS espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo do AWS espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome  | Atributo de origem  | Namespace |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Função | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration | "fornecer um valor entre 900 segundos (15 minutos) para segundos 43200 (12 horas)" |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > A AWS espera funções para usuários atribuídos ao aplicativo. Configure essas funções no Azure AD para que os usuários possam ser atribuídos às funções apropriadas. Para entender como configurar funções no Azure AD, veja [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. Na página **Configurar o logon único com o SAML**, na caixa de diálogo **Certificado de Autenticação do SAML** (Etapa 3), selecione **Adicionar um certificado**.

    ![Criar um certificado SAML](common/add-saml-certificate.png)

1. Gere um novo certificado de autenticação SAML e, em seguida, selecione **Novo Certificado**. Insira um endereço de email para notificações de certificado.
   
    ![Novo certificado SAML](common/new-saml-certificate.png) 

1. Na seção **Certificado de Autenticação SAML**, localize o **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](./media/amazon-web-service-tutorial/certificate.png)

1. Na seção **Configurar o AWS Single-Account Access**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. Na portal do Azure, procure e selecione **Azure Active Directory**.
1. No menu de visão geral do Azure Active Directory, escolha **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilita B.Fernandes a usar o logon único do Azure concedendo a ela acesso ao AWS Single-Account Access.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **AWS Single-Account Access**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-aws-single-account-access-sso"></a>Configurar o SSO do AWS Single-Account Access

1. Em uma janela diferente do navegador, entre no site da empresa do AWS como administrador.

2. Selecione **Página inicial do AWS**.

    ![Captura de tela do site da empresa AWS, com o ícone Página inicial do AWS realçado][11]

3. Selecione **Gerenciamento de acesso e identidade**.

    ![Captura de tela da página de serviços do AWS, com IAM realçado][12]

4. Selecione **Provedores de Identidade** > **Criar Provedor**.

    ![Captura de tela da página IAM, com Provedores de Identidade e Criar Provedor realçados][13]

5. Na página **Configurar Provedor**, execute as seguintes etapas:

    ![Captura de tela de Configurar Provedor][14]

    a. Em **Tipo de provedor**, selecione **SAML**.

    b. Em **Nome do Provedor**, digite um nome para o provedor (por exemplo: *WAAD*).

    c. Para carregar seu **arquivo de metadados** baixado do Portal do Azure, selecione **Escolher Arquivo**.

    d. Selecione **Próxima etapa**.

6. Na página **Verificar Informações do Provedor**, selecione **Criar**.

    ![Captura de tela de Verificar Informações do Provedor, com Criar realçado][15]

7. Selecione **Funções** > **Criar função**.

    ![Captura de tela da página Funções][16]

8. Na página **Criar função**, realize as seguintes etapas:  

    ![Captura de tela da página Criar função][19]

    a. Em **Selecionar tipo de entidade confiável**, selecione **Federação do SAML 2.0**.

    b. Em **Escolher um provedor do SAML 2.0**, selecione o **Provedor do SAML** criado anteriormente (por exemplo: *WAAD*).

    c. Selecione **Permitir acesso do Console de Gerenciamento do AWS e programação**.
  
    d. Selecione **Avançar: Permissões.**

9. Na caixa de diálogo **Anexar políticas de permissão**, anexe a política adequada conforme sua organização. Em seguida, selecione **Avançar: Análise**.  

    ![Captura de tela da caixa de diálogo Anexar política de permissões][33]

10. Na caixa de diálogo **Examinar**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Examinar][34]

    a. Em **Nome da função**, insira o nome da função.

    b. Em **Descrição da função**, insira a descrição.

    c. Selecione **Criar função**.

    d. Crie quantas funções forem necessárias e mapeie-as para o provedor de identidade.

11. Use as credenciais de conta de serviço do AWS para buscar as funções da conta AWS no provisionamento de usuário do Azure AD. Para isso, abra a página inicial do console AWS.

12. Selecione **Serviços**. Em **Segurança, Identidade e Conformidade**, selecione **IAM**.

    ![Captura de tela da página inicial do console do AWS, com Serviços e IAM realçados](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Na seção IAM, selecione **Políticas**.

    ![Captura de tela da seção IAM, com Políticas realçado](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Crie uma política selecionando **Criar política** para buscar as funções da conta AWS no provisionamento de usuário do Azure AD.

    ![Captura de tela da página Criar função, com Criar política realçado](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Crie sua própria política para buscar todas as funções de contas AWS.

    ![Captura de tela da página Criar política, com JSON realçado](./media/amazon-web-service-tutorial/policy1.png)

    a. Na **Criar a política**, selecione a guia **JSON**.

    b. No documento de política, adicione o seguinte JSON:

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

    c. Selecione **Examinar política** para validar a política.

    ![Captura de tela da página Criar política](./media/amazon-web-service-tutorial/policy5.png)

16. Defina a nova política.

    ![Captura de tela da página Criar política, com os campos Nome e Descrição realçados](./media/amazon-web-service-tutorial/policy2.png)

    a. Para **Nome**, insira **AzureAD_SSOUserRole_Policy**.

    b. Para **Descrição**, insira **Esta política permitirá buscar as funções de contas do AWS**.

    c. Selecione **Criar política**.

17. Crie uma conta de usuário no serviço de IAM do AWS.

    a. No console IAM do AWS, selecione **Usuários**.

    ![Captura de tela do console IAM do AWS, com Usuários realçado](./media/amazon-web-service-tutorial/policy3.png)

    b. Para criar um usuário, selecione **Adicionar usuário**.

    ![Captura de tela do botão Adicionar usuário](./media/amazon-web-service-tutorial/policy4.png)

    c. Na seção **Adicionar usuário**:

    ![Captura de tela da página Adicionar usuário, com Nome de usuário e Tipo de acesso realçados](./media/amazon-web-service-tutorial/adduser1.png)

    * Digite o nome de usuário como **AzureADRoleManager**.

    * Para o tipo de acesso, selecione **Acesso programático**. Dessa forma, o usuário pode invocar as APIs e buscar as funções da conta AWS.

    * Selecione **Próximas Permissões**.

18. Crie uma política para este usuário.

    ![Captura de tela que mostra a página Adicionar usuário, na qual você pode criar uma política para o usuário.](./media/amazon-web-service-tutorial/adduser2.png)

    a. Selecione **Anexar políticas existentes diretamente**.

    b. Pesquise a política recém-criada na seção filtro **AzureAD_SSOUserRole_Policy**.

    c. Selecione a política e, em seguida, **Próximo: Análise**.

19. Examine a política para o usuário anexado.

    ![Captura de tela da página Adicionar usuário, com Criar usuário realçado](./media/amazon-web-service-tutorial/adduser3.png)

    a. Examine o nome de usuário, tipo de acesso e política mapeados para o usuário.

    b. Selecione **Criar usuário**.

20. Baixe as credenciais do usuário de um usuário.

    ![Captura de tela que mostra a página Adicionar usuário com um botão Baixar .csv para obter as credenciais do usuário.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copie a **ID de chave de acesso** do usuário e a **Chave de acesso secreta**.

    b. Insira essas credenciais na seção de provisionamento de usuário do Azure AD para buscar as funções do console AWS.

    c. Selecione **Fechar**.

### <a name="how-to-configure-role-provisioning-in-aws-single-account-access"></a>Como configurar o provisionamento de função no AWS Single-Account Access

1. No portal de gerenciamento do Azure AD, no aplicativo AWS, acesse **Provisionamento**.

    ![Captura de tela do aplicativo AWS, com Provisionamento realçado](./media/amazon-web-service-tutorial/provisioning.png)

2. Insira a chave de acesso e o segredo nos campos **clientsecret** e **Token do Segredo**, respectivamente.

    ![Captura de tela da caixa de diálogo Credenciais de Administrador](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Insira a chave de acesso do usuário do AWS no campo **Segredo do cliente**.

    b. Insira o segredo do usuário do AWS no campo **Segredo do Token**.

    c. Selecione **Testar Conexão**.

    d. Salve a configuração selecionando **Salvar**.

3. Na seção **Configurações**, para **Status de Provisionamento**, selecione **Ativado**. Em seguida, selecione **Salvar**.

    ![Captura de tela da seção Configurações, com Ativado realçado](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> O serviço de provisionamento importa funções apenas do AWS para o Azure AD. Esse serviço não provisiona usuários e grupos do Azure AD para o AWS.

> [!NOTE]
> Depois de salvar as credenciais de provisionamento, você deve aguardar a execução do ciclo de sincronização inicial. A conclusão da sincronização leva cerca de 40 minutos. Você pode ver o status na parte inferior da página **Provisionamento**, em **Status Atual**.

### <a name="create-aws-single-account-access-test-user"></a>Criar usuário de teste do AWS Single-Account Access

O objetivo desta seção é criar uma usuária chamada B.Fernandes no AWS Single-Account Access. O AWS Single-Account Access não precisa que um usuário seja criado em seu sistema para o SSO, portanto você não precisa realizar nenhuma ação aqui.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do AWS Single-Account Access, na qual você poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do AWS Single-Account Access diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao AWS Single-Account Access, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do AWS Single-Account Access em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao AWS Single-Account Access, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="known-issues"></a>Problemas conhecidos

* A integração de provisionamento do AWS Single-Account Access pode ser usada somente para se conectar a pontos de extremidade de nuvem pública do AWS. A integração de provisionamento do AWS Single-Account Access não pode ser usada para acessar ambientes do AWS Governamental.
 
* Na seção **Provisionamento**, a subseção **Mapeamentos** mostra uma mensagem "Carregando..." e nunca exibe os mapeamentos de atributo. O único fluxo de trabalho de provisionamento compatível hoje é a importação de funções do AWS no Azure AD para seleção durante a atribuição de um usuário ou grupo. Os mapeamentos de atributo para isso são predeterminados e não são configuráveis.

* A seção **Provisionamento** só dá suporte a um conjunto de credenciais para um locatário do AWS por vez. Todas as funções importadas são escritas na propriedade `appRoles` do [`servicePrincipal` objeto](/graph/api/resources/serviceprincipal) do Azure AD para o locatário do AWS.

  Vários locatários AWS (representados por `servicePrincipals`) podem ser adicionados ao Azure AD da galeria para provisionamento. Há um problema conhecido, no entanto, sem ser possível gravar automaticamente todas as funções importadas de vários `servicePrincipals` do AWS usados para provisionamento em um único `servicePrincipal` usado para SSO.

  Como alternativa, você pode usar a [API do Microsoft Graph](/graph/api/resources/serviceprincipal) para extrair todo o `appRoles` importado em cada `servicePrincipal` do AWS em que o provisionamento é configurado. Posteriormente, você pode adicionar essas cadeias de caracteres de função ao `servicePrincipal` do AWS em que o SSO está configurado.

* As funções deverão atender aos seguintes requisitos para que sejam elegíveis a serem importadas do AWS para o Azure AD:

  * As funções devem ter exatamente um provedor SAML definido no AWS
  * O comprimento combinado do ARN (Nome do Recurso da Amazon) para a função e o ARN para o provedor SAML associado devem ter menos de 240 caracteres.

## <a name="change-log"></a>Log de alterações

* 12/01/2020 – aumentado o limite de extensão da função de 119 caracteres para 239 caracteres. 

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o AWS Single-Account Access, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


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