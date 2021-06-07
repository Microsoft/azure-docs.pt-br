---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao F5 | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o F5.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 28040a92d713707204f0651773f7edce9f4daf6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651755"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>Tutorial: Configurar o SSO (logon único) entre o Azure Active Directory e o F5

Neste tutorial, você aprenderá a integrar o F5 ao Azure AD (Azure Active Directory). Ao integrar o F5 ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao F5.
* Permitir que os usuários sejam conectados automaticamente ao F5 com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

> [!NOTE]
> F5 BIG-IP APM [Comprar agora](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

* Assinatura habilitada para SSO (logon único) do F5.

* Implantar a solução conjunta requer a seguinte licença:

    * Pacote BIG-IP® Best da F5 (ou) 

    * Licença autônoma do BIG-IP Access Policy Manager™ (APM) da F5 

    * Licença de complemento do BIG-IP Access Policy Manager™ (APM) da F5 em um BIG-IP® Local Traffic Manager™ (LTM) da F5.

    * Além da licença acima, o sistema da F5 também pode ser licenciado com: 

        * Uma assinatura de Filtragem de URL para usar o banco de dados de categoria de URL

        * Uma assinatura do IP Intencionado da F5 para detectar e bloquear invasores conhecidos e tráfego mal-intencionado
     
        * Um HSM (módulo de segurança de hardware) de rede para proteger e gerenciar chaves digitais para ter uma autenticação forte

* O sistema de BIG-IP da F5 é provisionado com módulos do APM (o LTM é opcional)

* Embora seja opcional, é altamente recomendável implantar os sistemas da F5 em um [S/F DG](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (grupo de dispositivos de sincronização/failover), que inclui um par de espera ativo, com um endereço IP flutuante para oferecer HA (alta disponibilidade). A redundância de interface adicional pode ser obtida usando o LACP (Protocolo de Controle de Agregação de Link). O LACP gerencia as interfaces físicas conectadas como uma única interface virtual (grupo de agregação) e detecta falhas de interface dentro do grupo.

* Para aplicativos Kerberos, uma conta de serviço do AD local para delegação restrita.  Veja a [Documentação da F5](https://support.f5.com/csp/article/K43063049) para criar uma conta de delegação do AD.

## <a name="access-guided-configuration"></a>Configuração guiada de acesso

* A configuração guiada de acesso é compatível com o TMOS da F5 versão 13.1.0.8 e superior. Se o sistema BIG-IP estiver executando uma versão inferior à 13.1.0.8, confira a seção **Configuração avançada**.

* A configuração guiada de acesso apresenta uma experiência do usuário completamente nova e simplificada. Essa arquitetura baseada em fluxo de trabalho fornece etapas de configuração reentrantes e intuitivas adaptadas à topologia selecionada.

* Antes de passar para a configuração, atualize a configuração guiada baixando o mais recente pacote de caso de uso de [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Para atualizar, siga o procedimento abaixo.

    >[!NOTE]
    >As capturas de tela abaixo são para a versão mais recente lançada (BIG-IP 15.0 com AGC versão 5.0). As etapas de configuração a seguir são válidas para esse caso de uso da versão 13.1.0.8 à versão do BIG-IP mais recente.

1. Na interface do usuário da Web de BIG-IP da F5, clique em **Acessar >> Configuração do Guia**.

1. Na página **Configuração Guiada**, clique em **Atualizar Configuração Guiada** no canto superior esquerdo.

    ![A captura de tela mostra a página de configuração guiada com o link Atualizar Configuração Guiada.](./media/headerf5-tutorial/configure14.png) 

1. Na tela pop-up Atualizar Configuração do Guia, selecione **Escolher Arquivo** para fazer upload do pacote de casos de uso baixado e clique no botão **Fazer Upload e Instalar**.

    ![A captura de tela mostra a caixa de diálogo Atualizar Configuração Guiada com Escolher Arquivo selecionado.](./media/headerf5-tutorial/configure15.png) 

1. Quando a atualização for concluída, clique no botão **Continuar**.

    ![A captura de tela mostra a caixa de diálogo Atualizar Configuração Guiada com uma mensagem de conclusão.](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SSO do F5 pode ser configurado de três maneiras.

- [Configurar logon único do F5 para aplicativo baseado em cabeçalho](#configure-f5-single-sign-on-for-header-based-application)

- [Configurar logon único do F5 para aplicativo Kerberos](kerbf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos Avançado](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Cenários de autenticação de chaves

* Além do suporte à integração nativa do Azure Active Directory para protocolos de autenticação modernos, como Open ID Connect, SAML e WS-Fed, a F5 estende o acesso seguro para aplicativos de autenticação herdados para acesso interno e externo com o Azure AD, habilitando cenários modernos (por exemplo, acesso sem senha) a esses aplicativos. Isso inclui:

* Aplicativos de autenticação baseada em cabeçalho

* Aplicativos de autenticação Kerberos

* Autenticação anônima ou nenhum aplicativo de autenticação interno

* Aplicativos de autenticação NTLM (proteção com prompts duplos para o usuário)

* Aplicativo baseado em formulários (proteção com prompts duplos para o usuário)

## <a name="adding-f5-from-the-gallery"></a>Adicionar o F5 da galeria

Para configurar a integração do F5 ao Azure AD, você precisa adicionar o F5 por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **F5** na caixa de pesquisa.
1. Selecione **F5** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-f5"></a>Configurar e testar o SSO do Azure AD para o F5

Configure e teste o SSO do Azure AD com o F5 usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no F5.

Para configurar e testar o SSO do Azure AD com o F5, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do F5](#configure-f5-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do F5](#create-f5-test-user)** – para ter um equivalente de B. Fernandes no F5 vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **F5**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o F5**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao F5.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **F5**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-f5-sso"></a>Configurar o SSO do F5

- [Configurar logon único do F5 para aplicativo Kerberos](kerbf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos Avançado](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Configurar logon único do F5 para aplicativo baseado em cabeçalho

### <a name="guided-configuration"></a>Configuração guiada

1. Abra uma nova janela do navegador da Web, entre no site da empresa F5 (Baseado em Cabeçalho) como administrador e execute as seguintes etapas:

1. Navegue até **Sistema > Gerenciamento de Certificado > Gerenciamento de Certificado de Tráfego > Lista de Certificados SSL**. Selecione **Importar** do lado direito. Especifique um **Nome do Certificado** (será referenciado futuramente na configuração). Na **Origem do Certificado**, selecione Fazer Upload de Arquivo e especifique o certificado baixado do Azure ao configurar o Logon Único do SAML. Clique em **Importar**.

    ![A captura de tela mostra a Lista de Certificados SSL em que você seleciona o Nome do Certificado e a Origem do Certificado.](./media/headerf5-tutorial/configure12.png)
 
1. Além disso, você precisará do **Certificado SSL para o Nome do Host do Aplicativo. Navegue até Sistema > Gerenciamento de Certificado > Gerenciamento de Certificado de Tráfego > Lista de Certificados SSL**. Selecione **Importar** do lado direito. O **Tipo de Importação** será **PKCS 12(IIS)** . Especifique um **Nome da Chave** (será referenciado futuramente na configuração) e especifique o arquivo PFX. Especifique a **Senha** do PFX. Clique em **Importar**.

    >[!NOTE]
    >No exemplo, o nome do aplicativo é `Headerapp.superdemo.live`; estamos usando um Certificado Curinga; nosso keyname é `WildCard-SuperDemo.live`.

    ![A captura de tela mostra a página Origem do Certificado/Chave SSL.](./media/headerf5-tutorial/configure13.png)

1. Usaremos a Experiência Guiada para configurar a Federação do Azure AD e o Acesso ao Aplicativo. Acesse – **Principal** do BIG-IP da F5 e selecione **Acessar > Configuração Guiada > Federação > Provedor de Serviços SAML**. Clique em **Avançar**; em seguida, clique em **Avançar** para iniciar a configuração.

    ![A captura de tela mostra a página Configuração Guiada com Federação selecionada.](./media/headerf5-tutorial/configure01.png)

    ![A captura de tela mostra a página Provedor de Serviços SAML.](./media/headerf5-tutorial/configure02.png)
 
1. Forneça um **Nome de Configuração**. Especifique a **ID da Entidade** (a mesma que você definiu na Configuração de Aplicativos do Azure AD). Especifique o **Nome do host**. Adicione uma **Descrição** para consultar. Aceite as entradas padrão restantes e selecione e clique em **Salvar e Avançar**.

    ![A captura de tela mostra a página Propriedades do Provedor de Serviços.](./media/headerf5-tutorial/configure03.png) 

1. Neste exemplo, estamos criando um Servidor Virtual como 192.168.30.20 com a porta 443. Especifique o endereço IP do Servidor Virtual no **Endereço de Destino**. Selecione o **Perfil SSL** do cliente e selecione Criar. Especifique o certificado de aplicativo carregado anteriormente (o certificado curinga neste exemplo) e a chave associada. Em seguida, clique em **Salvar e Avançar**.

    >[!NOTE]
    >Neste exemplo, nosso webserver interno está sendo executado na porta 888 e queremos publicá-lo com a 443.

    ![A captura de tela mostra a página Propriedades do Servidor Virtual.](./media/headerf5-tutorial/configure04.png) 

1. Em **Selecione um método para configurar seu conector IdP**, especifique os Metadados, clique em Escolher Arquivo e faça upload do arquivo XML de Metadados baixado anteriormente do Azure AD. Especifique um **Nome** exclusivo para o conector IDP do SAML. Escolha o **Certificado de Autenticação de Metadados** que foi carregado anteriormente. Clique em **Salvar e Avançar**.

    ![A captura de tela mostra a página Configurações do Conector do Provedor de Identidade Externo.](./media/headerf5-tutorial/configure05.png)
 
1. Em **Selecione um Pool**, especifique **Criar** (ou selecione um pool que já existe). Deixe o outro valor ser o padrão. Em Servidores de Pool, digite o Endereço IP em **Endereço IP/Nome do Nó**. Especifique a **Porta**. Clique em **Salvar e Avançar**.

    ![A captura de tela mostra a página Propriedades do Pool.](./media/headerf5-tutorial/configure06.png)

1. Na tela Configurações de Logon Único, selecione **Habilitar Logon Único**. Em Tipo de Logon Único Selecionado, escolha **HTTP baseado em cabeçalho**. Substitua **session.saml.last.Identity** por **session.saml.last.attr.name.Identity** em Origem do Nome de Usuário (esta variável é definida usando o mapeamento de declarações no Azure AD). Em Cabeçalhos de SSO.

    * **HeaderName: MyAuthorization**

    * **Valor do cabeçalho: %{session.saml.last.attr.name.Identity}**

    * Clique em **Salvar e Avançar**

    Confira no Apêndice uma lista completa de variáveis e valores. Você pode adicionar mais cabeçalhos conforme necessário.

    >[!NOTE]
    >O nome da conta é a conta de delegação de F5 criada (confira a Documentação do F5).

    ![A captura de tela mostra a página Configurações de Logon Único.](./media/headerf5-tutorial/configure07.png) 

1. Para fins destas diretrizes, ignoraremos as verificações de ponto de extremidade.  Veja a documentação da F5 para ver os detalhes. Selecione **Salvar e Avançar**.

    ![A captura de tela mostra a página Propriedades de Verificações de Ponto de Extremidade.](./media/headerf5-tutorial/configure08.png)

1. Aceite os padrões e clique em **Salvar e Avançar**. Veja a documentação da F5 para obter detalhes sobre as configurações de gerenciamento de sessão do SAML.

    ![A captura de tela mostra a página Configurações de Tempo Limite.](./media/headerf5-tutorial/configure09.png)

1. Examine a tela de resumo e selecione **Implantar** para configurar o BIG-IP. Clique em **Concluir**.

    ![A captura de tela mostra a página Seu aplicativo está pronto para ser implantado.](./media/headerf5-tutorial/configure10.png)

    ![A captura de tela mostra a página Seu aplicativo está implantado.](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Configuração avançada

Esta seção deverá ser usada se você não puder usar a Configuração guiada ou desejar adicionar/modificar parâmetros adicionais. Você precisará do certificado TLS/SSL para o Nome do Host do Aplicativo.

1. Navegue até **Sistema > Gerenciamento de Certificado > Gerenciamento de Certificado de Tráfego > Lista de Certificados SSL**. Selecione **Importar** do lado direito. O **Tipo de Importação** será **PKCS 12(IIS)** . Especifique um **Nome da Chave** (será referenciado futuramente na configuração) e especifique o arquivo PFX. Especifique a **Senha** do PFX. Clique em **Importar**.

    >[!NOTE]
    >No exemplo, o nome do aplicativo é `Headerapp.superdemo.live`; estamos usando um Certificado Curinga; nosso keyname é `WildCard-SuperDemo.live`.
  
    ![A captura de tela mostra a página Origem do Certificado/Chave SSL para Configuração Avançada.](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Como adicionar um novo servidor Web a BigIP-F5

1. Clique em **Principal > IApps > Serviços de Aplicativo > Aplicativo > Criar**.

1. Forneça o **Nome** e, em **Modelo**, escolha **f5.http**.
 
    ![A captura de tela mostra a página Serviços de Aplicativos com Seleção de Modelo.](./media/headerf5-tutorial/configure18.png)

1. Publicaremos nosso HeaderApp2 externamente como HTTPS nesse caso, **como o sistema de BIG-IP deve lidar com o tráfego SSL**? Especificamos **Encerrar o SSL do cliente, texto não criptografado para servidores (descarregamento de SSL)** . Especifique o Certificado e a Chave em **Qual certificado SSL você deseja usar?** e **Qual chave privada SSL você deseja usar?** . Especifique o IP do servidor virtual em **Qual endereço IP você deseja usar para o servidor virtual?** . 

    * **Especificar outros detalhes**

        * FQDN  

        * Especifique o pool de aplicativos de saída ou crie um.

        * Se estiver criando um servidor de aplicativos, especifique **Endereço IP Interno** e **número da porta**.

        ![A captura de tela mostra o painel em que você pode especificar esses detalhes.](./media/headerf5-tutorial/configure19.png) 

1. Clique em **Concluído**.

    ![A captura de tela mostra a página após a conclusão.](./media/headerf5-tutorial/configure20.png) 

1. Verifique se as propriedades do aplicativo podem ser modificadas. Clique em **Principal > IApps > Serviços de Aplicativo: Aplicativos >> HeaderApp2**. Desmarque **Atualizações Estritas** (modificaremos algumas configurações fora da GUI). Clique no botão **Atualizar**.

    ![A captura de tela mostra a página Serviços de Aplicativos com a guia Propriedades selecionada.](./media/headerf5-tutorial/configure21.png) 

1. Neste ponto, você deve ser capaz de navegar pelo servidor virtual.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Configuração de F5 como SP e Azure como IDP

1.  Clique em **Acessar > Federação > Provedor de Serviços SAML > Serviço SP Local > clique em criar ou + assinar**.

    ![A captura de tela mostra a página Sobre este BIG IP. ](./media/headerf5-tutorial/configure22.png)

1. Especifique os detalhes para o serviço do provedor de serviços. Especifique **Nome** representando a Configuração de SP F5. Especifique **ID da Entidade** (geralmente igual à URL do aplicativo).

    ![A captura de tela mostra a página Provedor de Serviços SAML com a caixa de diálogo Criar Serviço do SP SAML.](./media/headerf5-tutorial/configure23.png)

    ![A captura de tela mostra a caixa de diálogo Criar Serviço do SP SAML com Configurações de Ponto de Extremidade selecionadas.](./media/headerf5-tutorial/configure24.png)

    ![A captura de tela mostra a caixa de diálogo Criar Serviço do SP SAML com Configurações de Segurança selecionadas.](./media/headerf5-tutorial/configure25.png)

    ![A captura de tela mostra a caixa de diálogo Criar Serviço do SP SAML com Contexto de Autenticação selecionado.](./media/headerf5-tutorial/configure26.png)

    ![A captura de tela mostra a caixa de diálogo Criar Serviço do SP SAML com Atributos Solicitados selecionado.](./media/headerf5-tutorial/configure27.png)

    ![A captura de tela mostra a caixa de diálogo Editar Serviço do SP SAML com Configurações Avançadas selecionado.](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Criar conector Idp

1. Clique no botão **Associar/Desassociar Conectores IdP**, selecione **Criar Conector IdP** e escolha entre a opção **Metadados** e, em seguida, execute as seguintes etapas:
 
    ![A captura de tela mostra a caixa de diálogo Editar IdPs SAML que usam este SP com Criar Conector do IdP selecionado.](./media/headerf5-tutorial/configure29.png)

    a. Navegue até o arquivo metadata.xml baixado do Azure AD e especifique um **Nome de Provedor de Identidade**.

    b. Clique em **OK**.

    c. O conector é criado e o certificado fica pronto automaticamente do arquivo XML de metadados.
    
    ![A captura de tela mostra a caixa de diálogo Criar Conector do IdP SAML.](./media/headerf5-tutorial/configure30.png)

    d. Configure o F5BIG-IP para enviar todas as solicitações ao Azure AD.

    e. Clique em **Adicionar Nova Linha**, escolha **AzureIDP** (conforme criado nas etapas anteriores, especifique 

    f. **Fonte correspondente = %{session.server.landinguri}** 

    g. **Valor correspondente = /** *

    h. Clique em **Atualizar**

    i. Clique em **OK**

    j. **A instalação do IDP do SAML foi concluída**
    
    ![A captura de tela mostra a caixa de diálogo Editar IdPs SAML que este usuário contém.](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Configurar a política F5 para redirecionar os usuários para o IDP do SAML Azure

1. Para configurar a política F5 para redirecionar os usuários para o IDP do SAML do Azure, execute as seguintes etapas:

    a. Clique em **Principal > Acessar > Perfil/Políticas > Perfis de Acesso**.

    b. Clique no botão **Criar**.

    ![A captura de tela mostra a página Perfis de Acesso.](./media/headerf5-tutorial/configure32.png)
 
    c. Especifique **Nome** (HeaderAppAzureSAMLPolicy no exemplo).

    d. Você pode personalizar outras configurações. Veja a documentação de F5.

    ![A captura de tela mostra a página Propriedades Gerais.](./media/headerf5-tutorial/configure33.png)

    ![A captura de tela mostra a página Propriedades Gerais (continuação).](./media/headerf5-tutorial/configure34.png) 

    e. Clique em **Concluído**.

    f. Quando a criação da política for concluída, clique na política e vá para a guia **Política de Acesso**.

    ![A captura de tela mostra a guia Política de Acesso com Propriedades Gerais.](./media/headerf5-tutorial/configure35.png)
 
    g. Clique no **Editor de Política Visual**, edite o link **Política de Acesso para Perfil**.

    h. Clique no sinal de + no editor de Política Visual e escolha **Autenticação SAML**.

    ![A captura de tela mostra uma Política de Acesso.](./media/headerf5-tutorial/configure36.png)

    ![A captura de tela mostra uma caixa de diálogo de pesquisa com Autenticação SAML selecionada.](./media/headerf5-tutorial/configure37.png)
 
    i. Clique em **Adicionar Item**.

    j. Em **Propriedades**, especifique **Nome** e, em **Servidor AAA**, selecione o SP configurado anteriormente; clique em **SALVAR**.
 
    ![A captura de tela mostra as Propriedades do item, incluindo seu servidor AAA.](./media/headerf5-tutorial/configure38.png)

    k. A política básica está pronta. Você pode personalizá-la para incorporar fontes/armazenamentos de atributos adicionais.

    ![A captura de tela mostra a política personalizada.](./media/headerf5-tutorial/configure39.png)
 
    l. Clique no link **Aplicar Política de Acesso** na parte superior.

### <a name="apply-access-profile-to-the-virtual-server"></a>Aplicar perfil de acesso ao servidor virtual

1. Atribua o perfil de acesso ao servidor virtual para que F5 BIG-IP APM aplique as configurações de perfil ao tráfego de entrada e execute a política de acesso definida anteriormente.

    a. Clique em **Principal** > **Tráfego Local** > **Servidores Virtuais**.

    ![A captura de tela mostra a página Lista de Servidores Virtuais.](./media/headerf5-tutorial/configure40.png)
 
    b. Clique no servidor virtual, role até a seção **Política de Acesso**, na lista suspensa **Perfil de Acesso** e selecione a política SAML criada (no exemplo HeaderAppAzureSAMLPolicy)

    c. Clique em **Atualizar**
 
    ![A captura de tela mostra o painel Política de Acesso.](./media/headerf5-tutorial/configure41.png)

    d. Crie um F5 BIG-IP iRule® para extrair os atributos SAML personalizados da declaração de entrada e passá-los como cabeçalhos HTTP para o aplicativo de teste de back-end. Clique em **Principal > Tráfego Local > iRules > Lista do iRule > clique em criar**

    ![A captura de tela mostra a lista iRule do Tráfego Local.](./media/headerf5-tutorial/configure42.png)
 
    e. Cole o texto F5 BIG-IP iRule abaixo na janela de definição.

    ![A captura de tela mostra a página Nova iRule.](./media/headerf5-tutorial/configure43.png)
 
    quando RULE_INIT { set static::debug 0 } quando ACCESS_ACL_ALLOWED {

    defina AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] se { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } se { !([HTTP::header exists "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    defina AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] se { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } se { !([HTTP::header exists "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    defina AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] se { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } se { !([HTTP::header exists "AZUREAD_EMAILADDRESS"]) } { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Saída de exemplo abaixo**

    ![A captura de tela mostra a saída de exemplo.](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Criar usuário de teste do F5

Nesta seção, você criará um usuário chamado B.Fernandes no F5. Trabalhe com a [equipe de suporte ao cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os usuários na plataforma do F5. Os usuários devem ser criados e ativados antes de usar o logon único. 

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso o redirecionará para a URL de Logon do F5, na qual você poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do F5 diretamente e inicie o fluxo de logon de lá.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e entre automaticamente no F5 para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do F5 no portal Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao F5 para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

> [!NOTE]
> F5 BIG-IP APM [Comprar agora](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o F5, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).