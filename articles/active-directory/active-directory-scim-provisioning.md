---
title: "Usar o sistema para gerenciamento de identidade de domínio cruzado provisiona automaticamente usuários e grupos do Azure Active Directory a aplicativos | Microsoft Docs"
description: "O Azure Active Directory pode provisionar automaticamente usuários e grupos a qualquer repositório de identidades ou aplicativos que seja administrado por um serviço Web com a interface definida na especificação do protocolo SCIM."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 4d86f3dc-e2d3-4bde-81a3-4a0e092551c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;oldportal
ms.openlocfilehash: 91978cee88d55c99bcb63c63cdaf01581ae84668
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-system-for-cross-domain-identity-management-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Usar o sistema de gerenciamento de identidade de domínio cruzado para provisionar automaticamente usuários e grupos do Azure Active Directory a aplicativos

## <a name="overview"></a>Visão geral
O Azure Active Directory (Azure AD) pode provisionar automaticamente usuários e grupos para qualquer repositório de identidades ou aplicativos que seja administrado por um serviço Web com a interface definida na [especificação do protocolo Sistema de Gerenciamento de Identidade entre Domínios (SCIM) 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). O Azure Active Directory pode enviar solicitações para criar, modificar ou excluir usuários e grupos atribuídos ao serviço Web. O serviço Web pode então converter essas solicitações em operações no repositório de identidades de destino. 

> [!IMPORTANT]
> A Microsoft recomenda que você gerencie o Azure AD usando o [Centro de administração do AD do Azure](https://aad.portal.azure.com) no portal do Azure em vez de usar o portal clássico do Azure mencionado neste artigo. 



![][0]
*Figura 1: Provisionando do Azure Active Directory a um repositório de identidades por meio de um serviço Web*

Esse recurso pode ser usado juntamente com o recurso "traga seu próprio aplicativo" no Azure AD para habilitar o logon único e o provisionamento automático de usuário de aplicativos que forneçam ou comecem com um serviço Web SCIM.

Há dois casos de uso para utilizar SCIM no Azure Active Directory:

* **Provisionamento de usuários e grupos a aplicativos que dão suporte a SCIM** Aplicativos que dão suporte a SCIM 2.0 e usam tokens de portador OAuth para autenticação funcionam com o Azure AD sem necessidade de configuração.
* **Criar a sua própria solução de provisionamento para aplicativos que dão suporte a outro provisionamento baseado em API** para aplicativos não SCIM, você pode criar um ponto de extremidade SCIM para converter entre o ponto de extremidade SCIM do Azure AD e qualquer API à qual o aplicativo dê suporte para provisionamento de usuários. Para ajudá-lo a desenvolver um ponto de extremidade SCIM, fornecemos bibliotecas Common Language Infrastructure (CLI) com exemplos de código que mostram como fornecer um ponto de extremidade SCIM e converter mensagens SCIM.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Provisionamento de usuários e grupos a aplicativos que dão suporte a SCIM
O Azure AD pode ser configurado para provisionar automaticamente usuários e grupos atribuídos a aplicativos que implementam um serviço Web de [Sistema de Gerenciamento de Identidade entre Domínios 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) e aceitam tokens de portador OAuth para autenticação. Dentro da especificação SCIM 2.0, os aplicativos devem satisfazer estes requisitos:

* Dar suporte à criação de usuários e/ou grupos, de acordo com a seção 3.3 do protocolo SCIM.  
* Dar suporte à modificação de usuários e/ou grupos com solicitações de patch, de acordo com a seção 3.5.2 do protocolo SCIM.  
* Dar suporte à recuperação de um recurso conhecido, de acordo com a seção 3.4.1 do protocolo SCIM.  
* Dar suporte a consultas de usuários e/ou grupos, de acordo com a seção 3.4.2 do protocolo SCIM.  Por padrão, os usuários são consultados por externalId e os grupos são consultados por displayName.  
* Dar suporte a consultas de usuário por ID e pelo gerenciador, de acordo com a seção 3.4.2 do protocolo SCIM.  
* Dar suporte a consultas de grupos por ID e por membro, de acordo com a seção 3.4.2 do protocolo SCIM.  
* Aceitar tokens de portador OAuth para autorização, de acordo com a seção 2.1 do protocolo SCIM.

Verifique com o seu provedor de aplicativo ou na documentação do seu provedor de aplicativo as declarações de compatibilidade com esses requisitos.

### <a name="getting-started"></a>Introdução
Os aplicativos que dão suporte ao perfil SCIM descrito neste artigo podem ser conectados ao Azure Active Directory usando o recurso de "aplicativo não galeria" na galeria de aplicativos do Azure AD. Uma vez conectado, o Azure AD executa um processo de sincronização a cada 20 minutos, em que ele consulta o ponto de extremidade SCIM do aplicativo para os usuários e grupos atribuídos e os cria ou modifica de acordo com os detalhes da atribuição.

**Para conectar um aplicativo que dê suporte a SCIM:**

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Navegue até **Azure Active Directory > Aplicativos empresariais e selecione **Novo aplicativo > Todos > Aplicativo inexistente na galeria**.
3. Insira um nome para seu aplicativo e clique no ícone **Adicionar** para criar um objeto de aplicativo.
    
  ![][1]
  *Figura 2: Galeria de aplicativos do Azure AD*
    
4. Na tela de resultados, selecione a guia **Provisionamento** na coluna esquerda.
5. No menu **Modo de Provisionamento**, selecione **Automático**.
    
  ![][2]
  *Figura 3: Configurar o provisionamento no Portal do Azure*
    
6. No campo **URL do locatário** , insira a URL do ponto de extremidade do SCIM do aplicativo. Exemplo: https://api.contoso.com/scim/v2/
7. Se o ponto de extremidade SCIM exigir um token de portador OAuth de um emissor diferente do Azure AD, copie o token de portador OAuth necessário para o campo opcional **Token Secreto**. Se esse campo estiver em branco, o Azure AD incluiu um token de portador OAuth emitido do Azure AD com cada solicitação. Aplicativos que usam o Azure AD como provedor de identidade podem validar esse token emitido pelo Azure AD.
8. Clique no botão **Testar conectividade** o Azure Active Directory tente se conectar ao ponto de extremidade do SCIM. Se as tentativas falharem, as informações de erro serão exibidas.  
9. Se as tentativas de conexão ao aplicativo forem bem-sucedidas, clique em **Salvar** para salvar as credenciais de administrador.
10. Na seção **Mapeamento**, há dois conjuntos selecionáveis de mapeamentos de atributos: um para objetos de usuário e outro para objetos de grupo. Selecione cada um para revisar os atributos que são sincronizados do Azure Active Directory para seu aplicativo. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre os usuários e os grupos no seu aplicativo para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

    >[!NOTE]
    >Opcionalmente, você pode desabilitar a sincronização dos objetos de grupo desabilitando o mapeamento "grupos". 

11. Em **Configurações**, o campo **Escopo** define quais usuários e/ou grupos são sincronizados. Selecionar "Sincronizar apenas usuários e grupos atribuídos" (recomendado) sincroniza somente usuários e grupos atribuídos na guia **Usuários e grupos**.
12. Depois que a configuração estiver concluída, altere o **Status de provisionamento** para **Ligado**.
13. Clique em **Salvar** para iniciar o serviço de provisionamento do Azure AD. 
14. Caso esteja sincronizando apenas usuários e grupos atribuídos (recomendado), certifique-se de selecionar a guia **Usuários e grupos** e designar os usuários e/ou grupos que você deseja sincronizar.

Depois de iniciada a sincronização inicial, você pode usar a guia **Logs de auditoria** para monitorar o progresso, que mostra todas as ações executadas pelo serviço de provisionamento em seu aplicativo. Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatando o provisionamento automático de conta de usuário](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

>[!NOTE]
>Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 20 minutos, desde que o serviço esteja em execução. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Criando sua própria solução de provisionamento para qualquer aplicativo
Ao criar um serviço Web SCIM que interaja com o Active Directory do Azure, você poderá habilitar o logon único e o provisionamento de usuário automático para praticamente qualquer aplicativo que forneça uma API de provisionamento de usuário SOAP ou REST.

Veja como ele funciona:

1. O AD do Azure fornece uma biblioteca CLI denominada [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Os desenvolvedores e integradores de sistema podem usar essa biblioteca para criar e implantar um ponto de extremidade de serviço Web baseado em SCIM capaz de conectar o AD do Azure ao repositório de identidades de qualquer aplicativo.
2. Os mapeamentos são implementados no serviço Web para mapear o esquema de usuário padronizado para o esquema de usuário e o protocolo exigido pelo aplicativo.
3. A URL do ponto de extremidade é registrada no AD do Azure como parte de um aplicativo personalizado na galeria de aplicativos.
4. Os usuários e grupos são atribuídos a esse aplicativo no AD do Azure. Na atribuição, eles são colocados em uma fila para serem sincronizados com o aplicativo de destino. O processo de sincronização que trata a fila é executado a cada 20 minutos.

### <a name="code-samples"></a>Exemplos de código
Para facilitar esse processo, será fornecido um conjunto de [exemplos de código](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) que cria um ponto de extremidade de serviço Web SCIM e demonstra o provisionamento automático. Um dos exemplos é de um provedor que mantém um arquivo com linhas de valores separados por vírgula representando usuários e grupos.  O outro é de um provedor que opera no serviço de Gerenciamento de Acesso e Identidade do Amazon Web Services.  

**Pré-requisitos**

* Visual Studio 2013 ou posterior.
* [SDK do Azure para .NET](https://azure.microsoft.com/downloads/)
* Computador com Windows que ofereça suporte à estrutura ASP.NET 4.5 a ser usado como o ponto de extremidade SCIM. Esse computador deve poder ser acessado da nuvem
* [Uma assinatura do Azure com uma versão de avaliação ou licenciada do Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* O exemplo do Amazon AWS requer bibliotecas do [Kit de Ferramentas do AWS para Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Para obter mais informações, consulte o arquivo LEIAME incluído no exemplo.

### <a name="getting-started"></a>Introdução
A maneira mais fácil de implementar um ponto de extremidade SCIM que possa aceitar solicitações de provisionamento do AD do Azure é criando e implantando o exemplo de código que gera os usuários provisionados em um arquivo CSV (valores separados por vírgula).

**Para criar um exemplo de ponto de extremidade SCIM:**

1. Baixe o pacote de exemplo de códigos de [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Descompacte o pacote e coloque-o no seu computador com Windows em um local como C:\AzureAD-BYOA-Provisioning-Samples\.
3. Nessa pasta, inicie a solução FileProvisioningAgent no Visual Studio.
4. Selecione **Ferramentas > Gerenciador de Pacotes de Biblioteca > Console do Gerenciador de Pacotes** e execute os seguintes comandos para o projeto FileProvisioningAgent resolver as referências de solução:
  ```` 
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   Install-Package Microsoft.Owin.Diagnostics
   Install-Package Microsoft.Owin.Host.SystemWeb
  ````
5. Compile o projeto FileProvisioningAgent.
6. Inicie o aplicativo Prompt de Comando no Windows (como administrador) e use o comando **cd** para alterar o diretório para a sua pasta **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug**.
7. Execute o seguinte comando, substituindo <ip-address> pelo endereço IP ou pelo nome de domínio do computador com Windows:
  ````   
   FileAgnt.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. No Windows, em **Configurações do Windows > Configurações de Rede e Internet**, selecione o **Firewall do Windows > Configurações Avançadas** e crie uma **Regra de Entrada** que permita acesso de entrada na porta 9000.
9. Se o computador com Windows estiver atrás de um roteador, precisará ser configurado para executar a Network Access Translation entre sua porta 9000 exposta à Internet e a porta 9000 no computador com Windows. Isso é necessário para que o AD do Azure possa acessar esse ponto de extremidade na nuvem.

**Para registrar o exemplo de ponto de extremidade SCIM no AD do Azure:**

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Navegue até **Azure Active Directory > Aplicativos empresariais e selecione **Novo aplicativo > Todos > Aplicativo inexistente na galeria**.
3. Insira um nome para seu aplicativo e clique no ícone **Adicionar** para criar um objeto de aplicativo. O objeto de aplicativo criado destina-se a representar o aplicativo de destino para o qual você estará provisionando e implementando o logon único, e não apenas o ponto de extremidade SCIM.
4. Na tela de resultados, selecione a guia **Provisionamento** na coluna esquerda.
5. No menu **Modo de Provisionamento**, selecione **Automático**.
    
  ![][2]
  *Figura 4: Configurar o provisionamento no Portal do Azure*
    
6. No campo **URL do locatário**, insira a URL exposta à Internet e a porta do seu ponto de extremidade SCIM. Isso seria algo como http://testmachine.contoso.com:9000 or http://<ip-address>:9000/, em que <ip-address> é o endereço IP exposto na Internet.  
7. Se o ponto de extremidade SCIM exigir um token de portador OAuth de um emissor diferente do Azure AD, copie o token de portador OAuth necessário para o campo opcional **Token Secreto**. Se esse campo for deixado em branco, o Azure AD incluirá um token de portador OAuth emitido do Azure AD com cada solicitação. Aplicativos que usam o Azure AD como provedor de identidade podem validar esse token emitido pelo Azure AD.
8. Clique no botão **Testar conectividade** o Azure Active Directory tente se conectar ao ponto de extremidade do SCIM. Se as tentativas falharem, as informações de erro serão exibidas.  
9. Se as tentativas de conexão ao aplicativo forem bem-sucedidas, clique em **Salvar** para salvar as credenciais de administrador.
10. Na seção **Mapeamento**, há dois conjuntos selecionáveis de mapeamentos de atributos: um para objetos de usuário e outro para objetos de grupo. Selecione cada um para revisar os atributos que são sincronizados do Azure Active Directory para seu aplicativo. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre os usuários e os grupos no seu aplicativo para operações de atualização. Selecione o botão Salvar para confirmar as alterações.
11. Em **Configurações**, o campo **Escopo** define quais usuários e/ou grupos são sincronizados. Selecionar "Sincronizar apenas usuários e grupos atribuídos" (recomendado) sincroniza somente usuários e grupos atribuídos na guia **Usuários e grupos**.
12. Depois que a configuração estiver concluída, altere o **Status de provisionamento** para **Ligado**.
13. Clique em **Salvar** para iniciar o serviço de provisionamento do Azure AD. 
14. Caso esteja sincronizando apenas usuários e grupos atribuídos (recomendado), certifique-se de selecionar a guia **Usuários e grupos** e designar os usuários e/ou grupos que você deseja sincronizar.

Depois de iniciada a sincronização inicial, você pode usar a guia **Logs de auditoria** para monitorar o progresso, que mostra todas as ações executadas pelo serviço de provisionamento em seu aplicativo. Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [Relatando o provisionamento automático de conta de usuário](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

A etapa final da verificação do exemplo é abrir o arquivo TargetFile.csv da pasta \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug no seu computador com Windows. Depois que o processo de provisionamento é executado, esse arquivo mostra os detalhes de todos os usuários e grupos provisionados e atribuídos.

### <a name="development-libraries"></a>Bibliotecas de desenvolvimento
Para desenvolver seu próprio serviço Web em conformidade com a especificação do SCIM, em primeiro lugar, familiarize-se com as seguintes bibliotecas fornecidas pela Microsoft, que ajudam a acelerar o processo de desenvolvimento: 

1. As bibliotecas Common Language Infrastructure (CLI) são oferecidas para uso com linguagens que se baseiam na infraestrutura em questão, como C#. Uma dessas bibliotecas, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), declara uma interface, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, mostrada na ilustração a seguir: Um desenvolvedor que usa as bibliotecas implementa essa interface com uma classe que pode ser referenciada, de modo genérico, como provedor. As bibliotecas permitem que o desenvolvedor implante um serviço Web que esteja em conformidade com a especificação de SCIM. O serviço Web pode ser hospedado em Serviços de Informações da Internet ou pode ser qualquer assembly executável de Common Language Infrastructure. A solicitação é convertida em chamadas aos métodos do provedor, que podem ser programadas pelo desenvolvedor para operar em algum repositório de identidades.
  
  ![][3]
  
2. [Manipuladores de ExpressRoute](http://expressjs.com/guide/routing.html) estão disponíveis para análise de objetos de solicitação node.js que representam chamadas (como definido pela especificação do SCIM) feitas para um serviço Web node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Criando um ponto de extremidade SCIM personalizado
Usando as bibliotecas CLI, os desenvolvedores podem hospedar seus serviços em qualquer assembly executável da Common Language Infrastructure ou nos Serviços de Informações da Internet. Veja um exemplo de código para hospedar um serviço em um assembly executável no endereço http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Esse serviço deve ter um endereço HTTP e um certificado de autenticação de servidor do qual a autoridade de certificação raiz é uma destas: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* Verisign
* WoSign

Um certificado de autenticação de servidor pode ser associado a uma porta em um host do Windows usando o utilitário de shell de rede: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Aqui, o valor fornecido para o argumento certhash é a impressão digital do certificado, enquanto o valor fornecido para o argumento appid é um identificador global exclusivo arbitrário.  

Para hospedar o serviço nos Serviços de Informações da Internet, um desenvolvedor cria um assembly de biblioteca de códigos da CLA com uma classe chamada Startup no namespace padrão do assembly.  Veja um exemplo dessa classe: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Manipulando a autenticação do ponto de extremidade
As solicitações do Active Directory do Azure incluem um token de portador do OAuth 2.0.   Qualquer serviço que recebe a solicitação deve autenticar o emissor como sendo o Azure Active Directory em nome do locatário esperado do Azure Active Directory, para acesso ao serviço Web Graph do Azure Active Directory.  No token, o emissor é identificado por uma declaração de iss, como "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Neste exemplo, o endereço base do valor da declaração, https://sts.windows.net, identifica o Azure Active Directory como o emissor, enquanto o segmento do endereço relativo, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, é um identificador exclusivo do locatário do Azure Active Directory em nome do qual o token foi emitido.  Se o token tiver sido emitido para acessar um serviço Web Graph do Azure Active Directory, o identificador desse serviço, 00000002-0000-0000-c000-000000000000, deverá estar no valor da declaração aud do token.  

Os desenvolvedores que usam as bibliotecas CLA fornecidas pela Microsoft para criação de um serviço SCIM podem autenticar solicitações do Azure Active Directory usando o pacote Microsoft.Owin.Security.ActiveDirectory seguindo estas etapas: 

1. Em um provedor, implemente a propriedade Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior fazendo com que ela retorne um método a ser chamado sempre que o serviço é iniciado: 

  ````
    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }
  ````

2. Adicione o código a seguir ao método para que as solicitações a qualquer um dos pontos de extremidade do serviço sejam autenticados como sendo um token emitido pelo Azure Active Directory em nome de um locatário especificado para acesso ao serviço Web Graph do Azure AD: 

  ````
    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }
  ````


## <a name="user-and-group-schema"></a>Esquema de usuários e grupos
O Azure Active Directory pode provisionar dois tipos de recurso aos serviços Web SCIM.  Esses tipos de recurso são usuários e grupos.  

Os recursos de usuário são identificados pelo identificador do esquema, urn:ietf:params:scim:schemas:extension:enterprise:2.0:User, que está incluído nesta especificação de protocolo: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  O mapeamento padrão dos atributos de usuários no Active Directory do Azure para os atributos dos recursos urn:ietf:params:scim:schemas:extension:enterprise:2.0:User é fornecido na tabela 1, abaixo.  

Os recursos de grupo são identificados pelo identificador do esquema, http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  A tabela 2 abaixo mostra o mapeamento padrão de atributos de grupos no Azure Active Directory para os atributos de recursos de http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabela 1: Mapeamento padrão de atributo do usuário
| Usuário do Active Directory do Azure | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User |
| --- | --- |
| IsSoftDeleted |ativo |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |título |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| Serviço Móvel |phoneNumbers[type eq "mobile"].value |
| ID do objeto |ID |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| sobrenome |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabela 2: Mapeamento padrão de atributo do grupo
| Grupo do Active Directory do Azure | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| membros |membros |
| ID do objeto |ID |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="user-provisioning-and-de-provisioning"></a>Provisionamento e desprovisionamento de usuários
A seguinte ilustração mostra as mensagens que o Azure Active Directory envia a um serviço SCIM para gerenciar o ciclo de vida de um usuário em outro repositório de identidades. O diagrama também mostra como um serviço SCIM implementado usando as bibliotecas CLI fornecidas pela Microsoft para a criação de tais serviços converte as solicitações em chamadas para os métodos de um provedor.  

![][4]
*Figura 5: Sequência de provisionamento e desprovisionamento de usuário*

1. O Azure Active Directory consulta o serviço para procurar um usuário com um valor de atributo externalId correspondente ao valor de atributo mailNickname de um usuário no Azure AD. A consulta é expressa como solicitação HTTP como no exemplo, na qual jyoung é o exemplo de um mailNickname de um usuário no Azure Active Directory: 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Se o serviço foi criado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada ao método Query do provedor de serviços.  Veja a assinatura desse método: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Veja a definição da interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }
  ````
  No exemplo a seguir de uma consulta para um usuário com um valor fornecido para o atributo externalId, os valores dos argumentos passados para o método Query são: 
  * parameters.AlternateFilters.Count: 1
  * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
  * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Se a resposta a uma consulta ao serviço Web para procurar um usuário com um valor de atributo externalId que corresponda ao valor de atributo mailNickname de um usuário não retornar nenhum usuário, o Azure Active Directory solicitará que o serviço provisione um usuário correspondente ao usuário no Azure Active Directory.  Veja um exemplo de tal solicitação: 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
  ````
  As bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação dos serviços SCIM convertem essa solicitação em uma chamada ao método Create do provedor de serviços.  O método Create tem essa assinatura: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  Em uma solicitação para provisionar um usuário, o valor do argumento de recurso é uma instância da classe Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser, definida na biblioteca Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Se a solicitação de provisionamento de usuário for bem-sucedida, a implementação do método deverá retornar uma instância da classe Microsoft.SystemForCrossDomainIdentityManagement. Classe Core2EnterpriseUser, com o valor da propriedade Identifier definido como o identificador exclusivo do usuário recentemente provisionado.  

3. Para atualizar um usuário conhecido que existe em um repositório de identidades administrado por um SCIM, o Azure Active Directory prossegue solicitando o estado atual desse usuário no serviço com uma solicitação como: 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Em um serviço criado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada ao método Retrieve do provedor de serviços.  Veja a assinatura do método Retrieve: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
  ````
  No exemplo de uma solicitação para recuperar o estado atual de um usuário, os valores das propriedades do objeto fornecidos como o valor do argumento de parâmetros são: 
  
  * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Se um atributo de referência deve ser atualizado, o Azure Active Directory consulta o serviço para determinar se o valor atual do atributo de referência no repositório de identidades administrado pelo serviço já corresponde ou não ao valor desse atributo no Azure Active Directory. Para usuários, o único atributo no qual o valor atual é consultado dessa forma é o atributo de gerenciador. Veja o exemplo de uma solicitação para determinar se o atributo de gerenciador de um objeto de usuário específico atualmente tem um determinado valor: 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  O valor do parâmetro de consulta de atributos, id, significa que, se existir um objeto de usuário que atenda à expressão fornecida como o valor do parâmetro de consulta de filtro, o serviço deverá responder com um recurso urn:ietf:params:scim:schemas:core:2.0:User ou urn:ietf:params:scim:schemas:extension:enterprise:2.0:User, incluindo apenas o valor do atributo id desse recurso.  O valor do atributo **id** é do conhecimento do solicitante. Ele é incluído no valor do parâmetro de consulta de filtro; a finalidade de solicitá-lo, na verdade, é solicitar uma representação mínima de um recurso que atenda à expressão de filtro como uma indicação da existência ou não de tal objeto.   

  Se o serviço foi criado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada ao método Query do provedor de serviços. O valor das propriedades do objeto fornecido como o valor do argumento de parâmetros é: 
  
  * parameters.AlternateFilters.Count: 2
  * parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
  * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
  * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * parameters.RequestedAttributePaths.ElementAt(0): "id"
  * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Aqui, o valor do índice x pode ser 0 e o valor do índice y pode ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, dependendo da ordem das expressões do parâmetro do filtro de consulta.   

5. Veja um exemplo de uma solicitação do Azure Active Directory a um serviço SCIM para atualizar um usuário: 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  As bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação dos serviços SCIM convertem a solicitação em uma chamada ao método Update do provedor de serviços. Veja a assinatura do método Update: 
  ````
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }
  ````
    No exemplo de uma solicitação para atualizar um usuário, o objeto fornecido como valor do argumento de patch tem estes valores de propriedade: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (PatchRequest as PatchRequest2).Operations.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

6. Para desprovisionar um usuário de um repositório de identidades administrado por um serviço SCIM, o Azure AD envia uma solicitação como esta: 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Se o serviço tiver sido criado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada ao método Delete do provedor de serviços.   Esse método tem esta assinatura: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  O objeto fornecido como valor do argumento resourceIdentifier tem estes valores de propriedade no exemplo de uma solicitação para desprovisionar um usuário: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Provisionamento e desprovisionamento de grupo
A seguinte ilustração mostra as mensagens que o Azure AD envia a um serviço SCIM para gerenciar o ciclo de vida de um grupo em outro repositório de identidades.  Essas mensagens são diferentes das mensagens pertencentes aos usuários em três aspectos: 

* O esquema de um recurso de grupo é identificado como http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  
* As solicitações para recuperar grupos estipulam que o atributo de membros deve ser excluído de qualquer recurso fornecido em resposta à solicitação.  
* As solicitações para determinar se um atributo de referência tem um determinado valor são sobre o atributo de membros.  

![][5]
*Figura 6: sequência de provisionamento e desprovisionamento de grupo*

## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](active-directory-saas-customizing-attribute-mappings.md)
* [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de escopo para provisionamento de usuários](active-directory-saas-scoping-filters.md)
* [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
