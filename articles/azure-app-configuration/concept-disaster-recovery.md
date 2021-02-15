---
title: Resiliência de configuração Azure App e recuperação de desastre
description: Saiba como implementar resiliência e recuperação de desastre com a configuração de Azure App.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 7910e2092259081aade799fc662052e5a1375e25
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930476"
---
# <a name="resiliency-and-disaster-recovery"></a>Resiliência e recuperação de desastre

Atualmente, a Configuração de Aplicativos do Azure é um serviço regional. Cada repositório de configuração é criado em uma determinada região do Azure. Uma interrupção em toda a região afetará todos os repositórios dessa região. A Configuração de Aplicativos não oferece failover automático para outra região. Este artigo fornece orientações gerais sobre como você pode usar vários repositórios de configuração nas regiões do Azure para aumentar a resiliência geográfica de seu aplicativo.

## <a name="high-availability-architecture"></a>Arquitetura de alta disponibilidade

Para obter redundância entre regiões, crie vários repositórios da Configuração de Aplicativos em regiões diferentes. Com essa configuração, seu aplicativo terá, pelo menos, um repositório de configurações adicional para fallback caso o repositório primário fique inacessível. O seguinte diagrama ilustra a topologia entre o aplicativo e seus repositórios de configurações primário e secundário:

![Repositórios com redundância geográfica](./media/geo-redundant-app-configuration-stores.png)

O aplicativo carrega sua configuração dos repositórios primário e secundário em paralelo. Essa ação aumenta a chance de obter dados de configuração com êxito. Você é responsável por manter os dados em ambos os repositórios sincronizados. As seções a seguir explicam como você pode criar resiliência geográfica em seu aplicativo.

## <a name="failover-between-configuration-stores"></a>Failover entre os repositórios de configuração

Tecnicamente, o aplicativo não está executando um failover. Ele está tentando recuperar o mesmo conjunto de dados de configuração de dois repositórios da Configuração de Aplicativos simultaneamente. Organize seu código de modo que ele seja carregado primeiro do repositório secundário e depois do repositório primário. Essa abordagem garante que os dados de configuração do repositório primário tenham precedência sempre que estiverem disponíveis. O trecho de código a seguir mostra como você pode implementar essa disposição no .NET Core:

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Observe o parâmetro `optional` passado para a função `AddAzureAppConfiguration`. Quando definido como `true`, esse parâmetro impede que a falha do aplicativo continue se a função não pode carregar os dados de configuração.

## <a name="synchronization-between-configuration-stores"></a>Sincronização entre os repositórios de configuração

É importante que os repositórios de configurações com redundância geográfica tenham todos o mesmo conjunto de dados. Há duas maneiras de fazer isso:

### <a name="backup-manually-using-the-export-function"></a>Fazer backup manualmente usando a função exportar

Use a função **Exportar** da Configuração de Aplicativos para copiar os dados do repositório primário para o secundário sob demanda. Essa função está disponível no portal e na CLI do Azure.

No portal do Azure, efetue push de uma alteração para outro repositório de configurações seguindo estas etapas.

1. Acesse a guia **Importação/Exportação** e, em seguida, selecione **Exportar** > **Configuração de Aplicativos** > **Destino** > **Selecionar um recurso**.

1. Na nova folha que é aberta, especifique a assinatura, o grupo de recursos e o nome do recurso do armazenamento secundário e, em seguida, selecione **aplicar**.

1. A interface do usuário será atualizada para que você possa escolher quais dados de configuração deseja exportar para o repositório secundário. Você pode deixar o valor de hora padrão como está e definir ambos **de rótulo** e **rótulo** com o mesmo valor. Selecione **Aplicar**. Repita isso para todos os rótulos em seu repositório primário.

1. Repita as etapas anteriores sempre que a configuração for alterada.

O processo de exportação também pode ser obtido usando o CLI do Azure. O comando a seguir mostra como exportar todas as configurações do repositório primário para o secundário:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --dest-name {SecondaryStore} --label * --preserve-labels -y
```

### <a name="backup-automatically-using-azure-functions"></a>Fazer backup automaticamente usando Azure Functions

O processo de backup pode ser automatizado usando Azure Functions. Ele aproveita a integração com a grade de eventos do Azure na configuração do aplicativo. Uma vez configurado, a configuração do aplicativo publicará eventos na grade de eventos para quaisquer alterações feitas nos valores de chave em um repositório de configuração. Assim, um aplicativo Azure Functions pode escutar esses eventos e fazer backup dos dados de acordo. Para obter detalhes, consulte o tutorial sobre [como fazer backup de repositórios de configuração de aplicativo automaticamente](./howto-backup-config-store.md).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como aumentar seu aplicativo para obter a resiliência geográfica durante o runtime da Configuração de Aplicativos. Você também pode inserir dados de configuração da Configuração de Aplicativos no momento do build ou da implantação. Para saber mais, confira [Integrar com o pipeline de CI/CD](./integrate-ci-cd-pipeline.md).
