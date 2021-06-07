---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory com o Oracle PeopleSoft – Protegido por F5 BIG-IP APM | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Oracle PeopleSoft – Protegido por F5 BIG-IP APM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 3af149f0c1db7f354be6bd968bbd0cf858493d4c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219290"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory com o Oracle PeopleSoft – Protegido por F5 BIG-IP APM

Neste tutorial, você aprenderá a integrar o Oracle PeopleSoft – Protegido por F5 BIG-IP APM com o Azure AD (Azure Active Directory). Ao integrar o Oracle PeopleSoft – Protegido por F5 BIG-IP APM com o Azure AD, você poderá:

* Controlar no Azure AD quem terá acesso ao Oracle PeopleSoft – Protegido por F5 BIG-IP APM.
* Permitir que os usuários entrem automaticamente no Oracle PeopleSoft – Protegido por F5 BIG-IP APM com suas respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.
* Este tutorial aborda as instruções para o Oracle PeopleSoft ELM.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

1. Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
1. Assinatura do Oracle PeopleSoft – Protegido por F5 BIG-IP APM habilitada para SSO (logon único).

1. Implantar a solução conjunta requer a seguinte licença:

    1. Pacote BIG-IP® Best da F5 (ou) 
    2. Licença autônoma do BIG-IP Access Policy Manager™ (APM) da F5 
    3. Licença de complemento do BIG-IP Access Policy Manager™ (APM) da F5 em um BIG-IP® Local Traffic Manager™ (LTM) da F5.
    4. Além da licença acima, o sistema da F5 também pode ser licenciado com: 
        * Uma assinatura de Filtragem de URL para usar o banco de dados de categoria de URL. 
        * Uma assinatura do IP Intelligence da F5 para detectar e bloquear invasores conhecidos e tráfego mal-intencionado. 
        * Um HSM (módulo de segurança de hardware) de rede para proteger e gerenciar chaves digitais para ter uma autenticação forte.
1. O sistema de BIG-IP da F5 é provisionado com módulos do APM (o LTM é opcional).
1. Embora seja opcional, é altamente recomendável implantar os sistemas da F5 em um [S/F DG](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (grupo de dispositivos de sincronização/failover), que inclui um par de espera ativo, com um endereço IP flutuante para oferecer HA (alta disponibilidade). A redundância de interface adicional pode ser obtida usando o LACP (Protocolo de Controle de Agregação de Link). O LACP gerencia as interfaces físicas conectadas como uma única interface virtual (grupo de agregação) e detecta falhas de interface dentro do grupo.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Oracle PeopleSoft – protegido por F5 BIG-IP APM é compatível com o SSO iniciado por **SP e IDP**.

## <a name="add-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>Adicionar o Oracle PeopleSoft – protegido por F5 BIG-IP APM da galeria

Para configurar a integração do Oracle PeopleSoft – Protegido por F5 BIG-IP APM com o Azure AD, você precisará adicionar o Oracle PeopleSoft – Protegido por F5 BIG-IP APM da galeria para a lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Oracle PeopleSoft – Protegido por F5 BIG-IP APM** na caixa de pesquisa.
1. Selecione **Oracle PeopleSoft – Protegido por F5 BIG-IP APM** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Configurar e testar o SSO do Azure AD para o Oracle PeopleSoft – Protegido por F5 BIG-IP APM

Configure e teste o SSO do Azure AD com o Oracle PeopleSoft – Protegido por F5 BIG-IP APM usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Oracle PeopleSoft – Protegido por F5 BIG-IP APM.

Para configurar e testar o SSO do Azure AD com o Oracle PeopleSoft – Protegido por F5 BIG-IP APM, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configure o SSO do Oracle PeopleSoft – Protegido por F5 BIG-IP APM](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Crie o usuário de teste do Oracle PeopleSoft – Protegido por F5 BIG-IP APM](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** – para ter um equivalente do B.Fernandes no Oracle PeopleSoft – Protegido por F5 BIG-IP APM que esteja vinculado à representação deste usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Oracle PeopleSoft – Protegido por F5 BIG-IP APM**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<FQDN>.peoplesoft.f5.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. Na caixa de texto **URL de Logoff**, digite uma URL usando o seguinte padrão: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >Esses valores não são reais. Atualize esses valores com a URL de Logon, o Identificador, a URL de Resposta e a URL de Logoff reais. Entre em contato com a [equipe de suporte ao cliente do Oracle PeopleSoft – Protegido por F5 BIG-IP APM](https://support.f5.com) para obter os valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Oracle PeopleSoft – Protegido por F5 BIG-IP APM espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Oracle PeopleSoft – Protegido por F5 BIG-IP APM espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados a seguir. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | ------------------ | --------- |
    | EMPLID | user.employeeid |

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, baixe o **XML de Metadados de Federação** e o **Certificado (Base64)** e salve-os no computador.

    ![O link de download do Certificado](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao permitir-lhe acesso ao Oracle PeopleSoft – Protegido por F5 BIG-IP APM.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Oracle PeopleSoft – Protegido por F5 BIG-IP APM**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Configurar o SSO do Oracle PeopleSoft – Protegido por F5 BIG-IP APM

### <a name="f5-saml-sp-configuration"></a>Configuração do SP no SAML da F5

Importe o Certificado de Metadados para a F5 que será usado posteriormente no processo de configuração. Navegue até **Sistema > Gerenciamento de Certificado > Gerenciamento de Certificado de Tráfego > Lista de Certificados SSL**. Selecione **Importar** do lado direito.

![Configuração do SP no SAML da F5](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>Configurar o conector IDP do SAML 

1. Navegue até **Acesso > Federação > SAML: Provedor de Serviços > Conectores IdP Externos** e clique em **Criar > Com Base em Metadados**.

    ![Conector IDP do SAML da F5](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. Na página a seguir, clique em **Procurar** para carregar o arquivo XML.

1. Forneça um nome válido na caixa de texto **Nome do Provedor de Identidade** e clique em **OK**.

    ![novo Conector IDP do SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. Execute as etapas necessárias na guia **Configurações de Segurança** e clique em **OK**.

    ![editar Conector IDP do SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>Configurar o SP no SAML

1. Navegue até **Acesso > Federação > Provedor de Serviço do SAML > Serviços SP Locais** e clique em **Criar**. Preencha as seguintes informações e clique em **OK**.

    * Nome: `<Name>`
    * ID da Entidade: `https://<FQDN>.peoplesoft.f5.com`
    * Esquema de Configurações
        * Esquema: `https`
        * Host: `<FQDN>.peoplesoft.f5.com`
        * Descrição: `<Description>`

    ![novos serviços do SP no SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. Selecione a Configuração do SP, PeopleSoftAppSSO, e **Clique em Associar/Desassociar Conectores IdP**.
Clique em **Adicionar Nova Linha** e selecione o **Conector IdP externo** criado na etapa anterior. Clique em **Atualizar** e, em seguida, em **OK**.

    ![criar serviços do SP no SAML](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>Configurando o Aplicativo

### <a name="create-a-new-pool"></a>Criar um Pool
1. Navegue até **Tráfego Local > Pools > Lista de Pools**, clique em **Criar**, preencha as informações a seguir e clique em **Concluído**.

    * Nome: `<Name>`
    * Descrição: `<Description>`
    * Monitores de Integridade: `http`
    * Endereço: `<Address>`
    * Porta de serviço: `<Service Port>`

    ![criação do pool](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>Criar um perfil de Cliente SSL

Navegue até **Tráfego Local > Perfis > SSL > Cliente > +** , preencha as informações a seguir e clique em **Concluído**.

* Nome: `<Name>`
* Certificado: `<Certificate>`
* Chave: `<Key>`

![novo perfil de Cliente SSL](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>Criar um Servidor Virtual

1. Navegue até **Tráfego Local > Servidores Virtuais > Lista de Servidores Virtuais > +** , preencha as informações a seguir e clique em **Concluído**.
    * Nome: `<Name>`
    * Endereço de Destino/Máscara: `<Address>`
    * Porta de serviço: Porta 443 HTTPS
    * Perfil HTTP (Cliente): http

    ![Criar um Servidor Virtual](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. Preencha os seguintes valores na página abaixo:

    * Perfil SSL (Cliente): `<SSL Profile>`
    * Conversão do Endereço de Origem: Mapa Automático
    * Perfil de Acesso: `<Access Profile>`
    * Pool Padrão: `<Pool>`


    ![Criar um pool do PeopleSoft no Servidor Virtual](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>Configurando o aplicativo do PeopleSoft para dar suporte ao F5 Big-IP APM como a solução de logon único

>[!Note]
> Referência https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. Faça logon no Console do PeopleSoft `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` usando credenciais de administrador (exemplo: PS/PS).

    ![Autoatendimentos do Gerenciador](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. No aplicativo do PeopleSoft, crie o perfil do usuário **OAMPSFT** e associe a ele uma função de segurança baixa, como **Usuário do PeopleSoft**.
Navegue até **PeopleTools > Segurança > Perfis do Usuário > Perfis de Usuário** para criar um perfil do usuário, por exemplo: **OAMPSFT** e adicione **Usuário do PeopleSoft**.

    ![Usuário do PeopleSoft](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Acesse o perfil da Web e insira **OAMPSFT** como a **ID de usuário** de acesso público.

    ![Perfis de Usuário](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. No **Designer de Aplicativos da PeopleTools**, abra o registro **FUNCLIB_LDAP**.

    ![configuração do perfil da Web](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. Atualize o cabeçalho do usuário com **PS_SSO_UID** para a função **OAMSSO_AUTHENTICATION**.
Na função **getWWWAuthConfig()** , substitua o valor atribuído à **&defaultUserId** com o **OAMPSFT** que definimos no perfil da Web. Salve a definição do registro.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. Acesse a página do **PeopleCode de Entrada** (PeopleTools, Segurança, Objetos de Segurança, PeopleCode de Entrada) e habilite a função **OAMSSO_AUTHENTICATION** – o PeopleCode de Entrada para logon único do Oracle Access Manager.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>Configurando F5 Big-IP APM para popular o cabeçalho HTTP "PS_SSO_UID" com a ID de Usuário do PeopleSoft

### <a name="configuring-per-request-policy"></a>Configurando a Política por Solicitação
1. Navegue até **Acessar > Perfil/Políticas > Políticas por Solicitação**, clique em **Criar**, preencha as informações a seguir e clique em **Concluído**.

    * Nome: `<Name>`
    * Tipo de Perfil: Todos
    * Linguagens: `<Language>`

    ![Configurando a Política por Solicitação ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. Clique em **Editar** Política por Solicitação `<Name>` ![editar Política por Solicitação do PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>Atribuir Política por Solicitação ao Servidor Virtual

Navegue até **Tráfego Local > Servidores Virtuais > Lista de Servidores Virtuais > PeopleSoftApp** e especifique `<Name>` como Política por Solicitação

![PeopleSoftSSO como Política por Solicitação ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>Configurando o F5 Big-IP APM para dar suporte ao Logoff Único do aplicativo PeopleSoft

Para adicionar suporte de logout único a todos os usuários do PeopleSoft, siga estas etapas:

1. Determinar a URL de logout correta para o portal do PeopleSoft
    * Para determinar o endereço que o aplicativo PeopleSoft usa para encerrar uma sessão de usuário, você precisa abrir o portal usando qualquer navegador da Web e habilitar as ferramentas de depuração do navegador, conforme mostrado no exemplo abaixo:

        ![URL de logoff para o portal do PeopleSoft ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * Localize o elemento com a ID de `PT_LOGOUT_MENU` e salve o caminho da URL com os parâmetros de consulta. Em nosso exemplo, temos o seguinte valor: `/psp/ps/?cmd=logout`

1. Crie o LTM iRule que redirecionará o usuário para a URL de logoff do APM: `/my.logout.php3`

    * Navegue até **Tráfego Local > iRule**, clique em **Criar**, preencha as informações a seguir e clique em **Concluído**.

        Nome: `<Name>`  
        Definição:  
                    _quando HTTP_REQUEST { switch -glob -- [HTTP::uri] { `/psp/ps/?cmd=logout` { HTTP::redirect `/my.logout.php3` } } }_

1. Atribuir o iRule criado ao Servidor Virtual

    * Navegue até **Tráfego Local > Servidores Virtuais > Lista de Servidores Virtuais > PeopleSoftApp > Recursos**. Clique no botão **Gerenciar…** :   

    * Especifique `<Name>` como iRule Habilitado e clique em **Concluído**.

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * Dê à caixa de texto **Nome** o valor `<Name>` 

        ![_iRule_PeopleSoftApp finished](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Criar o usuário de teste do Oracle PeopleSoft – Protegido por F5 BIG-IP APM

Nesta seção, você criará um usuário chamado B.Fernandes no Oracle PeopleSoft – Protegido por F5 BIG-IP APM. Trabalhe com a [equipe de suporte do Oracle PeopleSoft – Protegido por F5 BIG-IP APM](https://support.f5.com) para adicionar os usuários na plataforma do Oracle PeopleSoft – Protegido por F5 BIG-IP APM. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará para a URL do Oracle PeopleSoft – Protegido por F5 BIG-IP APM BIG-IP, na qual você poderá iniciar o fluxo de logon.  

* Vá para a URL de logon do Oracle PeopleSoft – Protegido por F5 BIG-IP APM diretamente e inicie dali o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no Oracle PeopleSoft – protegido por F5 BIG-IP APM, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no Oracle PeopleSoft – Protegido por F5 BIG-IP APM nos Meus Aplicativos, se estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se estiver configurado no modo IdP, você será conectado automaticamente ao Oracle PeopleSoft – protegido por F5 BIG-IP APM, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Oracle PeopleSoft – protegido por F5 BIG-IP APM, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
