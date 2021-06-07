---
title: 'Tutorial: Integração do logon único do Azure AD ao F5 | Microsoft Docs'
description: Saiba como configurar o SSO (logon único) entre o Azure Active Directory e o F5.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: 2fd96c47e98a6f8a66a5fda555304c4448d772e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646088"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao F5

Neste tutorial, você aprenderá a integrar o F5 ao Azure AD (Azure Active Directory). Ao integrar o F5 ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao F5.
* Permitir que os usuários sejam conectados automaticamente ao F5 com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

* Assinatura habilitada para SSO (logon único) do F5.

* Implantar a solução conjunta requer a seguinte licença:
    * Pacote BIG-IP&reg; Best da F5 (ou)

    * Licença autônoma do BIG-IP Access Policy Manager&trade; (APM) da F5

    * Licença de complemento do BIG-IP Access Policy Manager&trade; (APM) da F5 em um BIG-IP&reg; Local Traffic Manager&trade; (LTM) da F5.

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

2. Na página **Configuração Guiada**, clique em **Atualizar Configuração Guiada** no canto superior esquerdo.

    ![Captura de tela que mostra a página "Configuração Guiada" com a ação "Atualizar Configuração Guiada" selecionada.](./media/kerbf5-tutorial/configure14.png) 

3. Na tela pop-up Atualizar Configuração do Guia, selecione **Escolher Arquivo** para fazer upload do pacote de casos de uso baixado e clique no botão **Fazer Upload e Instalar**.

    ![Captura de tela que mostra a tela pop-up "Atualizar Configuração Guiada" com as opções "Escolher Arquivo" e "Carregar e Instalar" selecionadas.](./media/kerbf5-tutorial/configure15.png) 

4. Quando a atualização for concluída, clique no botão **Continuar**.

    ![Captura de tela que mostra a caixa de diálogo "A atualização da Configuração Guiada foi concluída" e o botão "Continuar" selecionado.](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O F5 dá suporte ao SSO iniciado por **SP e IDP**
* O SSO do F5 pode ser configurado de três maneiras.

- [Configurar logon único do F5 para aplicativo Kerberos](#configure-f5-single-sign-on-for-kerberos-application)

- [Configurar logon único do F5 para aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos Avançado](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Cenários de autenticação de chaves

Além do suporte à integração nativa do Azure Active Directory para protocolos de autenticação modernos, como Open ID Connect, SAML e WS-Fed, a F5 estende o acesso seguro para aplicativos de autenticação herdados para acesso interno e externo com o Azure AD, habilitando cenários modernos (por exemplo, acesso sem senha) a esses aplicativos. Isso inclui:

* Aplicativos de autenticação baseada em cabeçalho

* Aplicativos de autenticação Kerberos

* Autenticação anônima ou nenhum aplicativo de autenticação interno

* Aplicativos de autenticação NTLM (proteção com prompts duplos para o usuário)

* Aplicativo baseado em formulários (proteção com prompts duplos para o usuário)

## <a name="adding-f5-from-the-gallery"></a>Adicionar o F5 da galeria

Para configurar a integração do F5 ao Azure AD, você precisa adicionar o F5 por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **F5** na caixa de pesquisa.
1. Selecione **F5** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurar e testar logon único do Azure AD para o F5

Configure e teste o SSO do Azure AD com o F5 usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no F5.

Para configurar e testar o SSO do Azure AD com o F5, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do F5](#configure-f5-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do F5](#create-f5-test-user)** – para ter um equivalente de B. Fernandes no F5 vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **F5**, localize a seção **Gerenciar** e selecione **logon único**.
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

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.
1. Clique em **Acesso Condicional**.
1. Clique em **Nova Política**.
1. Agora você pode ver seu aplicativo da F5 como um recurso para a política de Acesso Condicional e aplicar qualquer acesso condicional, incluindo a Autenticação Multifator, o controle de acesso baseado em dispositivo ou a Política do Identity Protection.

## <a name="configure-f5-sso"></a>Configurar o SSO do F5

- [Configurar logon único do F5 para aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos Avançado](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Configurar logon único do F5 para aplicativo Kerberos

### <a name="guided-configuration"></a>Configuração guiada

1. Abra uma nova janela do navegador da Web, entre no site da empresa F5 (Kerberos) como administrador e execute as seguintes etapas:

1. Você precisará importar o Certificado de Metadados para a F5 que será usado posteriormente no processo de instalação.

1. Navegue até **Sistema > Gerenciamento de Certificado > Gerenciamento de Certificado de Tráfego > Lista de Certificados SSL**. Selecione **Importar** do lado direito. Especifique um **Nome do Certificado** (será referenciado futuramente na configuração). Na **Origem do Certificado**, selecione Fazer Upload de Arquivo e especifique o certificado baixado do Azure ao configurar o Logon Único do SAML. Clique em **Importar**.

    ![Captura de tela que mostra a página "Certificado SSL/Origem da Chave" com "Nome do Certificado" selecionado e os botões "Carregar Arquivo" e "Importar" selecionados.](./media/kerbf5-tutorial/configure01.png) 

1. Além disso, você precisará do **Certificado SSL para o Nome do Host do Aplicativo. Navegue até Sistema > Gerenciamento de Certificado > Gerenciamento de Certificado de Tráfego > Lista de Certificados SSL**. Selecione **Importar** do lado direito. O **Tipo de Importação** será **PKCS 12(IIS)** . Especifique um **Nome da Chave** (será referenciado futuramente na configuração) e especifique o arquivo PFX. Especifique a **Senha** do PFX. Clique em **Importar**.

    >[!NOTE]
    >No exemplo, o nome do aplicativo é `Kerbapp.superdemo.live`; estamos usando um Certificado Curinga; nosso keyname é `WildCard-SuperDemo.live`

    ![Captura de tela que mostra a página "Certificado do SSL/Origem da Chave" com os valores inseridos e o botão "Importar" selecionado.](./media/kerbf5-tutorial/configure02.png) 
 
1. Usaremos a Experiência Guiada para configurar a Federação do Azure AD e o Acesso ao Aplicativo. Acesse – **Principal** do BIG-IP da F5 e selecione **Acessar > Configuração Guiada > Federação > Provedor de Serviços SAML**. Clique em **Avançar**; em seguida, clique em **Avançar** para iniciar a configuração.

    ![Captura de tela que mostra a página "Configuração Guiada" com o ícone "Federação" realçado e a opção "Provedor de Serviços do SAML" selecionada.](./media/kerbf5-tutorial/configure03.png) 

    ![Captura de tela que mostra a página "Configuração Guiada – Provedor de Serviços do SAML" com o botão "Avançar" selecionado.](./media/kerbf5-tutorial/configure04.png)

1. Forneça um **Nome de Configuração**. Especifique a **ID da Entidade** (a mesma que você definiu na Configuração de Aplicativos do Azure AD). Especifique o **Nome do host**. Adicione uma **Descrição** para consultar. Aceite as entradas padrão restantes e selecione e clique em **Salvar e Avançar**.

    ![Captura de tela que mostra a opção "Propriedades do Provedor de Serviços" com as caixas de texto "Nome do host" e "Descrição" realçadas e o botão "Salvar e Avançar" selecionado.](./media/kerbf5-tutorial/configure05.png) 

1. Neste exemplo, estamos criando um Servidor Virtual como 192.168.30.200 com a porta 443. Especifique o endereço IP do Servidor Virtual no **Endereço de Destino**. Selecione o **Perfil SSL** do cliente e selecione Criar. Especifique o certificado de aplicativo carregado anteriormente (o certificado curinga neste exemplo) e a chave associada. Em seguida, clique em **Salvar e Avançar**.

    >[!NOTE]
    >Neste exemplo, nosso webserver interno está sendo executado na porta 80 e queremos publicá-lo com a 443.

    ![Captura de tela que mostra a página "Propriedades do Servidor Virtual" com a caixa de texto "Endereço de Destino" realçada e o botão "Salvar e Avançar" selecionado.](./media/kerbf5-tutorial/configure06.png)

1. Em **Selecione um método para configurar seu conector IdP**, especifique os Metadados, clique em Escolher Arquivo e faça upload do arquivo XML de Metadados baixado anteriormente do Azure AD. Especifique um **Nome** exclusivo para o conector IDP do SAML. Escolha o **Certificado de Autenticação de Metadados** que foi carregado anteriormente. Clique em **Salvar e Avançar**.

    ![Captura de tela que mostra a página "Configurações do Conector do Provedor de Identidade Externo" com a caixa de texto "Nome" realçada e o botão "Salvar e Avançar" selecionado.](./media/kerbf5-tutorial/configure07.png)  

1. Em **Selecione um Pool**, especifique **Criar** (ou selecione um pool que já existe). Deixe o outro valor ser o padrão.    Em Servidores de Pool, digite o Endereço IP em **Endereço IP/Nome do Nó**. Especifique a **Porta**. Clique em **Salvar e Avançar**.
 
    ![Captura de tela que mostra a página "Propriedades do Pool" com as caixas de texto "Endereço IP/Nome do Nó" e "Porta" realçadas e o botão "Salvar e Avançar" selecionado.](./media/kerbf5-tutorial/configure08.png)

1. Na tela Configurações de Logon Único, selecione **Habilitar Logon Único**. Em **Tipo de Logon Único Selecionado**, escolha **Kerberos**. Substitua **session.saml.last.Identity** por **session.saml.last.attr.name.Identity** em **Origem do Nome de Usuário** (esta variável é definida usando o mapeamento de declarações no Azure AD). Selecione **Mostrar Configurações Avançadas**. Em **Realm Kerberos**, digite o Nome de Domínio. Em **Nome da Conta/Senha da Conta**, especifique a conta e a senha da delegação do APM. Especifique o IP do Controlador de Domínio no campo **KDC**. Clique em **Salvar e Avançar**.

    ![Captura de tela que mostra as "Configurações de Logon Único" com as caixas de texto realçadas e o botão "Salvar e Avançar" selecionado.](./media/kerbf5-tutorial/configure09.png)   

1. Para fins destas diretrizes, ignoraremos as verificações de ponto de extremidade.  Veja a documentação da F5 para ver os detalhes.  Na tela, selecione **Salvar e Avançar**.

    ![Captura de tela que mostra a página "Propriedades de Verificações de Ponto de Extremidade" e o botão "Salvar e Avançar" selecionado.](./media/kerbf5-tutorial/configure10.png) 

1. Aceite os padrões e clique em **Salvar e Avançar**. Veja a documentação da F5 para obter detalhes sobre as configurações de gerenciamento de sessão do SAML.


    ![Captura de tela que mostra a página "Configurações de Tempo Limite" com o botão "Salvar e Avançar" selecionado.](./media/kerbf5-tutorial/configure11.png) 
 
1. Examine a tela de resumo e selecione **Implantar** para configurar o BIG-IP.
 
    ![Captura de tela que mostra a página "Seu aplicativo está pronto para ser implantado" com a seção "Resumo" realçada e o botão "Implantar" selecionado.](./media/kerbf5-tutorial/configure12.png)

1. Depois que o aplicativo for configurado, clique em **Concluir**.

    ![Captura de tela que mostra a página "Seu aplicativo foi implantado" com o botão "Concluir" selecionado.](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Configuração avançada

>[!NOTE]
>Para referência, clique [aqui](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>Configurar um servidor AAA do Active Directory

Configure um servidor AAA do Active Directory no APM (Access Policy Manager) para especificar controladores de domínio e credenciais para o APM usar para autenticar os usuários.

1. Na guia Principal, clique em **Política de Acesso > Servidores AAA > Active Directory**. A tela da lista de Servidores do Active Directory é aberta.

2. Clique em **Criar**. A tela de propriedades do Novo Servidor é aberta.

3. No campo **Nome**, digite um nome exclusivo para o servidor de autenticação.

4. No campo **Nome do Domínio**, digite o nome do domínio do Windows.

5. Para a configuração **Conexão do Servidor**, selecione uma destas opções:

   * Selecione **Usar Pool** para configurar a alta disponibilidade para o servidor AAA.

   * Selecione **Direto** para configurar o servidor AAA para a funcionalidade autônoma.

6. Se tiver selecionado **Direto**, digite um nome no campo **Controlador de Domínio**.

7. Se tiver selecionado Usar **pool**, configure o pool:

   * Digite um nome no campo **Nome do Pool de Controlador de Domínio**.

   * Especifique os **Controladores de Domínio** no pool digitando o endereço IP e o nome do host de cada um deles e clicando no botão **Adicionar**.

   * Para monitorar a integridade do servidor AAA, você tem a opção de selecionar o monitor de integridade: somente o monitor **gateway_icmp** é adequado nesse caso; você pode selecioná-lo na lista **Monitor do Pool de Servidores**.

8. No campo **Nome do Administrador**, digite um nome que diferencia maiúsculas de minúsculas para um administrador que tem permissões de administrador do Active Directory. O APM usa as informações nos campos **Nome do Administrador** e **Senha do Administrador** para a Consulta do AD. Se o Active Directory estiver configurado para consultas anônimas, você não precisará fornecer um Nome do Administrador. Caso contrário, o APM precisará de uma conta com privilégio suficiente para ser associada a um servidor do Active Directory, buscar informações do grupo de usuários e buscar políticas de senha do Active Directory para dar suporte a uma funcionalidade relacionada a senha. (O APM deverá buscar políticas de senha, por exemplo, se você selecionar Solicitar usuário para alterar a senha antes da opção de expiração em uma ação da Consulta do AD.) Se você não fornecer as informações da conta do administrador nessa configuração, o APM usará a conta de usuário para buscar informações. Isso funcionará se a conta de usuário tiver privilégio suficiente.

9. No campo **Senha do Administrador**, digite uma senha do administrador associada ao Nome de Domínio.

10. No campo **Verificar Senha do Administrador**, digite novamente a senha do administrador associada à configuração **Nome do Domínio**.

11. No campo **Tempo de Vida do Cache do Grupo**, digite o número de dias. O tempo de vida padrão é de 30 dias.

12. No campo **Tempo de Vida do Cache do Objeto de Segurança de Senha**, digite o número de dias. O tempo de vida padrão é de 30 dias.

13. Na lista **Tipo de Criptografia de Pré-autenticação Kerberos**, selecione um tipo de criptografia. O padrão é **Nenhum**. Se você especificar um tipo de criptografia, o sistema BIG-IP incluirá os dados de pré-autenticação Kerberos dentro do primeiro pacote AS-REQ (solicitação de serviço de autenticação).

14. No campo **Tempo Limite**, digite um intervalo de tempo limite (em segundos) para o servidor AAA. (Essa configuração é opcional.)

15. Clique em **Concluído**. O novo servidor é exibido na lista. Isso adiciona o novo servidor do Active Directory à lista de Servidores do Active Directory.

    ![Captura de tela que mostra as seções "Propriedades Gerais" e "Configuração".](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>Configuração de SAML

1. Você precisará importar o Certificado de Metadados para a F5 que será usado posteriormente no processo de instalação. Navegue até **Sistema > Gerenciamento de Certificado > Gerenciamento de Certificado de Tráfego > Lista de Certificados SSL**. Selecione **Importar** do lado direito.

    ![Captura de tela que mostra a página "Importar certificado do SSL/Origem da Chave" com o botão "Importar" selecionado.](./media/kerbf5-tutorial/configure18.png)

2. Para configurar o IDP do SAML, **navegue até Acessar > Federação > SAML: Provedor de Serviços > Conectores IdP Externos** e clique em **Criar > Com Base em Metadados**.

    ![Captura de tela que mostra a página "Provedor de Serviços SAML" com a opção "de Metadados" selecionada na lista suspensa "Criar".](./media/kerbf5-tutorial/configure19.png)

    ![Captura de tela que mostra a caixa de diálogo "Criar Conector do IdP SAML".](./media/kerbf5-tutorial/configure20.png)

    ![Captura de tela que mostra a janela "Editar Conector do IdP SAML" com a opção "Configurações Gerais" selecionada.](./media/kerbf5-tutorial/configure21.png)

    ![Captura de tela que mostra a janela "Editar Conector do IdP SAML" com a opção "Configurações do Serviço de Logon Único" selecionada.](./media/kerbf5-tutorial/configure22.png)

    ![Captura de tela que mostra a janela "Editar o Conector do IdP SAML" com a opção "Configurações de Segurança" selecionada.](./media/kerbf5-tutorial/configure23.png)

    ![Captura de tela que mostra a janela "Editar o Conector do IdP SAML" com a opção "Configurações do Serviço de SLO" selecionada.](./media/kerbf5-tutorial/configure24.png)

1. Para configurar o SP SAML, navegue até **Acessar > Federação > Provedor de Serviço SAML > Serviços SP Locais** e clique em **Criar**. Preencha as seguintes informações e clique em **OK**.

    * Nome do tipo: KerbApp200SAML
    * ID da entidade*: https://kerb-app.com.cutestat.com
    * Esquema de Configurações
    * de Nome SP: https
    * Host: kerbapp200.superdemo.live
    * Descrição: kerbapp200.superdemo.live

     ![Captura de tela que mostra a janela "Editar o Serviço SP do SAML" com a opção "Configurações Gerais" selecionada.](./media/kerbf5-tutorial/configure25.png)

     b. Selecione a Configuração do SP, KerbApp200SAML, e clique em **Associar/Desassociar conectores IdP**.

     ![Captura de tela que mostra a página "Provedor de Serviços do SAML – Serviços do SP Local" com a opção "SAML do KerbAPP200" selecionada.](./media/kerbf5-tutorial/configure26.png)

     ![Captura de tela que mostra o botão "Associar/Desassociar Conectores do IdP" selecionado.](./media/kerbf5-tutorial/configure27.png)

     c. Clique em **Adicionar Nova Linha** e selecione o **Conector IdP externo** criado na etapa anterior. Clique em **Atualizar** e, em seguida, em **OK**.

     ![Captura de tela que mostra a janela "Editar IdPs de SAML que usam este SP" com o botão "Adicionar Nova Linha" selecionado.](./media/kerbf5-tutorial/configure28.png)

1. Para configurar o SSO do Kerberos, navegue até **Acessar > Logon Único > Kerberos**, preencha as informações e clique em **Concluído**.

    >[!Note]
    > Será necessário criar e especificar a Conta de Delegação do Kerberos. Veja a Seção do KCD (Consulta o Apêndice para obter Referências de Variável)

    * **Origem do nome de usuário**: session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **Origem do Realm do Usuário**: session.logon.last.domain

        ![Captura de tela que mostra a página "Logon Único – Propriedades" com as caixas de texto "Origem do Nome de Usuário" e "Origem do Realm do Usuário" realçadas.](./media/kerbf5-tutorial/configure29.png)

1. Para configurar o Perfil de Acesso, navegue até **Acessar > Perfil/Políticas > Perfil de Acesso (de acordo com as políticas da sessão)** , clique em **Criar**, preencha as seguintes informações e clique em **Concluído**.

    * Nome: KerbApp200
    * Tipo de Perfil: Todos
    * Escopo do Perfil: Perfil
    * Linguagens: Inglês

        ![Captura de tela que mostra a página "Perfis/Políticas – Propriedades" com as caixas de texto "Nome", "Tipo de Perfil" e "Idiomas" realçadas.](./media/kerbf5-tutorial/configure30.png)

1. Clique no nome, KerbApp200, preencha as seguintes informações e clique em **Atualizar**.

    * Cookie do Domínio: superdemo.live
    * Configuração de SSO: KerAppSSO_sso

        ![Captura de tela que mostra a página "SSD/Domínios de Autenticação" com a caixa de texto "Cookie de Domínio" e a lisa suspensa "Configuração de SSO" realçada e o botão "Atualizar" selecionado.](./media/kerbf5-tutorial/configure31.png)

1. Clique em **Política de Acesso** e clique em **Editar Política de Acesso** para o perfil "KerbApp200".

    ![Captura de tela que mostra a página "Política de Acesso" com a ação "Editar Política de Acesso para o Perfil KerbApp200" selecionada.](./media/kerbf5-tutorial/configure32.png)

    ![Captura de tela que mostra a página "Política de Acesso" e a caixa de diálogo "SP de Autenticação SAML".](./media/kerbf5-tutorial/configure33.png)

    ![Captura de tela que mostra a página "Política de Acesso" e a caixa de diálogo "Atribuição de Variável" com as caixas de texto "Atribuição" realçadas.](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain TEXT superdemo.live**

        ![Captura de tela que mostra a página "Política de Acesso" e a caixa de diálogo "Active Directory" com a caixa de texto "SearchFilter" realçada.](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![Captura de tela que mostra a página "Política de Acesso" com a caixa de diálogo "Consulta do AD – Regras de Branch".](./media/kerbf5-tutorial/configure36.png)

        ![Captura de tela que mostra as caixas de texto "Variável Personalizada" e "Expressão Personalizada" realçadas.](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![Captura de tela que mostra a caixa de texto "Nome de Usuário da Página de Logon" realçada.](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. Para adicionar um novo nó, navegue até **Tráfego Local > Nós > Lista de Nós, clique em Criar**, preencha as informações a seguir e clique em **Concluído**.

    * Nome: KerbApp200
    * Descrição: KerbApp200
    * Endereço: 192.168.20.200

        ![Captura de tela que mostra a página "Novo Nó" com as caixas de texto "Nome", "Descrição" e "Endereço" realçadas e o botão "Concluído" selecionado.](./media/kerbf5-tutorial/configure39.png)

1. Para criar um pool, navegue até **Tráfego Local > Pools > Lista de Pools, clique em Criar**, preencha as seguintes informações e clique em **Concluído**.

    * Nome: KerbApp200-Pool
    * Descrição: KerbApp200-Pool
    * Monitores de Integridade: http
    * Endereço: 192.168.20.200
    * Porta de serviço: 81

        ![Captura de tela que mostra a página "Novo Pool" com valores inseridos e o botão "Concluído" selecionado.](./media/kerbf5-tutorial/configure40.png)

1. Para criar um Servidor Virtual, navegue até **Tráfego Local > Servidores Virtuais > Lista de Servidores Virtuais > +** , preencha as seguintes informações e clique em **Concluído**.

    * Nome: KerbApp200
    * Endereço de Destino/Máscara: Host 192.168.30.200
    * Porta de serviço: Porta 443 HTTPS
    * Perfil de Acesso: KerbApp200
    * Especifique o Perfil de Acesso criado na etapa anterior

        ![Captura de tela que mostra a página "Lista de Servidores Virtuais" com as caixas de texto "Nome", "Endereço/Máscara de Destino" e "Porta de Serviço" realçadas.](./media/kerbf5-tutorial/configure41.png)

        ![Captura de tela que mostra a página "Lista de Servidores Virtuais" com a lista suspensa "Perfil de Acesso" realçada.](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Configurar Delegação do Kerberos 

>[!NOTE]
>Para referência, clique [aqui](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **Etapa 1:** Criar uma Conta de Delegação

    **Exemplo:**
    * Nome de Domínio: **superdemo.live**

    * Nome de Conta SAM: **big-ipuser**

    * New-ADUser -Name "Conta de Delegação APM" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **Etapa 2:** Definir SPN (na Conta de Delegação APM)

    **Exemplo:**
    * setspn –A **host/big-ipuser.superdemo.live** big-ipuser

* **Etapa 3:** Delegação de SPN (para a Conta do Serviço de Aplicativo) Instale a delegação adequada para a Conta de Delegação da F5.
    No exemplo abaixo, a conta de Delegação APM está sendo configurada para KCD para o aplicativo FRP-App1.superdemo.live

    ![Configuração do F5 (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Forneça os detalhes conforme mencionado no documento de referência acima [aqui](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html).

### <a name="create-f5-test-user"></a>Criar usuário de teste do F5

Nesta seção, você criará um usuário chamado B.Fernandes no F5. Trabalhe com a [equipe de suporte ao cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os usuários na plataforma do F5. Os usuários devem ser criados e ativados antes de usar o logon único. 

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do F5 no Painel de Acesso, você deverá ser conectado automaticamente ao F5 no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o F5 com o Azure AD](https://aad.portal.azure.com/)

- [Configurar logon único do F5 para aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar logon único do F5 para aplicativo Kerberos Avançado](advance-kerbf5-tutorial.md)

- [Integração do F5 BIG-IP APM e do Azure AD para acesso híbrido seguro](../manage-apps/f5-aad-integration.md)

- [Tutorial para implantar a VM do F5 BIG-IP Virtual Edition na IaaS do Azure para acesso híbrido seguro](../manage-apps/f5-bigip-deployment-guide.md)

- [Tutorial para a integração de logon único do Azure Active Directory ao F5 BIG-IP para VPN sem senha](../manage-apps/f5-aad-password-less-vpn.md)