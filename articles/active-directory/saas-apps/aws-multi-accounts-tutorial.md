---
title: 'Tutorial: Integração do Azure Active Directory com o Amazon Web Services (AWS) para conectar várias contas | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure AD e a AWS (Amazon Web Services) (Tutorial Herdado).
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
ms.openlocfilehash: 440fe52689a345eec36c3ac613d6bc2cc2dccc13
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735446"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Tutorial: Integração do Azure Active Directory com a AWS (Amazon Web Services) (Tutorial Herdado)

Neste tutorial, você aprenderá a integrar o Azure AD (Azure Active Directory) com a AWS (Amazon Web Services) (Tutorial Herdado).

A integração do AWS (Amazon Web Services) ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso ao AWS (Amazon Web Services).
- Você pode habilitar seus usuários a fazerem logon automaticamente no AWS (Amazon Web Services) (logon único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

![AWS (Amazon Web Services) na lista de resultados](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Observe que conectar um aplicativo da AWS a todas as suas contas da AWS não é nossa abordagem recomendada. Em vez disso, recomendamos que você use [essa](./amazon-web-service-tutorial.md) abordagem para configurar várias instâncias da conta da AWS para Várias instâncias de aplicativos da AWS no Azure AD. Você só deverá usar essa abordagem se tiver poucas Contas e Funções da AWS. Esse modelo não é escalonável, pois as contas e funções da AWS nessas contas crescem. Essa abordagem não usa a funcionalidade de importação de Função da AWS com o Provisionamento de Usuário do Azure AD, portanto, você precisa adicionar/atualizar/excluir manualmente as funções. Para obter outras limitações sobre essa abordagem, confira os detalhes abaixo.

**Tenha em atenção que não recomendamos a utilização desta abordagem pelas seguintes razões:**

* Você tem que usar a abordagem do Explorador do Microsoft Graph para aplicar patches em todas as funções do aplicativo. Não recomendamos o uso da abordagem de arquivo de manifesto.

* Vimos clientes relatando que, depois de adicionar ~ 1200 funções de aplicativos para um único aplicativo da AWS, qualquer operação no aplicativo começou a gerar erros relacionados ao tamanho. Há um limite rígido de tamanho no objeto de aplicativo.

* Você precisa atualizar manualmente a função à medida que as funções forem adicionadas em qualquer uma das contas, que é uma abordagem Substituir e não Anexar infelizmente. Além disso, se suas contas estiverem crescendo, isso se tornará n x n relacionamento com contas e funções.

* Todas as contas da AWS usarão o mesmo arquivo XML de Metadados da Federação e, no momento da substituição do certificado, você terá que realizar esse exercício gigantesco para atualizar o certificado em todas as contas da AWS ao mesmo tempo

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o AWS (Amazon Web Services), você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único da AWS (Amazon Web Services)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A AWS (Amazon Web Services) dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar o AWS (Amazon Web Services) da galeria

Para configurar a integração do AWS (Amazon Web Services) com o Azure AD, você precisa adicionar o AWS (Amazon Web Services), por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Amazon Web Services (AWS)** na caixa de pesquisa.
1. Selecione **AWS (Amazon Web Services)** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

1. Depois que o aplicativo for adicionado, vá para a página **Propriedades** e copie a **ID de Objeto**.

    ![ID de objeto](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o AWS (Amazon Web Services), com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do AWS (Amazon Web Services) é equivalente a um usuário do Azure AD. Em outras palavras, uma relação de link entre um usuário do Azure AD e o usuário relacionado no serviço AWS (Amazon Web Services) deve ser estabelecida.

No Amazon Web Services (AWS), atribua o valor do **nome de usuário** no Microsoft Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com a AWS (Amazon Web Services), execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO da AWS (Amazon Web Services)](#configure-amazon-web-services-aws-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo AWS (Amazon Web Services).

**Para configurar o logon único do Azure AD com o AWS (Amazon Web Services), execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **AWS (Amazon Web Services)** , selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **lápis** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure, basta clicar em **Salvar**.

5. Aplicativo Amazon Web Services (AWS) espera as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **Atributos e reivindicações do usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **User Attributes & Claims**.

    ![Captura de tela que mostra Atributos do Usuário com o controle de edição em destaque.](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    | Nome  | Atributo de Origem  | Namespace |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Função | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "fornecer um valor entre 900 segundos (15 minutos) para segundos 43200 (12 horas)" |  `https://aws.amazon.com/SAML/Attributes` |

    1. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

        ![Captura de tela que mostra Declarações do usuário com Adicionar nova declaração e Salvar em destaque.](common/new-save-attribute.png)

        ![Captura de tela que mostra Gerenciar declarações do usuário, em que você pode inserir os valores descritos nesta etapa.](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na caixa de texto **Namespace**, digite o valor de namespace mostrado para essa linha.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

    >[!NOTE]
    >Para obter mais informações sobre funções no Azure AD, confira [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

7. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do **XML de metadados de federação** e salve-o no seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-sso"></a>Configurar o SSO da AWS (Amazon Web Services)

1. Em uma janela de navegador diferente, entre no site de sua empresa do AWS (Amazon Web Services) como administrador.

1. Clique na **Página inicial do AWS**.

    ![Configurar página inicial de logon único][11]

1. Clique em **Gerenciamento de identidades e acesso**.

    ![Configurar identidade de logon único][12]

1. Clique em **Provedores de identidade**, e, em seguida, clique em **Criar provedor**.

    ![Configurar provedor de logon único][13]

1. Na página da caixa de diálogo **Configurar provedor** , execute as seguintes etapas:

    ![Configurar diálogo logon único][14]

    a. Como **Tipo de provedor**, selecione **SAML**.

    b. Na caixa de texto **Nome do provedor**, digite um nome de provedor (por exemplo: *WAAD*).

    c. Para carregar seu **arquivo de metadados** baixado do Portal do Azure, clique em **Escolher Arquivo**.

    d. Clique em **Próxima etapa**.

1. Na página de diálogo **Verificar informações do provedor**, clique em **Criar**.

    ![Configurar verificação de logon único][15]

1. Clique em **Funções** e, em seguida, clique em **Criar função**.

    ![Configurar funções de logon único][16]

    > [!NOTE]
    > O comprimento combinado total do ARN da função e do ARN do provedor SAML para uma função que está sendo importada precisa ser de até 240 caracteres.

1. Na página **Criar função**, realize as seguintes etapas:  

    ![Configurar confiança de logon único][19]

    a. Selecione **Federação do SAML 2.0** em **Selecionar tipo de entidade confiável**.

    b. Em **Escolher uma seção do provedor do SAML 2.0**, selecione o **provedor do SAML** criado anteriormente (por exemplo: *WAAD*)

    c. Selecione **Permitir acesso do Console de Gerenciamento do AWS e programação**.

    d. Clique em **Próximo: Permissões**.

1. Pesquise **Acesso de Administrador** na barra de pesquisa, marque a caixa de seleção **AdministratorAccess** e clique em **Avançar: Marcas**.

    ![A captura de tela mostra AdministratorAccess selecionado como um nome de Política.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Na seção **Adicionar marcações (opcional)** , realize as seguintes etapas:

    ![Adicionar marcas](./media/aws-multi-accounts-tutorial/config2.png)

    a. Na caixa de texto **Chave**, insira o nome da chave, por exemplo: Azureadtest.

    b. Na caixa de texto **Valor (opcional)** , insira o valor da chave usando o formato `accountname-aws-admin` a seguir. O nome da conta deve estar em letras minúsculas.

    c. Clique em **avançar: Análise**.

1. Na caixa de diálogo **Examinar** , execute as seguintes etapas:

    ![Configurar revisão de logon único][34]

    a. Na caixa de texto **Nome da função**, insira o valor no padrão `accountname-aws-admin` a seguir.

    b. Na caixa de texto **Descrição da função**, insira o mesmo valor que você usou no nome da função.

    c. Clique em **Criar função**.

    d. Crie quantas funções forem necessárias e mapeie-as para o Provedor de Identidade.

    > [!NOTE]
    > De modo semelhante, crie outras funções restantes como accountname-finance-admin, accountname-read-only-user, accountname-devops-user, accountname-tpm-user com políticas diferentes a serem anexadas. Posteriormente, essas políticas de função podem ser alteradas de acordo com os requisitos por conta da AWS, mas é sempre melhor manter as mesmas políticas para cada função nas contas da AWS.

1. Anote a ID da conta dessa conta da AWS nas propriedades do EC2 ou no painel do IAM, conforme destacado abaixo:

    ![A captura de tela mostra onde a ID da conta aparece na janela da AWS.](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Agora, entre no portal do Azure e navegue até **Grupos**.

1. Crie grupos com o mesmo nome que as Funções do IAM criadas anteriormente e anote as **IDs de Objeto** desses novos grupos.

    ![Selecione Acesso de Administrador1](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Saia da atual conta AWS e faça logon com outra conta em que você deseja configurar o logon único com o Microsoft Azure AD.

1. Depois que todas as funções são criadas nas contas, elas aparecem na lista **Funções** para essas contas.

    ![A captura de tela mostra a lista de Funções com nome de Função, Descrição e Entidades confiáveis.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. É necessário capturar todas as Funções ARN e Entidades Confiáveis para todas as funções através de todas as contas, que pode ser necessário mapear manualmente com o aplicativo Microsoft Azure AD.

1. Clique nas funções para copiar os valores da **Função ARN** e das **Entidades Confiáveis**. Esses valores são necessários para todas as funções que você precisa criar no Microsoft Azure AD.

    ![Configuração de funções2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Execute a etapa acima para todas as funções em todas as contas e armazene todas elas em formato **de Função ARN, Entidades Confiáveis** em um bloco de notas.

1. Abra o [Explorador do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

    1. Entre no site do Explorador do Microsoft Graph usando as credenciais de Administrador/Coadministrador Globais do locatário.

    1. Você precisa ter permissões suficientes para criar as funções. Clique em **modificar permissões** para obter as permissões necessárias.

        ![Caixa de diálogo do Explorador do Microsoft Graph1](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    1. Selecione as permissões da lista (se você já não tiver) a seguir e clique em "Modificar Permissões" 

        ![Caixa de diálogo do Explorador do Microsoft Graph2](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    1. Isso pedirá para fazer logon novamente e aceitar o consentimento. Depois de aceitar o consentimento, você estará logado no Explorador do Microsoft Graph novamente.

    1. Alterar a lista suspensa de versão para **beta**. Para buscar todas as Entidades de Serviço em seu locatário, use a seguinte consulta: `https://graph.microsoft.com/beta/servicePrincipals`. Se você estiver usando vários diretórios, poderá usar o seguinte padrão, que tem o seu domínio principal nele: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.

        ![Caixa de diálogo do Explorador do Microsoft Graph3](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    1. Da lista de Entidades de Serviço buscada, obtenha o que é necessário modificar. Você também pode usar o Ctrl+F para pesquisar o aplicativo de todos os ServicePrincipals listados. Você pode usar a consulta a seguir com a **ID de Objeto da Entidade de Serviço** que você copiou da página Propriedades do Azure AD e usar a consulta a seguir para acessar a respectiva Entidade de Serviço.

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

        ![Caixa de diálogo do Explorador do Microsoft Graph4](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    1. Extraia a propriedade appRoles do objeto da entidade de serviço.

        ![Caixa de diálogo do Explorador do Microsoft Graph5](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    1. Agora, é necessário gerar novas funções para aplicativo. 

    1. Abaixo, um exemplo do objeto appRoles. Crie um objeto semelhante para adicionar as funções que você deseja para o aplicativo.

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
    > Você pode apenas adicionar as novas funções depois do **msiam_access** para a operação de patch. Além disso, você pode adicionar quantas regras quiser de acordo com a necessidade da sua Organização. O Microsoft Azure AD enviará o **valor** dessas funções conforme o valor de solicitação na resposta SAML.

    1. Volte ao Explorador do Microsoft Graph e altere o método de **GET** para **PATCH**. Atualize o objeto da Entidade de Serviço para ter as funções desejadas, atualizando a propriedade appRoles semelhante àquela exibida acima no exemplo. Clique em **Executar Consulta** para executar o operação de patch. Uma mensagem confirma a criação da função do seu aplicativo Amazon Web Services.

        ![Caixa de diálogo do Explorador do Microsoft Graph6](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Após a atualização da Entidade de Serviço com mais funções, será possível atribuir Usuários/Grupos às respectivas funções. Isso pode ser feito, acessando o portal e navegando até o aplicativo Amazon Web Services. Clique na guia **Usuários e Grupos** na parte superior.

1. Recomendamos que você crie novos grupos para cada função AWS para poder atribuir essa função específica a esse grupo. Observe que este é um mapeamento de um grupo para uma função. Em seguida, você pode adicionar os membros que pertencem ao grupo.

1. Uma vez que os Grupos são criados, selecione o grupo e atribua ao aplicativo.

    ![Configurar a adição do logon único1](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Grupos aninhados não têm suporte durante a atribuição de grupos.

1. Para atribuir a função ao grupo, selecione a função e clique no botão **Atribuir** na parte inferior da página.

    ![Configurar a adição do logon único2](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Observe que você precisa atualizar a sua sessão no Portal do Azure para ver as novas funções.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD usando Meus Aplicativos.

Ao clicar no bloco AWS (Amazon Web Services) nos Meus Aplicativos, você deve entrar na página do aplicativo AWS (Amazon Web Services) com a opção para selecionar a função.

![Testar o logon único1](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Você também pode verificar a resposta SAML para ver as funções sendo passadas como declarações.

![Testar o logon único2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar a AWS (Amazon Web Services), você poderá impor o Controle de Sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

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
