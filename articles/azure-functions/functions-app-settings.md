---
title: Referência de configurações de aplicativo para Azure Functions
description: Documentação de referência para as configurações de aplicativo ou variáveis de ambiente do Azure Functions.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: a28530fd4e4731065c4ddcc2f39e9a4660529921
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881916"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência de configurações de aplicativo para Azure Functions

As configurações de aplicativo em um aplicativo de funções contém opções de configuração global que afetam todas as funções desse aplicativo de funções. Quando você executa localmente, essas configurações são acessadas como [variáveis de ambiente](functions-run-local.md#local-settings-file). Este artigo lista as configurações de aplicativo disponíveis nos aplicativos de funções.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Há outras opções de configuração global no arquivo [host.json](functions-host-json.md) e no arquivo [local.settings.json](functions-run-local.md#local-settings-file).

> [!NOTE]  
> Você pode usar as configurações do aplicativo para substituir host.jsem valores de configuração sem precisar alterar o host.jsno próprio arquivo. Isso é útil para cenários em que você precisa configurar ou modificar host.jsespecíficas em configurações para um ambiente específico. Isso também permite que você altere host.jsem configurações sem precisar republicar o projeto. Para saber mais, consulte o [host.jsno artigo de referência](functions-host-json.md#override-hostjson-values).  

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

A chave de instrumentação para Application Insights. Use apenas um `APPINSIGHTS_INSTRUMENTATIONKEY` ou `APPLICATIONINSIGHTS_CONNECTION_STRING` . Quando Application Insights é executado em uma nuvem soberanas, use `APPLICATIONINSIGHTS_CONNECTION_STRING` . Para obter mais informações, consulte [como configurar o monitoramento para Azure Functions](configure-monitoring.md). 

|Chave|Valor de exemplo|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

A cadeia de conexão para Application Insights. Use em `APPLICATIONINSIGHTS_CONNECTION_STRING` vez de `APPINSIGHTS_INSTRUMENTATIONKEY` nos seguintes casos:

+ Quando seu aplicativo de funções requer as personalizações adicionadas com suporte usando a cadeia de conexão. 
+ Quando sua instância de Application Insights é executada em uma nuvem soberanas, que requer um ponto de extremidade personalizado.

Para obter mais informações, consulte [cadeias de conexão](../azure-monitor/app/sdk-connection-string.md). 

|Chave|Valor de exemplo|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey = [chave]; IngestionEndpoint = [url]; LiveEndpoint = [url]; ProfilerEndpoint = [url]; SnapshotEndpoint = [url];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Por padrão, os [proxies do Functions](functions-proxies.md) usam um atalho para enviar chamadas de API de proxies diretamente para funções no mesmo aplicativo de funções. Esse atalho é usado em vez de criar uma nova solicitação HTTP. Essa configuração permite que você desabilite esse comportamento de atalho.

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Chamadas com uma URL de back-end apontando para uma função no aplicativo de função local não serão enviadas diretamente para a função. Em vez disso, as solicitações são direcionadas de volta para o front-end HTTP para o aplicativo de funções.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Chamadas com uma URL de back-end apontando para uma função no aplicativo de funções local são encaminhadas diretamente para a função. Esse é o valor padrão. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Essa configuração controla se os caracteres `%2F` são decodificados como barras em parâmetros de rota quando eles são inseridos na URL de back-end. 

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Os parâmetros de rota com barras codificadas são decodificados. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Todos os parâmetros de rota são passados em conjunto inalterado, que é o comportamento padrão. |

Por exemplo, considere o proxies.jsno arquivo para um aplicativo de funções no `myfunction.com` domínio.

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

Quando `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` é definido como `true` , a URL é `example.com/api%2ftest` resolvida para `example.com/api/test` . Por padrão, a URL permanece inalterada como `example.com/test%2fapi` . Para saber mais, veja [proxies de funções](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Na versão 2. x e versões posteriores do tempo de execução do functions, o configura o comportamento do aplicativo com base no ambiente de tempo de execução. Esse valor é [lido durante a inicialização](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Você pode definir `AZURE_FUNCTIONS_ENVIRONMENT` para qualquer valor, mas há suporte para [três valores](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [desenvolvimento](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [preparo](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)e [produção](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Quando `AZURE_FUNCTIONS_ENVIRONMENT` não está definido, ele usa como padrão `Development` um ambiente local e `Production` no Azure. Essa configuração deve ser usada em vez de `ASPNETCORE_ENVIRONMENT` para definir o ambiente de tempo de execução. 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

Na versão 2. x e versões posteriores do tempo de execução do functions, as configurações do aplicativo podem substituir [host.jsnas](functions-host-json.md) configurações no ambiente atual. Essas substituições são expressas como configurações de aplicativo chamadas `AzureFunctionsJobHost__path__to__setting` . Para obter mais informações, consulte [substituir host.jsem valores](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de conexão da conta de armazenamento opcional para armazenar logs e exibi-los na guia **Monitor** do portal. Essa configuração só é válida para aplicativos que têm como destino a versão 1. x do tempo de execução de Azure Functions. A conta de armazenamento deve ser de uso geral, com suporte para blobs, filas e tabelas. Para saber mais, confira [Requisitos de uma conta de armazenamento](storage-considerations.md#storage-account-requirements).

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> Para obter melhor desempenho e experiência, o tempo de execução versão 2. x e versões posteriores usam APPINSIGHTS_INSTRUMENTATIONKEY e o app insights para monitoramento em vez de `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` significa desabilitar a página de aterrissagem padrão mostrada para a URL raiz de um aplicativo de funções. O padrão é `false`.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Quando essa configuração de aplicativo é omitida ou definida como `false`, uma página semelhante ao exemplo a seguir é exibida na resposta à URL `<functionappname>.azurewebsites.net`.

![Página de aterrissagem do aplicativo de funções](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` significa usar o modo de versão durante a compilação do código .NET; `false` significa usar o modo de depuração. O padrão é `true`.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Uma lista delimitada por vírgulas de recursos beta a habilitar. Os recursos beta habilitados por esses sinalizadores não estão prontos para produção, mas podem ser habilitados para uso experimental antes de serem lançados.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica o repositório ou o provedor a ser usado para armazenar chaves. Atualmente, os repositórios com suporte são o armazenamento de blobs ("Blob") e o sistema de arquivos local ("Arquivos"). O padrão é blob na versão 2 e sistema de arquivos na versão 1.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsSecretStorageType|Arquivos|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

O tempo de execução de Azure Functions usa essa cadeia de conexão da conta de armazenamento para a operação normal. Alguns usos dessa conta de armazenamento incluem pontos de verificação de gerenciamento de chaves, gerenciamento de gatilho de temporizador e hubs de eventos. A conta de armazenamento deve ser de uso geral, com suporte para blobs, filas e tabelas. Consulte [Conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [Requisitos da conta de armazenamento](storage-considerations.md#storage-account-requirements).

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador usado para TypeScript. Permite substituir o padrão se necessário.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Determina se a edição no portal do Azure está habilitada. Os valores válidos são "readwrite" e "readonly".

|Chave|Valor de exemplo|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

A versão do runtime do Functions para usar nesse aplicativo de funções. Um til com a versão principal significa usar a versão mais recente da versão principal (por exemplo, "~2"). Quando novas versões da mesma versão principal estão disponíveis, elas são instaladas automaticamente no aplicativo de funções. Para fixar o aplicativo a uma versão específica, use o número de versão completo (por exemplo, "2.0.12345"). O padrão é "~ 2". Um valor de pinos `~1` seu aplicativo para a versão 1.x do runtime.

|Chave|Valor de exemplo|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>Modo de compatibilidade do Functions \_ v2 \_ \_

Essa configuração permite que seu aplicativo de funções seja executado em um modo compatível com versão 2. x no tempo de execução da versão 3. x. Use essa configuração somente se encontrar problemas ao [atualizar seu aplicativo de funções da versão 2. x para 3. x do tempo de execução](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Essa configuração destina-se somente como uma solução alternativa de curto prazo enquanto você atualiza seu aplicativo para ser executado corretamente na versão 3. x. Há suporte para essa configuração, contanto que o [tempo de execução 2. x tenha suporte](functions-versions.md). Se você encontrar problemas que impeçam que seu aplicativo seja executado na versão 3. x sem usar essa configuração, informe [seu problema](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Exige que [a \_ \_ versão de extensão de funções](functions-app-settings.md#functions_extension_version) seja definida como `~3` .

|Chave|Valor de exemplo|
|---|------------|
|Modo de compatibilidade do Functions \_ v2 \_ \_|true|

## <a name="functions_worker_process_count"></a>\_contagem de \_ processos de trabalho do Functions \_

Especifica o número máximo de processos de trabalho de idioma, com um valor padrão de `1` . O valor máximo permitido é `10` . As invocações de função são distribuídas uniformemente entre os processos de trabalho de linguagem. Os processos de trabalho de idioma são gerados a cada 10 segundos até que a contagem de processo de trabalho de funções definida pela função \_ \_ \_ seja atingida. O uso de vários processos de trabalho de idioma não é o mesmo que o [dimensionamento](functions-scale.md). Considere usar essa configuração quando sua carga de trabalho tiver uma combinação de invocações associadas à CPU e de e/s. Essa configuração se aplica a todos os idiomas do non-.NET.

|Chave|Valor de exemplo|
|---|------------|
|\_contagem de \_ processos de trabalho do Functions \_|2|

## <a name="python_threadpool_thread_count"></a>\_contagem de \_ threads de THREADPOOL do Python \_

Especifica o número máximo de threads que um trabalho do Python Language usaria para executar invocações de função, com um valor padrão de `1` para a versão do Python `3.8` e abaixo. Para a versão do Python `3.9` e superior, o valor é definido como `None` . Observe que essa configuração não garante o número de threads que seriam definidos durante as execuções. A configuração permite que o Python expanda o número de threads para o valor especificado. A configuração se aplica somente a aplicativos do Python functions. Além disso, a configuração se aplica à invocação de funções síncronas e não a corrotinas.

|Chave|Valor de exemplo|Valor máximo|
|---|------------|---------|
|\_contagem de \_ threads de THREADPOOL do Python \_|2|32|


## <a name="functions_worker_runtime"></a>FUNÇÕES\_TRABALHADOR\_TEMPO DE EXECUÇÃO

O runtime do trabalho de linguagem deve ser carregado no aplicativo de funções.  Isso irá corresponder ao idioma que está sendo usado em seu aplicativo (por exemplo, "dotnet"). Para funções em vários idiomas, você precisará publicá-las em vários aplicativos, cada um com um valor de runtime de trabalho correspondente.  Os valores válidos são `dotnet` (C#/f #), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) e `python` (Python).

|Chave|Valor de exemplo|
|---|------------|
|FUNÇÕES\_TRABALHADOR\_TEMPO DE EXECUÇÃO|dotnet|

## <a name="pip_extra_index_url"></a>\_URL de \_ índice \_ extra de Pip

O valor dessa configuração indica uma URL de índice de pacote personalizado para aplicativos Python. Use essa configuração quando precisar executar uma compilação remota usando dependências personalizadas que são encontradas em um índice de pacote extra.   

|Chave|Valor de exemplo|
|---|------------|
|\_URL de \_ índice \_ extra de Pip|http://my.custom.package.repo/simple |

Para saber mais, confira [dependências personalizadas](functions-reference-python.md#remote-build-with-extra-index-url) na referência do desenvolvedor do Python.

## <a name="scale_controller_logging_enable"></a>\_habilitar o \_ log do controlador de escala \_

_No momento, essa configuração está na versão prévia._  

Essa configuração controla o registro em log do controlador de escala de Azure Functions. Para obter mais informações, consulte [dimensionar os logs do controlador](functions-monitoring.md#scale-controller-logs).

|Chave|Valor de exemplo|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLE|AppInsights: detalhado|

O valor dessa chave é fornecido no formato `<DESTINATION>:<VERBOSITY>` , que é definido da seguinte maneira:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>CONTENTAZUREFILECONNECTIONSTRING do site \_

Cadeia de conexão para a conta de armazenamento em que o código e a configuração do aplicativo de funções são armazenados em planos de dimensionamento orientados a eventos em execução no Windows. Para obter mais informações, consulte [criar um aplicativo de funções](functions-infrastructure-as-code.md#windows).

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

Usado somente ao implantar em um consumo ou planos Premium em execução no Windows. Sem suporte para Linux. Alterar ou remover essa configuração pode fazer com que seu aplicativo de funções não seja iniciado. Para saber mais, confira [Este artigo de solução de problemas](functions-recover-storage-account.md#storage-account-application-settings-were-deleted). 

## <a name="website_contentovervnet"></a>CONTENTOVERVNET do site \_

Somente para planos Premium. Um valor `1` permite que seu aplicativo de funções seja dimensionado quando você tiver sua conta de armazenamento restrita a uma rede virtual. Você deve habilitar essa configuração ao restringir sua conta de armazenamento a uma rede virtual. Para saber mais, confira [restringir sua conta de armazenamento a uma rede virtual](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network).

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

O caminho do arquivo para o código do aplicativo de funções e a configuração em um plano de dimensionamento controlado por eventos no Windows. Usado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. O padrão é uma cadeia única que começa com o nome do aplicativo de funções. Consulte [Criar um aplicativo de funções](functions-infrastructure-as-code.md#windows).

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

Usado somente por aplicativos de funções em um consumo ou planos Premium em execução no Windows. Sem suporte para Linux. Alterar ou remover essa configuração pode fazer com que seu aplicativo de funções não seja iniciado. Para saber mais, confira [Este artigo de solução de problemas](functions-recover-storage-account.md#storage-account-application-settings-were-deleted).

Ao usar um Azure Resource Manager para criar um aplicativo de funções durante a implantação, não inclua WEBSITE_CONTENTSHARE no modelo. Essa configuração de aplicativo é gerada durante a implantação. Para saber mais, confira [automatizar a implantação de recursos para seu aplicativo de funções](functions-infrastructure-as-code.md#windows).   

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

O número máximo de instâncias que o aplicativo de funções pode alcançar. O padrão é sem limites.

> [!IMPORTANT]
> Essa configuração está em versão prévia.  Uma [propriedade de aplicativo para a função de expansão máxima](./event-driven-scaling.md#limit-scale-out) foi adicionada e é a maneira recomendada para limitar a escala horizontal.

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Somente Windows._  
Define a versão do Node.js a ser usada ao executar seu aplicativo de funções no Windows. Você deve usar um til (~) para que o tempo de execução use a versão mais recente disponível da versão principal de destino. Por exemplo, quando definido como `~10` , a versão mais recente do Node.js 10 é usada. Quando uma versão principal é destinada a um til, você não precisa atualizar manualmente a versão secundária. 

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>SITE\_EXECUTAR\_DO\_PACOTE

Permite que seu aplicativo de funções execute de um arquivo de pacote montado.

|Chave|Valor de exemplo|
|---|------------|
|SITE\_EXECUTAR\_DO\_PACOTE|1|

Os valores válidos são `1` ou uma URL que resolve para o local de um arquivo de pacote de implantação. Quando definido como `1`, o pacote deve estar na pasta `d:\home\data\SitePackages`. Ao usar a implantação em zip com essa configuração, o pacote é automaticamente carregado para esse local. Na versão prévia, essa configuração foi nomeada `WEBSITE_RUN_FROM_ZIP`. Para obter mais informações, veja [Executar suas funções de um arquivo de pacote](run-functions-from-deployment-package.md).

## <a name="website_time_zone"></a>\_fuso horário do site \_

Permite que você defina o fuso horário para seu aplicativo de funções. 

|Chave|Sistema operacional|Valor de exemplo|
|---|--|------------|
|\_fuso horário do site \_|Windows|Hora oficial do leste|
|\_fuso horário do site \_|Linux|América/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="next-steps"></a>Próximas etapas

[Saiba como atualizar as configurações do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings)

[Consulte as configurações globais no arquivo host.json](functions-host-json.md)

[Consulte outras configurações de aplicativo para aplicativos do Serviço de Aplicativo](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
