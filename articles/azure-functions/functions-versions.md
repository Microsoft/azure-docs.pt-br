---
title: Visão geral de versões de tempo de execução do Azure Functions
description: O Azure Functions é compatível com várias versões do tempo de execução. Aprenda as diferenças entre elas e como escolher a certa para você.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: glenga
ms.openlocfilehash: 6988fb547b07f81891efea3caad8bf34f4c8a476
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036199"
---
# <a name="azure-functions-runtime-versions-overview"></a>Visão geral de versões de tempo de execução do Azure Functions

 Há duas versões principais do Azure Functions runtime: 1.x e 2.x. A versão atual em que o novo recurso funciona e melhorias estão sendo feitas é a 2.x, embora ambas deem suporte a cenários de produção.  A seguir estão detalhadas algumas das diferenças entre as duas versões, como você pode criar cada versão e atualizar da 1.x para a 2.x.

> [!NOTE]
> Este artigo refere-se ao serviço de nuvem Azure Functions. Para obter informações sobre o produto de versão prévia que permite que você execute o Azure Functions localmente, confira a [Visão geral do Azure Functions Runtime](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Desenvolvimento multiplataforma

O tempo de execução versão 2.x é executado no .NET Core 2, o que permite a ele ser executado em todas as plataformas compatíveis no .NET Core, incluindo macOS e Linux. A execução no .NET Core permite cenários de desenvolvimento e hospedagem multiplataforma.

No entanto, o tempo de execução versão 1.x só dá suporte a desenvolvimento e hospedagem no portal do Azure ou em computadores Windows.

## <a name="languages"></a>Languages

O tempo de execução versão 2.x usa um novo modelo de extensibilidade de linguagem. Na versão 2.x, todas as funções em um aplicativo de funções devem compartilhar o mesmo idioma. A linguagem de funções em um aplicativo de funções é escolhida ao criá-lo.

Linguagens experimentais do Azure Functions 1.x não foram atualizadas para usar o novo modelo, por isso elas não são compatíveis com a versão 2.x. A tabela a seguir indica quais linguagens de programação são atualmente compatíveis com cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, consulte [Linguagens com suporte](supported-languages.md).

## <a name="creating-1x-apps"></a>Executar na versão 1.x

Por padrão, os aplicativos de funções criados no portal do Azure são definidos para a versão 2.x. Quando possível, você deve usar esta versão de tempo de execução, na qual novos investimentos em recursos estão sendo feitos. Se necessário, você ainda pode executar um aplicativo de funções no tempo de execução versão 1.x. Você só pode alterar a versão de tempo de execução depois de criar seu aplicativo de funções, mas antes de adicionar quaisquer funções. Para saber como fixar a versão de tempo de execução como 1.x, confira [Exibir e atualizar a versão atual do tempo de execução](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-2x"></a>Como migrar da versão 1.x para a 2.x

Você pode optar por migrar um aplicativo existente, escrito para usar a versão de tempo de execução 1.x, para usar em vez disso a versão 2.x. A maioria das alterações que você precisa fazer está relacionada a alterações no tempo de execução da linguagem, assim como alterações de API do C# entre o .NET Core 2 e o .NET Framework 4.7. Você também precisará verificar se seu código e suas bibliotecas são compatíveis com o tempo de execução de linguagem escolhido. Por fim, não deixe de observar as alterações no gatilho, associações e recursos destacados abaixo. Para obter melhores resultados de migração, você deve criar um novo aplicativo de funções para a versão 2.x e transferir seu código de função versão 1.x existente para o novo aplicativo.  

### <a name="changes-in-triggers-and-bindings"></a>Alterações nos gatilhos e associações

A versão 2.x requer que você instale as extensões para associações e disparadores específicos usados pelas funções em seu aplicativo. A única exceção para isso são os gatilhos de temporizador e HTTP, que não exigem uma extensão.  Para obter mais informações, confira [Registrar e instalar extensões de associação](./functions-bindings-register.md).

Também houve algumas alterações no `function.json` ou em atributos da função entre versões. Por exemplo, a propriedade `path` do Hub de Eventos agora é `eventHubName`. Veja a [tabela de associação existente](#bindings) para obter links para a documentação de cada associação.

### <a name="changes-in-features-and-functionality"></a>Alterações nos recursos e funcionalidades

Alguns recursos também foram removidos, atualizados ou substituídos na nova versão. Esta seção fornece detalhes sobre as alterações que você vê na versão 2.x depois de ter usado a versão 1.x.

As seguintes alterações foram feitas na versão 2.x:

* As chaves para chamar pontos de extremidade HTTP são sempre armazenadas criptografadas no Armazenamento de Blobs do Azure. Na versão 1.x, chaves foram armazenadas no Armazenamento de Arquivos do Azure por padrão. Ao atualizar um aplicativo da versão 1.x para a 2.x, os segredos existentes que estão no armazenamento de arquivos são redefinidos.

* A versão 2.x do tempo de execução não inclui suporte interno para provedores de webhook. Essa alteração foi feita para melhorar o desempenho. Você ainda pode usar gatilhos HTTP como pontos de extremidade para webhooks.

* O arquivo de configuração de host (host.json) deve estar vazio ou ter a cadeia de caracteres `"version": "2.0"`.

* Para melhorar o monitoramento, o painel WebJobs no portal, o qual usou a configuração [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard), é substituído com o Azure Application Insights, que usa a configuração [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey). Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md).

* Todas as funções em um aplicativo de funções devem compartilhar a mesma linguagem de programação. Quando você cria um aplicativo de funções, você deve escolher para ele uma pilha de tempo de execução. A pilha de tempo de execução é especificada pelo valor [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) nas configurações do aplicativo. Esse requisito foi adicionado para melhorar o tempo de inicialização e o volume. Ao desenvolver localmente, você também deve incluir essa configuração no [arquivo local.settings.json](functions-run-local.md#local-settings-file).

* O tempo limite padrão para as funções em um plano do Serviço de Aplicativo foi alterado para 30 minutos. Você pode alterar manualmente o tempo limite para ilimitado, usando a configuração [functionTimeout](functions-host-json.md#functiontimeout) em host.json.

* Restrições de simultaneidade HTTP são implementadas por padrão para funções de plano de consumo, com um padrão de 100 solicitações simultâneas por instância. Você pode alterar isso na configuração [`maxConcurrentRequests`](functions-host-json.md#http) no arquivo host.json.

* Por causa das [limitações do .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), o suporte a funções de script F# (.fsx) foi removido. Funções F# compiladas (.fs) ainda são compatíveis.

* O formato da URL de webhooks de gatilho da Grade de Eventos foi alterado para `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Como migrar um aplicativo desenvolvido localmente

Você pode ter projetos de aplicativo de funções existentes que você desenvolveu localmente usando o tempo de execução versão 1.x. Para atualizar para a versão 2.x, você deve criar um projeto de aplicativo de funções local em relação à versão 2.x e transferir seu código existente para o novo aplicativo. Você pode atualizar manualmente o projeto e o código existentes, uma espécie de atualização "in-loco". No entanto, há vários outros aprimoramentos entre a versão 1.x e a 2.x que você ainda pode precisar fazer. Por exemplo, em C#, o objeto de depuração foi alterado de `TraceWriter` para `ILogger`. Ao criar um novo projeto de versão 2.x, você começa com funções atualizadas com base nos modelos de versão 2.x mais recentes.

#### <a name="visual-studio-runtime-versions"></a>Versões de tempo de execução do Visual Studio

No Visual Studio, você seleciona a versão de tempo de execução quando cria um projeto. As Ferramentas do Azure Functions para Visual Studio dão suporte a ambas as versões principais do tempo de execução. A versão correta é usada ao depurar e publicar com base nas configurações do projeto. As configurações de versão são definidas no arquivo `.csproj` nas seguintes propriedades:

##### <a name="version-1x"></a>Versão 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versão 2.x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Quando você depura ou publica seu projeto, a versão correta do tempo de execução é usada.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code e Azure Functions Core Tools

O [Azure Functions Core Tools](functions-run-local.md) é usado para o desenvolvimento de linha de comando e também pela [extensão do Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code. Para desenvolver para a versão 2.x, instale a versão 2.x do Core Tools. A implantação da versão 1.x requer a versão 1.x do Core Tools. Para obter mais informações, confira [Instalar o Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Para desenvolvimento no Visual Studio Code, você também precisará atualizar a configuração do usuário para o `azureFunctions.projectRuntime` corresponder à versão das ferramentas instaladas.  Essa configuração também atualiza os modelos e linguagens utilizados durante a criação do aplicativo de funções.

### <a name="changing-version-of-apps-in-azure"></a>Como alterar a versão de aplicativos no Azure

A versão do tempo de execução do Functions usada por aplicativos publicados no Azure é determinada pela configuração de aplicativo [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version). Um valor de `~2` destina-se ao tempo de execução versão 2.x, enquanto `~1` destina-se ao tempo de execução versão 1.x. Não altere essa configuração arbitrariamente, pois para isso provavelmente são necessárias outras alterações de configuração de aplicativo e alterações de código em suas funções. Para saber mais sobre a maneira recomendada de migrar seu aplicativo de funções para uma versão de tempo de execução diferente, confira [Como direcionar para versões do Azure Functions Runtime](set-runtime-version.md).

## <a name="bindings"></a>Associações

O tempo de execução versão 2.x usa um novo [modelo de extensibilidade de associação](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) que oferece estas vantagens:

* Suporte para extensões de associação de terceiros.

* Desacoplamento de tempo de execução e associações. Essa alteração permite que extensões de associação sejam lançadas independentemente e com controle de versão. Você pode, por exemplo, optar por atualizar para uma versão de uma extensão que se baseia em uma versão mais recente de um SDK subjacente.

* Um ambiente de execução mais leve, onde apenas as associações em uso são conhecidas e carregadas pelo tempo de execução.

Com exceção dos gatilhos HTTP e de temporizador, todas as associações devem ser explicitamente adicionadas ao projeto do aplicativo de funções ou registradas no portal. Para obter mais informações, confira [Registrar extensões de associação](./functions-bindings-expressions-patterns.md).

A tabela a seguir mostra quais associações são compatíveis em cada versão de tempo de execução.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Codificar e testar o Azure Functions localmente](functions-run-local.md)
* [Como direcionar para versões do Azure Functions Runtime](set-runtime-version.md)
* [Notas de versão](https://github.com/Azure/azure-functions-host/releases)
