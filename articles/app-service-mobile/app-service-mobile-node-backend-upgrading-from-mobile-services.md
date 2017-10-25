---
title: "Atualização dos Serviços Móveis para o Serviço de Aplicativo do Azure - Node.js"
description: "Saiba como atualizar com facilidade seu aplicativo de Serviços Móveis para um Aplicativo Móvel do Serviço de Aplicativo"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: yochayk
editor: 
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 5fc61fed674f0d2fc64bc29c064e7e872b4f2e68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Atualizar seu Serviço Móvel do Azure Node.js para o Serviço de Aplicativo
Os Aplicativos Móveis do Serviço de Aplicativo é uma nova maneira de compilar aplicativos móveis usando o Microsoft Azure. Para saber mais, confira [O que são Aplicativos Móveis?].

Este tópico descreve como atualizar um aplicativo de back-end Node.js dos Serviços Móveis do Azure para novos Aplicativos Móveis do Serviço de Aplicativo. Durante essa atualização, o seu aplicativo de Serviços Móveis pode continuar sendo executado.  Se você precisar atualizar um aplicativo back-end em Node.js, consulte [Atualizar seus Serviços Móveis do .NET](app-service-mobile-net-upgrading-from-mobile-services.md).

Quando um back-end móvel é atualizado para o Serviço de Aplicativo do Azure, ele tem acesso a todos os recursos do Serviço de Aplicativo e é cobrado de acordo com os [preços do Serviço de Aplicativo]e não com os preços dos Serviços Móveis.

## <a name="migrate-vs-upgrade"></a>Migração vs. atualização
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Recomendamos a [execução de uma migração](app-service-mobile-migrating-from-mobile-services.md) antes de executar uma atualização. Dessa forma, você pode colocar as duas versões de seu aplicativo no mesmo Plano de Serviço de Aplicativo sem qualquer custo adicional.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Aprimoramentos no SDK do servidor Node.js de Aplicativos Móveis
A atualização para o novo [SDK de Aplicativos Móveis](https://www.npmjs.com/package/azure-mobile-apps) fornece muitos aprimoramentos, incluindo:

* Com base na [estrutura do Express](http://expressjs.com/en/index.html), o novo SDK do Node é leve e foi projetado para acompanhar as novas versões do Node, conforme elas forem surgindo. Você pode personalizar o comportamento do aplicativo com o middleware do Express.
* Aprimoramentos consideráveis de desempenho em comparação com o SDK dos Serviços Móveis.
* Agora você pode hospedar um site junto com o back-end móvel. Da mesma forma, é fácil adicionar o Azure Mobile SDK a qualquer aplicativo express.v4 existente.
* Criado para o desenvolvimento local e de plataforma cruzada, o SDK dos Aplicativos Móveis pode ser desenvolvido e executado localmente nas plataformas do Windows, do Linux e do OSX. Agora é fácil usar técnicas comuns de desenvolvimento em Node, como a execução de testes [Mocha](https://mochajs.org/) antes da implantação.

## <a name="overview"></a>Visão geral da atualização básica
Para ajudar na atualização de um back-end em Node.js, o Serviço de Aplicativo do Azure forneceu um pacote de compatibilidade.  Após a atualização, você terá um site novo que pode ser implantado em um novo site de Serviço de Aplicativo.

Os SDKs do cliente de Serviços Móveis **não** são compatíveis com o novo SDK do servidor de Aplicativos Móveis. Para permitir a continuidade do serviço de seu aplicativo, não publique alterações em um site que esteja servindo clientes publicados. Em vez disso, você deve criar um novo aplicativo móvel que sirva como uma duplicata. Você pode colocar esse aplicativo no mesmo plano de Serviço de Aplicativo para evitar custos adicionais.

Assim, você terá duas versões do aplicativo: uma que permanece inalterada e atende aos aplicativos publicados e outra que você pode atualizar e usar como uma nova versão do cliente. Você pode mover e testar seu código de acordo com seu ritmo, mas deve aplicar as correções de bug feitas às duas versões. Quando você achar que um número desejado de aplicativos cliente foram atualizados para a versão mais recente, será possível excluir o aplicativo migrado original se assim quiser. Isso não causará custos adicionais, se for hospedado no mesmo plano de Serviço de Aplicativo de seu Aplicativo Móvel.

A estrutura completa para o processo de atualização é a seguinte:

1. Baixar seu Serviço Móvel do Azure (migrado) atual.
2. Converter o projeto em um Aplicativo Móvel do Azure usando o pacote de compatibilidade.
3. Corrigir quaisquer diferenças (como configurações de autenticação).
4. Implantar seu projeto de Aplicativo Móvel do Azure convertido em um novo Serviço de Aplicativo.
5. Lançar uma nova versão do aplicativo cliente que use o novo Aplicativo Móvel.
6. (Opcional) Excluir seu aplicativo de serviço móvel migrado original.

A exclusão pode ocorrer quando você não vê nenhum tráfego no serviço móvel migrado original.

## <a name="install-npm-package"></a> Instalar os pré-requisitos
Você deve instalar o [Node] em seu computador local.  Você também deve instalar o pacote de compatibilidade.  Após a instalação do Node, execute o seguinte comando em um prompt do PowerShell ou o cmd novo:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Obter os scripts dos Serviços Móveis do Azure
* Faça logon no [Portal do Azure].
* Usando **Todos os Recursos** ou **Serviços de Aplicativos**, encontre seu site de Serviços Móveis.
* Dentro do site, clique em **Ferramentas** -> **Kudu** -> **Ir** para abrir o site do Kudu.
* Clique em **Console de Depuração** -> **PowerShell** para abrir o console de Depuração.
* Navegue até `site/wwwroot/App_Data/config` clicando em cada diretório, um de cada vez
* Clique no ícone de download ao lado do diretório `scripts` .

Isso baixará os scripts em formato ZIP.  Crie um novo diretório no computador local e descompacte o arquivo `scripts.ZIP` dentro do diretório.  Isso criará um diretório `scripts` .

## <a name="scaffold-app"></a> Faça scaffold do novo back-end de Aplicativos Móveis do Azure
Execute o seguinte comando no diretório que contém o diretório de scripts:

```scaffold-mobile-app scripts out```

Isso criará um back-end dos Aplicativos Móveis do Azure com scaffold no diretório `out` .  Embora não seja necessário, convém verificar o diretório `out` em um repositório de código-fonte de sua escolha.

## <a name="deploy-ama-app"></a> Implantar seu back-end de Aplicativos Móveis do Azure
Durante a implantação, você precisará fazer o seguinte:

1. Criar um novo Aplicativo Móvel no [Portal do Azure].
2. Executar o script `createViews.sql` em seu banco de dados conectado.
3. Vincular o banco de dados que está vinculado a seu Serviço Móvel ao seu novo Serviço de Aplicativo.
4. Vincule quaisquer outros recursos (como os Hubs de Notificação) ao novo Serviço de Aplicativo.
5. Implante o código gerado em seu novo site.

### <a name="create-a-new-mobile-app"></a>Criar um novo Aplicativo Móvel
1. Faça logon no [Portal do Azure].
2. Clique em **+NOVO** > **Web + Móvel** > **Aplicativo Móvel**, então forneça um nome para o back-end do Aplicativo Móvel.
3. Para o **Grupo de Recursos**, selecione um grupo de recursos existente ou crie um novo (usando o mesmo nome que o aplicativo).

    Você pode selecionar outro plano de Serviço de Aplicativo ou criar um novo. Para saber mais sobre planos de Serviços de Aplicativos e como criar um novo plano em um tipo de preço e em seu local desejado, confira [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. Para o **plano de Serviço de Aplicativo**, o plano padrão (na [camada Standard](https://azure.microsoft.com/pricing/details/app-service/)) está selecionado. Você também pode selecionar um plano diferente ou [criar um novo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). As configurações de plano de Serviço de Aplicativo determinam o [local, os recursos, o custo e os recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados ao seu aplicativo.

    Depois de decidir o plano, clique em **Criar**. Isso cria o back-end de Aplicativo Móvel.

### <a name="run-createviewssql"></a>Executar CreateViews.SQL
O aplicativo com scaffold contém um arquivo chamado `createViews.sql`.  Esse script deve ser executado no banco de dados de destino.  A cadeia de conexão para o banco de dados de destino pode ser obtida em seu serviço móvel migrado, na folha **Configurações** em **Cadeias de Conexão**.  Ela é chamada de `MS_TableConnectionString`.

Você pode executar esse script no SQL Server Management Studio ou no Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Vincular o banco de dados ao Serviço de Aplicativo
Vincule o banco de dados existente ao Serviço de Aplicativo:

* No [Portal do Azure], abra seu Serviço de Aplicativo.
* Selecione **Todas as Configurações** -> **Conexões de Dados**.
* Clique em **+ Adicionar**.
* Na lista suspensa, selecione **Banco de Dados SQL**
* Em **Banco de Dados SQL**, selecione o banco de dados existente, clique em **Selecionar**.
* Em **Cadeia de conexão**, insira o nome de usuário e a senha para o banco de dados e clique em **OK**.
* Na folha **Adicionar conexões de dados**, clique em **OK**.

O nome de usuário e senha podem ser encontrados na Cadeia de Conexão do banco de dados de destino em seu Serviço Móvel migrado.

### <a name="set-up-authentication"></a>Configurar a autenticação
Os Aplicativos Móveis do Azure permitem que você configure a autenticação no Azure Active Directory, Facebook, Google, Twitter e Microsoft dentro do serviço.  A autenticação personalizada precisará ser desenvolvida separadamente.  Consulte a documentação [Conceitos de Autenticação] e [Início Rápido da Autenticação] para obter mais informações.  

## <a name="updating-clients"></a>Atualizar clientes móveis
Quando você tiver um back-end do Aplicativo Móvel operacional, poderá trabalhar em uma nova versão de seu aplicativo cliente para consumi-lo. Os Aplicativos Móveis também incluem uma nova versão dos SDKs do cliente e, assim como a atualização do servidor acima, você precisará remover todas as referências aos SDKs dos Serviços Móveis antes de instalar as versões dos Aplicativos Móveis.

Uma das principais alterações entre as versões é que os construtores não exigem mais uma chave de aplicativo.
Agora, basta passar a URL do Aplicativo Móvel. Por exemplo, em clientes .NET, o construtor `MobileServiceClient` agora é:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Você pode ler sobre como instalar novos SDKs e usar a nova estrutura nos links abaixo:

* [Android versão 2.2 ou posterior](app-service-mobile-android-how-to-use-client-library.md)
* [iOS versão 3.0.0 ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova versão 2.0 ou posterior](app-service-mobile-cordova-how-to-use-client-library.md)

Se o seu aplicativo usa notificações por push, anote as instruções de registro específicas para cada plataforma, pois houveram algumas alterações também.

Quando a nova versão do cliente estiver pronta, faça um teste em seu projeto de servidor atualizado. Após validar o funcionamento, libere uma nova versão de seu aplicativo para os clientes. Eventualmente, depois que seus clientes tiverem a oportunidade de receber essas atualizações, você poderá excluir a versão de Serviços Móveis do seu aplicativo. Neste ponto, você atualizou completamente para um aplicativo móvel de serviço de aplicativo usando o servidor de aplicativos móveis SDK mais recente.

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[O que são Aplicativos Móveis?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[preços do Serviço de Aplicativo]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Conceitos de Autenticação]: ../app-service/app-service-authentication-overview.md
[Início Rápido da Autenticação]: app-service-mobile-auth.md

[Portal do Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
