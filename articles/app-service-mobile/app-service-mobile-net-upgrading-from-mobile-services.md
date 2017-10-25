---
title: "Atualização de Serviços Móveis para o serviço de aplicativo do Azure"
description: "Saiba como atualizar com facilidade seu aplicativo de Serviços Móveis para um Aplicativo Móvel do Serviço de Aplicativo"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 81c8ba6245565368eab4cdaca297ff7656180605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Atualizar o Serviço Móvel do Azure .NET existente para o Serviço de Aplicativo
O Aplicativo Móvel do Serviço de Aplicativo é uma nova maneira de compilar aplicativos móveis usando o Microsoft Azure. Para saber mais, confira [O que são os Aplicativos Móveis?].

Este tópico descreve como atualizar um aplicativo de back-end do .NET usando os Serviços Móveis do Azure para novos Aplicativos Móveis do Serviço de Aplicativo. Durante essa atualização, o seu aplicativo de Serviços Móveis pode continuar sendo executado.   Se você precisar atualizar um aplicativo back-end em Node.js, veja [Atualizar seus Serviços Móveis do Node.js](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Quando um back-end móvel é atualizado para o Serviço de Aplicativo do Azure, ele tem acesso a todos os recursos do Serviço de Aplicativo e é cobrado de acordo com os [preços do Serviço de Aplicativo]e não com os preços dos Serviços Móveis.

## <a name="migrate-vs-upgrade"></a>Migração vs. atualização
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Recomendamos a [execução de uma migração](app-service-mobile-migrating-from-mobile-services.md) antes de executar uma atualização. Dessa forma, você pode colocar as duas versões de seu aplicativo no mesmo Plano do Serviço de Aplicativo sem qualquer custo adicional.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Aprimoramentos no SDK do servidor .NET de aplicativos móveis
A atualização para os novos [SDKs de Aplicativos Móveis](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) oferece os seguintes benefícios:

* Mais flexibilidade em dependências do NuGet. O ambiente de hospedagem não fornece mais suas próprias versões de pacotes do NuGet, então você pode usar versões compatíveis alternativas. No entanto, se há novas correções de bugs críticas ou atualizações de segurança para o Mobile Server SDK ou dependências, você deve atualizar manualmente o serviço.
* Mais flexibilidade no SDK do celular. Você pode controlar explicitamente quais recursos e rotas são configurados, como autenticação, APIs de tabela e o ponto de extremidade de registro push. Para saber mais, confira [Como usar o SDK do servidor do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Suporte para outros tipos de projeto do ASP.NET e rotas. Agora você pode hospedar os controladores MVC e API da Web no mesmo projeto como o projeto móvel de back-end.
* Suporte para novos recursos de autenticação do Serviço de Aplicativo, que permitem que você use uma configuração de autenticação comum em seus aplicativos Web e móveis.

## <a name="overview"></a>Visão geral da atualização básica
Em muitos casos, migrar será tão simples quanto alternar para o novo SDK do servidor de Aplicativos Móveis e republicar seu código para uma nova instância de Aplicativo Móvel. Há, no entanto, alguns cenários que exigem alguma configuração adicional, como cenários avançados de autenticação e trabalhar com trabalhos agendados. Cada um deles será abordado em seções posteriores.

> [!TIP]
> Recomendamos a leitura e compreensão do restante deste tópico antes de iniciar uma atualização. Tome nota de todos os recursos que você usar que são descritos abaixo.
>
>

Os SDKs do cliente de Serviços Móveis **não** são compatíveis com o novo SDK do servidor de Aplicativos Móveis. Para permitir a continuidade do serviço de seu aplicativo, não publique alterações em um site que esteja servindo clientes publicados. Em vez disso, você deve criar um novo aplicativo móvel que sirva como uma duplicata. Você pode colocar esse aplicativo no mesmo plano do Serviço de Aplicativo para evitar custos adicionais.

Assim, você terá duas versões do aplicativo: uma que permanece inalterada e atende aos aplicativos publicados e outra que você pode atualizar e usar como uma nova versão do cliente. Você pode mover e testar seu código de acordo com seu ritmo, mas deve aplicar as correções de bug feitas às duas versões. Quando você achar que um número desejado de aplicativos cliente foram atualizados para a versão mais recente, será possível excluir o aplicativo migrado original, se assim quiser.

A estrutura completa para o processo de atualização é a seguinte:

1. Criar um novo Aplicativo Móvel
2. Atualizar o projeto para usar os novos SDKs de Servidor
3. Lançar uma nova versão do aplicativo cliente
4. (Opcional) Excluir sua instância original migrada

## <a name="mobile-app-version"></a>Criando uma segunda instância do aplicativo
A primeira etapa de atualização é criar o recurso de Aplicativo Móvel que hospedará a nova versão do seu aplicativo. Se você já tiver migrado um serviço móvel existente, convém criar esta versão no mesmo plano de hospedagem. Abra o [Portal do Azure] e navegue até o aplicativo migrado. Anote o Plano de Serviço de Aplicativo no qual ele está sendo executado.

Em seguida, crie a segunda instância de aplicativo seguindo as [instruções de criação de back-end do .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Quando receber uma solicitação para escolher o Plano de Serviço de Aplicativo ou o "plano de hospedagem", escolha o plano de seu aplicativo migrado.

Você provavelmente desejará usar o mesmo banco de dados e Hub de Notificação, como você fez nos Serviços Móveis. Você pode copiar esses valores abrindo o [Portal do Azure], navegando até o aplicativo original e clicando em **Configurações** > **Configurações do aplicativo**. Em **Cadeias de Conexão**, copie `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Navegue até o novo site de atualização e cole-as, substituindo os valores existentes. Repita esse processo para quaisquer outras configurações de aplicativo exigidas por seu aplicativo. Se você não estiver usando um serviço migrado, leia as cadeias de conexão e as configurações de aplicativo da guia **Configurar** da seção Serviços Móveis do [portal clássico do Azure].

Faça uma cópia do projeto do aplicativo ASP.NET e publique-o em seu novo site. Usando uma cópia do seu aplicativo cliente atualizado com a nova URL, confirme que tudo funciona como o esperado.

## <a name="updating-the-server-project"></a>Atualizando o projeto de servidor
Os aplicativos móveis fornecem um novo [SDK do Servidor de Aplicativos Móveis] , que oferece praticamente a mesma funcionalidade que o tempo de execução de Serviços Móveis. Primeiro, você deve remover todas as referências aos pacotes de Serviços Móveis. No gerenciador de pacotes NuGet, pesquise por `WindowsAzure.MobileServices.Backend` A maioria dos aplicativos exibirá vários pacotes aqui, incluindo `WindowsAzure.MobileServices.Backend.Tables` e `WindowsAzure.MobileServices.Backend.Entity`. Nesse caso, inicie com o pacote mais baixo na árvore de dependência, como `Entity`, e remova-o Quando solicitado, não remova todos os pacotes dependentes. Repita este processo até remover o próprio `WindowsAzure.MobileServices.Backend`.

Neste momento, você terá um projeto que não faz referência a SDKs de Serviços Móveis.

Em seguida, você adicionará referências ao SDK dos Aplicativos Móveis. Para essa migração, a maioria dos desenvolvedores deseja baixar e instalar o pacote `Microsoft.Azure.Mobile.Server.Quickstart` , pois ele vai receber todo o conjunto necessário.

Haverá alguns erros de compilador resultantes de diferenças entre os SDKs, mas eles são fáceis de resolver e serão abordados posteriormente, ainda nesta seção.

### <a name="base-configuration"></a>Configuração base
Em seguida, em WebApiConfig.cs, você pode substituir:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

por:

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

> [!NOTE]
> Se desejar saber mais sobre o novo SDK de servidor .NET e como adicionar ou remover recursos do seu aplicativo, confira o tópico [Como usar o SDK do servidor .NET] .
>
>

Se o seu aplicativo usar os recursos de autenticação, você também precisará registrar um middleware OWIN. Nesse caso, você deve mover o código de configuração acima para uma nova classe de inicialização do OWIN.

1. Adicione o pacote NuGet `Microsoft.Owin.Host.SystemWeb` se já não estiver incluído em seu projeto.
2. No Visual Studio, clique com o botão direito do mouse em seu projeto e selecione **Adicionar** -> **Novo Item**. Selecione **Web** -> **Geral** -> **Classe de inicialização OWIN**.
3. Mova o código acima relativo a MobileAppConfiguration de `WebApiConfig.Register()` para o método `Configuration()` de sua nova classe de inicialização.

Verifique se o método `Configuration()` termina em:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Existem outras alterações relacionadas à autenticação que são abordadas na seção completa sobre autenticação abaixo.

### <a name="working-with-data"></a>Trabalhando com dados
Nos Serviços Móveis, o nome do aplicativo móvel serviu como nome do esquema padrão na configuração do Entity Framework.

Para garantir que você tenha o mesmo esquema referenciado anteriormente, use o seguinte a fim de definir o esquema para seu aplicativo no DbContext:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Verifique se você tem o MS_MobileServiceName definido ao executar as ações acima. Você pode fornecer outro nome de esquema se o seu aplicativo o tiver personalizado anteriormente.

### <a name="system-properties"></a>Propriedades do Sistema
#### <a name="naming"></a>Nomenclatura
No SDK do servidor dos Serviços Móveis do Azure, as propriedades do sistema sempre contêm um prefixo sublinhado duplo (`__`) para as propriedades:

* __createdAt
* __updatedAt
* __deleted
* __version

Os SDKs do cliente de Serviços Móveis têm uma lógica especial para analisar as propriedades do sistema nesse formato.

Nos Aplicativos Móveis do Azure, as propriedades do sistema não têm um formato especial e têm os seguintes nomes:

* createdAt
* updatedAt
* deleted
* version

Os SDKs de cliente de Aplicativos Móveis usam novos nomes de propriedades do sistema; portanto, não é necessário alterar o código do cliente. No entanto, se você estiver fazendo chamadas REST diretamente para seu serviço, deverá alterar suas consultas adequadamente.

#### <a name="local-store"></a>Armazenamento local
As alterações dos nomes das propriedades do sistema significam que um banco de dados local de sincronização offline para os Serviços Móveis não é compatível com os Aplicativos Móveis. Se possível, você deve evitar a atualização de aplicativos clientes dos Serviços Móveis para Aplicativos Móveis até que as alterações pendentes sejam enviadas ao servidor. Depois disso, o aplicativo atualizado deverá usar um novo nome de arquivo de banco de dados.

Se um aplicativo cliente for atualizado de Serviços Móveis para Aplicativos Móveis enquanto houver alterações offline pendentes na fila de operação, o banco de dados do sistema deverá ser atualizado para usar os novos nomes de coluna. No iOS, isso pode ser obtido usando migrações leves para alterar os nomes de coluna. No Android e no cliente gerenciado do .NET, você deve escrever um SQL personalizado para renomear as colunas das tabelas de objeto de dados.

No iOS, você deve alterar seu esquema de dados básicos para suas entidades de dados para que correspondam ao seguinte. Observe que as propriedades `createdAt`, `updatedAt` e `version` não têm mais um prefixo `ms_`:

| Atributo | Tipo | Observação |
| --- | --- | --- |
| ID |Cadeia de caracteres, marcadas como obrigatórias |chave primária no repositório remoto |
| createdAt |Data |(opcional) é mapeado para a propriedade do sistema createdAt |
| updatedAt |Data |(opcional) é mapeado para a propriedade do sistema updatedAt |
| version |Cadeia de caracteres |(opcional) usado para detectar conflitos, é mapeado para version |

#### <a name="querying-system-properties"></a>Consultando propriedades do sistema
Nos Serviços Móveis do Azure, as propriedades do sistema não são enviadas por padrão, mas somente quando são solicitadas usando a cadeia de caracteres de consulta `__systemProperties`. Em contraste, no sistema de Aplicativos Móveis do Azure, as propriedades estão **sempre selecionadas** , pois fazem parte do modelo de objeto do SDK do servidor.

Essa alteração afeta principalmente as implementações personalizadas dos gerenciadores de domínio, como extensões de `MappedEntityDomainManager`. Nos Serviços Móveis, se um cliente nunca solicitar uma propriedade do sistema, será possível usar um `MappedEntityDomainManager` que não mapeie realmente todas as propriedades. No entanto, nos Aplicativos Móveis do Azure, essas propriedades não mapeadas causarão um erro em consultas GET.

A maneira mais fácil de resolver o problema é modificando os DTOs para que herdem de `ITableData` em vez de `EntityData`. Em seguida, adicione o atributo `[NotMapped]` aos campos que devem ser omitidos.

Por exemplo, os itens abaixo definem `TodoItem` sem propriedades de sistema:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Observação: se você obtiver erros em `NotMapped`, adicione uma referência ao assembly `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Os Serviços Móveis incluíram algum suporte a CORS encapsulando a solução CORS ASP.NET. Essa camada de encapsulamento foi removida para dar ao desenvolvedor mais controle, para que você possa aproveitar diretamente o [suporte ao CORS ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

As principais áreas com que você deve se preocupar ao usar o CORS são os cabeçalhos `eTag` e `Location`, que devem ser permitidos para que os SDKs do cliente funcionem corretamente.

### <a name="push-notifications"></a>Notificações por Push
Para envios por push, o item principal que você pode descobrir estar ausente no SDK do Servidor é a classe PushRegistrationHandler. Registros são tratados de forma ligeiramente diferente em aplicativos móveis, e registros sem marca registros são habilitados por padrão. Gerenciar as marcas pode ser feito por meio de APIs personalizados. Confira as instruções de [registro de marcas](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) para saber mais.

### <a name="scheduled-jobs"></a>Trabalhos agendados
Os trabalhos agendados não são criados em Aplicativos Móveis; portanto, todos os trabalhos existentes que você tem no back-end do .NET precisam ser atualizados individualmente. Uma opção é criar um [Trabalho Web] agendado no site de código do Aplicativo Móvel. Você também pode configurar um controlador que contenha o código de trabalho e configurar o [Agendador do Azure] para atingir o ponto de extremidade conforme o esperado.

### <a name="miscellaneous-changes"></a>Alterações diversas
Todos os ApiControllers que serão consumidos por um cliente móvel agora devem ter o atributo `[MobileAppController]` . Isso não é mais incluído por padrão para que os outros ApiControllers não sejam afetados por formatadores móveis.

O objeto `ApiServices` não faz mais parte do SDK. Para acessar configurações de Aplicativo móvel, você pode usar o seguinte:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Da mesma forma, o log agora é realizado usando a gravação de rastreamento padrão do ASP.NET:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

## <a name="authentication"></a>Considerações sobre autenticação
Os componentes de autenticação dos Serviços Móveis foram movidos para o recurso Autenticação/Autorização do Serviço de Aplicativo. Você pode aprender a habilitar essa opção para seu site lendo o tópico [Adicionar autenticação ao seu aplicativo móvel](app-service-mobile-ios-get-started-users.md) .

Para alguns provedores, como o AAD, o Facebook e o Google, você deverá aproveitar o registro existente em seu aplicativo de cópia. Basta navegar até o portal do provedor de identidade e adicionar uma nova URL de redirecionamento ao registro. Em seguida, configure a Autenticação/Autorização do Serviço de Aplicativo com a ID do cliente e o segredo.

### <a name="controller-action-authorization"></a>Autorização de ação do controlador
Todas as instâncias do atributo `[AuthorizeLevel(AuthorizationLevel.User)]` agora devem ser alterados para usar o atributo ASP.NET padrão `[Authorize]`. Além disso, controladores são agora anônimos por padrão, como em outros aplicativos ASP.NET.
Se você estiver usando uma das outras opções de AuthorizeLevel, como administrador ou aplicativo, observe que eles desapareceram. Agora você pode configurar AuthorizationFilters para segredos compartilhados ou configurar uma entidade de serviço do AAD para permitir chamadas serviço a serviço com segurança.

### <a name="getting-additional-user-information"></a>Obtendo informações adicionais do usuário
Você pode obter informações adicionais do usuário, incluindo tokens de acesso usando o método `GetAppServiceIdentityAsync()` :

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Além disso, se o seu aplicativo tem dependências de IDs de usuário, como o armazenamento deles em um banco de dados, é importante observar que as IDs de usuário entre os Serviços Móveis e Aplicativos Móveis do Serviço de Aplicativo são diferentes. No entanto, você ainda pode obter a ID de usuário dos Serviços Móveis. Todas as subclasses ProviderCredentials têm uma propriedade UserId. Continuando do exemplo anterior:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Se o aplicativo usa todas as dependências das IDs de usuário, é importante aproveitar o mesmo registro com um provedor de identidade, se possível. IDs de usuário normalmente têm o escopo para o registro do aplicativo que foi usado, portanto, introduzir um novo registro pode criar problemas com os dados de usuários correspondentes.

### <a name="custom-authentication"></a>Autenticação personalizada
Se o seu aplicativo está usando uma solução de autenticação personalizada, você deve verificar se o site atualizado tem acesso ao sistema. Siga as novas instruções de autenticação personalizada em [Visão geral do SDK do servidor .NET] para integrar sua solução. Observe que os componentes de autenticação personalizada ainda estão em visualização.

## <a name="updating-clients"></a>Atualizando de clientes
Quando você tiver um back-end do Aplicativo Móvel operacional, poderá trabalhar em uma nova versão de seu aplicativo cliente para consumi-lo. Os Aplicativos Móveis também incluem uma nova versão dos SDKs do cliente e, assim como a atualização do servidor acima, você precisará remover todas as referências aos SDKs dos Serviços Móveis antes de instalar as versões dos Aplicativos Móveis.

Uma das principais alterações entre as versões é que os construtores não exigem mais uma chave de aplicativo. Agora, basta passar a URL do Aplicativo Móvel. Por exemplo, em clientes .NET, o construtor `MobileServiceClient` agora é:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Você pode ler sobre como instalar novos SDKs e usar a nova estrutura nos links abaixo:

* [iOS versão 3.0.0 ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)

Se o seu aplicativo usa notificações por push, anote as instruções de registro específicas para cada plataforma, pois houveram algumas alterações também.

Quando a nova versão do cliente estiver pronta, faça um teste em seu projeto de servidor atualizado. Após validar o funcionamento, libere uma nova versão de seu aplicativo para os clientes. Eventualmente, depois que seus clientes tiverem a oportunidade de receber essas atualizações, você poderá excluir a versão de Serviços Móveis do seu aplicativo. Neste ponto, você atualizou completamente para um aplicativo móvel de serviço de aplicativo usando o servidor de aplicativos móveis SDK mais recente.

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[portal clássico do Azure]: https://manage.windowsazure.com/
[O que são os Aplicativos Móveis?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[SDK do Servidor de Aplicativos Móveis]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Agendador do Azure]: /en-us/documentation/services/scheduler/
[Trabalho Web]: https://github.com/Azure/azure-webjobs-sdk/wiki
[Como usar o SDK do servidor .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[preços do Serviço de Aplicativo]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Visão geral do SDK do servidor .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
