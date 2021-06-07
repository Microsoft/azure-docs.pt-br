---
title: 'Tutorial: integração do Azure Active Directory com o Amazon Web Services com o objetivo de conectar várias contas | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure AD e o Amazon Web Services (tutorial herdado).
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
ms.openlocfilehash: 0ddcb239ba106bcdc2f0a29d68eb395876fadc06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384105"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Tutorial: integração do Azure Active Directory com o Amazon Web Services

Neste tutorial, você aprenderá de que modo integrar o Azure AD (Azure Active Directory) com a AWS (Amazon Web Services) (tutorial herdado).

Essa integração fornecerá os seguintes benefícios:

- É possível controlar quem terá acesso à AWS no Azure AD.
- É possível permitir que seus usuários entrem de modo automático na AWS usando o SSO (logon único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

![Diagrama de integração do Azure AD com a AWS.](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> *Não* recomendamos conectar um aplicativo da AWS a todas as contas da AWS. Como alternativa, recomendamos usar a [integração de SSO do Azure AD com a AWS](./amazon-web-service-tutorial.md) para configurar várias instâncias de sua conta da AWS para acessar aplicativos da AWS de várias instâncias no Azure AD. 

*Não* recomendamos conectar um aplicativo da AWS a todas as contas da AWS pelos seguintes motivos:

* Somente use essa abordagem caso tenha um pequeno número de contas e funções da AWS porque esse modelo não é escalonável, tendo em vista que o número de contas e funções aumenta na AWS. Essa abordagem não usa uma funcionalidade de importação de função da AWS com o provisionamento de usuário do Azure AD. Portanto, será necessário adicionar, atualizar ou excluir as funções de modo manual. 

* Você tem que usar a abordagem do Explorador do Microsoft Graph para aplicar patches em todas as funções do aplicativo. Não recomendamos o uso da abordagem de arquivo de manifesto.

* Os clientes relataram que após adicionar aproximadamente 1.200 funções de aplicativos em um aplicativo da AWS, outras operações do aplicativo começaram a gerar erros relacionados ao tamanho. Há um limite rígido de tamanho para o objeto do aplicativo.

* É necessário atualizar as funções de modo manual conforme elas são adicionadas às contas. Infelizmente, essa é uma abordagem de *substituição*, não uma abordagem de *acréscimo*. Além disso, caso o número de contas seja crescente, isso se tornará uma relação *n* &times; *n* com funções e contas.

* Todas as contas da AWS usam o mesmo arquivo XML de metadados federados. No momento da sobreposição do certificado, atualizá-lo ao mesmo tempo em todas as contas da AWS poderá demandar um enorme trabalho.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a AWS, será necessário obter os seguintes itens:

* Uma assinatura do Azure AD. Caso não tenha uma assinatura do Azure AD, será possível obter uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura da AWS habilitada para SSO.

> [!NOTE]
> Não recomendamos testar as etapas deste tutorial em um ambiente de produção, a não ser que seja necessário.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

A AWS é compatível com um SSO iniciado por SP e IDP.

## <a name="add-aws-from-the-gallery"></a>Adicionar o AWS da galeria

Para configurar a integração da AWS no Azure AD, adicione a AWS da galeria à sua lista de aplicativos SaaS (software como serviço) gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço do Azure AD com o qual deseja trabalhar.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Amazon Web Services** na caixa de pesquisa.
1. Na lista de resultados, selecione **Amazon Web Services** e adicione o aplicativo. Em alguns segundos, o aplicativo será adicionado ao seu locatário.

1. Acesso o painel **Propriedades** e copie o valor exibido na caixa **ID de Objeto**.

    ![Uma captura de tela da caixa ID de Objeto no painel Propriedades.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Nesta seção, configure e teste o logon único do Azure AD com a AWS tendo como base um usuário de teste chamado "Britta Simon".

O Azure AD precisa saber qual usuário da AWS será correspondente ao usuário do Azure AD para que o logon único funcione. Isso significa que é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o mesmo usuário na AWS.

Na AWS, atribua o valor do **nome de usuário** do Azure AD como o valor do **Nome de Usuário** da AWS para estabelecer uma relação de vínculo.

Para configurar e testar o logon único do Azure AD com a AWS, execute o seguinte:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
1. **[Configure o SSO da AWS](#configure-aws-sso)** para definir as configurações de SSO no aplicativo.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, habilite o SSO do Azure AD no portal do Azure e configure o SSO em seu aplicativo da AWS executando o seguinte:

1. No portal do Azure, acesse a página de integração do aplicativo da **AWS (Amazon Web Services)** no painel esquerdo e selecione **Logon único**.

    ![Uma captura de tela do comando "Logon único".](common/select-sso.png)

1. No painel **Escolher um método de logon único**, escolha o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Uma captura de tela do painel "Selecionar um método de logon único".](common/select-saml-option.png)

1. No painel **Configurar Logon Único usando SAML**, clique no botão **Editar** (ícone de lápis).

    ![Uma captura de tela do botão Editar no painel "Configurar Logon Único usando SAML".](common/edit-urls.png)

1. O painel **Configuração Básica do SAML** será exibido. Ignore essa seção porque o aplicativo é pré-integrado ao Azure. Selecione **Salvar**.

   O aplicativo da AWS espera que as instruções SAML estejam em um formato específico. É possível gerenciar os valores desses atributos na seção **Atributos e Declarações do Usuário** da página **Integração de aplicativos**. 
   
1. No painel **Configurar Logon Único usando SAML**, clique no botão **Editar**.

    ![Uma captura de tela do botão Editar no painel "Atributos de Usuário".](common/edit-attribute.png)

1. Na seção **Declarações do Usuário** do painel **Atributos de Usuário**, configure o atributo do token SAML usando os valores da seguinte tabela:

    | Nome  | Atributo de origem  | Namespace |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Função | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "fornecer um valor de 900 segundos (15 minutos) a 43.200 segundos (12 horas)" |  `https://aws.amazon.com/SAML/Attributes` |
  
   a. Clique em **Adicionar nova declaração** e no painel **Gerenciar declarações do usuário** execute o seguinte:

   ![Uma captura de tela dos botões "Adicionar nova declaração" e "Salvar" no painel "Declarações do usuário".](common/new-save-attribute.png)

   ![Uma captura de tela do painel "Gerenciar declarações do usuário".](common/new-attribute-details.png)

   b. Na caixa **Nome**, insira o nome do atributo.  

   c. Na caixa **Namespace**, insira o valor do namespace.

   d. Em **Origem**, selecione **Atributo**.

   e. Na lista suspensa **Atributo de origem**, selecione o atributo.

   f. Clique em **OK**, depois em **Salvar**.

   >[!NOTE]
   >Para obter mais informações sobre funções do Azure AD, confira como [Adicionar funções de aplicativos ao aplicativo e recebê-las no token](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na página **Configurar Logon Único usando SAML**, da seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o arquivo XML de metadados federados, depois o salve no computador.

   ![Uma captura de tela do link de download "Arquivo XML de Metadados Federados" no painel "Certificado de Autenticação SAML.](common/metadataxml.png)

### <a name="configure-aws-sso"></a>Configurar o SSO da AWS

1. Em uma nova janela do navegador, entre no site empresarial da AWS como administrador.

1. Clique no ícone **Página Inicial da AWS**.

   ![Uma captura de tela do ícone "Página Inicial da AWS".][11]

1. No painel **Serviços da AWS**, em **Segurança, Identidade e Conformidade**, selecione **IAM (Gerenciamento de Identidades e Acesso)** .

    ![Uma captura de tela do link de "Gerenciamento de Identidades e Acesso" no painel "Serviços da AWS".][12]

1. No painel esquerdo, selecione **Provedores de Identidade**, depois clique em **Criar Provedor**.

    ![Uma captura de tela do botão "Criar Provedor".][13]

1. No painel **Configurar Provedor**, execute o seguinte:

    ![Uma captura de tela do painel "Configurar Provedor".][14]

    a. Na lista suspensa **Tipo de Provedor**, selecione **SAML**.

    b. Na caixa **Nome do Provedor**, insira o nome do provedor (por exemplo, *WAAD*).

    c. Ao lado da caixa **Documento de Metadados**, clique em **Escolher Arquivo** para carregar o arquivo XML de metadados federados baixado no portal do Azure.

    d. Selecione **Próxima etapa**.

1. No painel **Verificar Informações do Provedor**, clique em **Criar**.

    ![Uma captura de tela do painel "Verificar Informações do Provedor".][15]

1. No painel esquerdo, selecione **Funções**, depois clique em **Criar função**.

    ![Uma captura de tela do botão "Criar função" no painel Funções.][16]

    > [!NOTE]
    > O comprimento combinado do ARN (Nome de Recurso da Amazon) da função e do ARN do provedor SAML para uma função que está sendo importada deverá ter 240 caracteres ou menos.

1. Na página **Criar função**, execute o seguinte:  

    ![Uma captura de tela do botão de entidade confiável "Federação do SAML 2.0" na página "Criar função".][19]

    a. Em **Selecionar tipo de entidade confiável**, selecione **Federação do SAML 2.0**.

    b. Em **Escolher um provedor do SAML 2.0**, selecione o provedor do SAML criado anteriormente (por exemplo, *WAAD*)

    c. Selecione **Permitir acesso do Console de Gerenciamento do AWS e programação**.

    d. Selecione **Avançar: Permissões.**

1. Na caixa de pesquisa, digite **Acesso de Administrador**, clique na caixa de seleção **Acesso de Administrador** e selecione **Próximo: marcações**.

    ![Uma captura de tela da lista "Nome da política" com a política AdministratorAccess selecionada.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. No painel **Adicionar marcações (opcional)** , execute o seguinte:

    ![Uma captura de tela do painel "Adicionar marcações (opcional)".](./media/aws-multi-accounts-tutorial/config2.png)

    a. Na caixa **Chave**, insira o nome da chave (por exemplo, *Azureadtest*).

    b. Na caixa **Valor (opcional)** , insira o valor da chave usando este formato: `<accountname-aws-admin>`. O nome da conta deverá ter somente letras minúsculas.

    c. Selecione **Avançar: Análise**.

1. No painel **Examinar**, execute o seguinte:

    ![Uma captura de tela do painel Examinar com as caixas "Nome da função" e "Descrição da função" destacadas.][34]

    a. Na caixa **Nome da função**, insira o valor neste formato: `<accountname-aws-admin>`.

    b. Na caixa **Descrição da função**, insira o valor usado para o nome da função.

    c. Selecione **Criar função**.

    d. Crie o número de funções necessárias e execute um mapeamento delas para o provedor de identidade.

    > [!NOTE]
    > Do mesmo modo, será possível criar outras funções, como *accountname-finance-admin*, *accountname-read-only-user*, *accountname-devops-user* ou *accountname-tpm-user*, cada uma delas com diferentes políticas anexadas. Será possível alterar essas políticas de função posteriormente, conforme os requisitos de cada conta da AWS. Recomendamos manter as mesmas políticas para cada função entre contas da AWS.

1. Lembre-se de anotar a ID da conta para obter a conta da AWS do painel de propriedades do Amazon EC2 (Amazon Elastic Compute Cloud) ou do painel IAM, conforme mostrado na seguinte captura de tela:

    ![Uma captura de tela mostrando onde a ID da conta será exibida no painel "Gerenciamento de Identidades e Acesso".](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Entre no portal do Azure e acesse a opção **Grupos**.

1. Crie grupos com o mesmo nome das funções do IAM criadas anteriormente. Além disso, anote o valor na caixa **ID de Objeto** de cada um desses novos grupos.

    ![Uma captura de tela dos detalhes da conta de um novo grupo.](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Saia da conta atual da AWS, depois entre na conta em que você deseja configurar o SSO com o Azure AD.

1. Após criar todas as funções nas contas, elas serão exibidas na lista de **Funções** dessas contas.

    ![Uma captura de tela da lista de funções mostrando cada nome, descrição e entidades confiáveis da função.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

Em seguida, será necessário capturar os ARNs da função e as entidades confiáveis das funções de todas as contas. Será necessário mapeá-los de modo manual usando um aplicativo do Azure AD. Para fazer isso:

1. Selecione cada função para copiar os respectivos valores do ARN da função e da entidade confiável. Esses valores são necessários para todas as funções que deverão ser criadas no Azure AD.

    ![Uma captura de tela do painel Resumo do ARN da função e das entidades confiáveis.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Repita a etapa anterior em todas as funções das contas e armazene-as em um arquivo de texto neste formato: `<Role ARN>,<Trusted entities>`.

1. Abra o [Explorador do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) e execute o seguinte:

    a. Entre no site do Explorador do Microsoft Graph usando as credenciais de Administrador ou Coadministrador Global de seu locatário.

    b. Você precisa ter permissões suficientes para criar as funções. Clique em **modificar permissões**.

      ![Uma captura de tela do link "modificar permissões" no painel de Autenticação do Explorador do Microsoft Graph.](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Na lista de permissões, caso ainda não tenha as permissões mostradas na captura de tela abaixo, selecione cada uma delas e clique em **Modificar Permissões**. 

      ![Uma captura de tela da lista de permissões do Explorador do Microsoft Graph com as permissões apropriadas destacadas.](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Entre novamente no Explorador do Microsoft Graph e aceite as condições de uso do site. 

    e. Na parte superior do painel, selecione **GET** para o método e **beta** para a versão. Em seguida, na caixa de consulta, insira uma das seguintes opções: 
    
    * Use `https://graph.microsoft.com/beta/servicePrincipals` para buscar todas as entidades de serviço de seu locatário. 
    * Caso esteja usando vários diretórios, use `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`, pois ele contém seu domínio principal.

    ![Uma captura de tela do painel de consulta "Corpo da Solicitação" do Explorador do Microsoft Graph.](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Obtenha a entidade de serviço que precisa ser modificada da lista de entidades de serviço. 
    
    Também é possível pesquisar o aplicativo para todas as entidades de serviço listadas pressionando Ctrl + F. Para obter uma entidade de serviço específica, inclua na consulta a ID de objeto da entidade de serviço copiada anteriormente do painel Propriedades do Azure AD, conforme mostrado aqui:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

      ![Uma captura de tela mostrando uma consulta da entidade de serviço que inclui a ID de objeto.](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extraia a propriedade appRoles do objeto da entidade de serviço.

    ![Uma captura de tela do código usado para extrair a propriedade appRoles do objeto da entidade de serviço.](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Agora, é necessário gerar novas funções para aplicativo. 

    i. O código JSON abaixo é um exemplo de um objeto appRoles. Crie um objeto semelhante para adicionar as funções que você deseja para o aplicativo.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > É possível adicionar funções somente após adicionar *msiam_access* para uma operação de patch. Também é possível adicionar quantas funções você desejar, dependendo das necessidades de sua organização. O Azure AD enviará o *valor* dessas funções como um valor da declaração na resposta SAML.

    j. No Explorador do Microsoft Graph, altere o método de **GET** para **PATCH**. Aplique patch no objeto da entidade de serviço usando funções de sua escolha ao atualizar a propriedade appRoles, conforme mostrado no exemplo anterior. Clique em **Executar Consulta** para executar a operação de patch. Uma mensagem de êxito confirmará a criação da função para o aplicativo da AWS.

      ![Uma captura de tela do painel do Explorador do Microsoft Graph com o método alterado para PATCH.](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Após aplicar patch na entidade de serviço usando outras funções, será possível atribuir usuários e grupos às respectivas funções. Execute essa ação no portal do Azure acessando o aplicativo da AWS e selecionando a guia **Usuários e Grupos** na parte superior da página.

1. Recomendamos criar grupos para cada função da AWS. Desse modo, será possível atribuir essa função específica ao grupo. Esse mapeamento individual significa que uma função será atribuída a cada grupo. Em seguida, você pode adicionar os membros que pertencem ao grupo.

1. Depois de criar os grupos, selecione o grupo e atribua-o ao aplicativo.

    ![Uma captura de tela do painel "Usuários e grupos".](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Os grupos aninhados não terão suporte durante a atribuição de grupos.

1. Selecione a função, depois clique em **Atribuir** para atribuir a função ao grupo.

    ![Uma captura de tela do painel "Adicionar Atribuição".](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Depois de atribuir as funções, será possível exibi-las atualizando a sessão do portal do Azure.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, teste a configuração de logon único do Azure AD usando a opção Meus Aplicativos da Microsoft.

Ao selecionar o bloco **AWS** em Meus Aplicativos, a página de aplicativo da AWS será exibida com uma opção para selecionar a função.

![Uma captura de tela da página da AWS para testar o SSO.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Você também pode verificar a resposta SAML para ver as funções sendo passadas como declarações.

![Uma captura de tela da resposta SAML.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Para obter mais informações sobre a opção Meus Aplicativos, confira como [Entrar nos aplicativos e iniciá-los do portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar a AWS, será possível impor um controle de sessão, que fornecerá proteção contra exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. Para obter mais informações, confira [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
