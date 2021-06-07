---
title: Usar configuração por ambiente
titleSuffix: Azure App Configuration
description: Use rótulos para fornecer valores de configuração por ambiente.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: 84286df063994f3def15079cb9b190550d5bd977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929609"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>Usar rótulos para habilitar configurações para ambientes diferentes

Muitos aplicativos precisam usar configurações diferentes para ambientes diferentes. Suponha que um aplicativo tenha um valor de configuração que defina a cadeia de conexão a ser usada para seu banco de dados de back-end. Os desenvolvedores de aplicativos usam um banco de dados diferente daquele usado na produção. A cadeia de conexão de banco de dados usada pelo aplicativo deve mudar conforme o aplicativo passa do desenvolvimento para a produção.

Na Configuração de Aplicativos do Azure, você pode usar *rótulos* para definir valores diferentes para a mesma chave. Por exemplo, você pode definir uma única chave com valores diferentes para desenvolvimento e produção. Você pode especificar qual rótulo carregar ao se conectar à Configuração de Aplicativos.

Para demonstrar essa funcionalidade, você modificará o aplicativo Web criado no [Início Rápido: Crie um aplicativo ASP.NET Core com a Configuração de Aplicativos do Azure](./quickstart-aspnet-core-app.md) para usar diferentes definições de configuração para desenvolvimento versus produção. Conclua o início rápido antes de continuar.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Especificar um rótulo ao adicionar um valor de configuração

No portal do Azure, acesse **Explorador de Configurações** e localize a chave *TestApp:Settings:FontColor* que você criou no guia de início rápido. Selecione seu menu de contexto e, em seguida, selecione **Adicionar Valor**.

> [!div class="mx-imgBorder"]
> ![Adicionar item de menu de valor](media/labels-add-value.png)

Na tela **Adicionar Valor**, insira um **Valor** de **Vermelho** e um **Rótulo** de **Desenvolvimento**. Deixe o **Tipo de conteúdo** vazio. Escolha **Aplicar**.

## <a name="load-configuration-values-with-a-specified-label"></a>Carregar valores de configuração com um rótulo especificado

Por padrão, a Configuração de Aplicativos do Azure carrega apenas valores de configuração sem rótulo. Se você definiu rótulos para seus valores de configuração, convém especificar os rótulos a serem usados ao se conectar à Configuração de Aplicativos.

Na seção anterior, você criou um valor de configuração diferente para o ambiente de desenvolvimento. Você usa a variável `HostingEnvironment.EnvironmentName` para determinar dinamicamente em qual ambiente o aplicativo é executado no momento. Para saber mais, consulte [Usar vários ambientes no ASP.NET Core](/aspnet/core/fundamentals/environments).

Carregue valores de configuração com o rótulo correspondente ao ambiente atual passando o nome do ambiente para o método `Select`:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> O trecho de código anterior carrega a cadeia de conexão da Configuração de Aplicativos de uma variável de ambiente chamada `AppConfigConnectionString`. Verifique se essa variável de ambiente está definida corretamente.

O método `Select` é chamado duas vezes. Na primeira vez, ele carrega valores de configuração sem rótulo. Em seguida, ele carrega valores de configuração com o rótulo correspondente no ambiente atual. Esses valores específicos do ambiente substituem quaisquer valores correspondentes sem rótulo. Você não precisa definir valores específicos do ambiente para cada chave. Se uma chave não tiver um valor com um rótulo correspondente ao ambiente atual, ela usará o valor sem rótulo.

## <a name="test-in-different-environments"></a>Testar em ambientes diferentes

Abra o arquivo `launchSettings.json` no diretório `Properties`. Localize a entrada `config` em `profiles`. Na seção `environmentVariables`, defina a variável `ASPNETCORE_ENVIRONMENT` como `Production`.

Com os novos valores definidos, compile e execute seu aplicativo.

```dotnetcli
dotnet build
dotnet run
```

Use um navegador da Web para acessar `http://localhost:5000`. Você observará que a cor da fonte é preta.

![Aplicativo Web em execução com a configuração de produção](media/labels-website-prod.png)

Atualize `launchSettings.json` para definir a variável `ASPNETCORE_ENVIRONMENT` como `Development`. Execute `dotnet run` novamente. 

Você observará que a cor da fonte agora é vermelha. Isso ocorre porque o aplicativo agora usa o valor de `TestApp:Settings:FontColor` que tem o rótulo `Development`. Todos os outros valores de configuração permanecem os mesmos que seus valores de produção.

![Aplicativo Web em execução com a configuração de desenvolvimento](media/labels-website-dev.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração no ASP.NET Core](/aspnet/core/fundamentals/configuration/)
