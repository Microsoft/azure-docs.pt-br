---
title: Registrar Azure Functions extensões de associação
description: Saiba como registrar uma extensão de associação de Azure Functions com base em seu ambiente.
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88689535"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrar Azure Functions extensões de associação

Começando com Azure Functions versão 2. x, o tempo de execução do Functions inclui apenas gatilhos HTTP e Timer por padrão. Outros [gatilhos e associações](./functions-triggers-bindings.md) estão disponíveis como pacotes separados.

As funções de biblioteca de classes .NET usam associações que estão instaladas no projeto como pacotes NuGet. Os pacotes de extensão permitem que os aplicativos do non-.NET Functions usem as mesmas associações sem a necessidade de lidar com a infraestrutura do .NET.

A tabela a seguir indica quando e como você registra as associações.

| Ambiente de desenvolvimento |Registro<br/> em funções de 1. x  |Registro<br/> nas funções 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automática|Automático<sup>*</sup>|
|Idiomas Non-.NET|Automática|Usar [pacotes de extensão](#extension-bundles) (recomendado) ou [instalar extensões explicitamente](#explicitly-install-extensions)|
|Biblioteca de classes C# usando o Visual Studio|[Usar as ferramentas do NuGet](#vs)|[Usar as ferramentas do NuGet](#vs)|
|Biblioteca de classes C# usando o código do Visual Studio|N/D|[Use o .NET Core CLI](#vs-code)|

<sup>*</sup> O portal usa pacotes de extensão.

## <a name="access-extensions-in-non-net-languages"></a>Extensões de acesso em idiomas non-.NET

Para Java, JavaScript, PowerShell, Python e aplicativos de função de manipulador personalizado, recomendamos o uso de pacotes de extensão para acessar associações. Nos casos em que os pacotes de extensão não podem ser usados, você pode instalar explicitamente as extensões de associação.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Pacotes de extensão

Os pacotes de extensão são uma maneira de adicionar um conjunto compatível de extensões de associação ao seu aplicativo de funções. Você habilita os pacotes de extensão nohost.jsdo aplicativo *no* arquivo.

Você pode usar pacotes de extensão com a versão 2. x e versões posteriores do tempo de execução do functions.

Os pacotes de extensão têm controle de versão. Cada versão contém um conjunto específico de extensões de ligação que são verificadas para funcionar em conjunto. Selecione uma versão de pacote com base nas extensões que você precisa em seu aplicativo.

Para adicionar um pacote de extensão ao seu aplicativo de funções, adicione a `extensionBundle` seção a *host.jsem*. Em muitos casos, Visual Studio Code e Azure Functions Core Tools irão adicioná-lo automaticamente para você.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

A tabela a seguir lista as versões disponíveis no momento do pacote padrão *Microsoft. Azure. Functions. ExtensionBundle* e links para as extensões que eles incluem.

| Versão do pacote | Versão em host.jsem | Extensões incluídas |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | Consulte [extensions.jsem](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) usado para gerar o pacote |
| 2. x | `[2.*, 3.0.0)` | Consulte [extensions.jsem](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) usado para gerar o pacote |

> [!NOTE]
> Embora seja possível especificar um intervalo de versão personalizada no host.jsem, recomendamos que você use um valor de versão desta tabela.

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>Instalar extensões explicitamente

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Instalar extensões do NuGet em linguagens .NET

Para um projeto de funções baseadas em biblioteca de classes C#, você deve instalar extensões diretamente. Os pacotes de extensão são projetados especificamente para projetos que não são baseados na biblioteca de classes C#.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#Biblioteca de classes C com o Visual Studio

No **Visual Studio**, você pode instalar pacotes do console do Gerenciador de pacotes usando o comando [install-Package](/nuget/tools/ps-ref-install-package) , conforme mostrado no exemplo a seguir:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

O nome do pacote usado para uma determinada associação é fornecido no artigo de referência para essa associação. Para obter um exemplo, consulte o [pacotes seção do artigo de referência de associação do barramento de serviço](functions-bindings-service-bus.md#functions-1x).

Substituir `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. As versões válidas são listadas nas páginas de pacote individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem ao Functions Runtime 1. x ou 2. x são especificadas no artigo de referência para a associação.

Se você usar `Install-Package` o para fazer referência a uma associação, não precisará usar [pacotes de extensão](#extension-bundles). Essa abordagem é específica para bibliotecas de classes criadas no Visual Studio.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Biblioteca de classes C# com Visual Studio Code

No **Visual Studio Code**, instale pacotes para um projeto de biblioteca de classes C# do prompt de comando usando o comando [dotnet adicionar pacote](/dotnet/core/tools/dotnet-add-package) na CLI do .NET Core. O exemplo a seguir demonstra como você adiciona uma associação:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

O .NET Core CLI só pode ser usado para o desenvolvimento do Azure Functions 2.x.

Substitua `<BINDING_TYPE_NAME>` pelo nome do pacote que contém a associação de que você precisa. Você pode encontrar o artigo de referência de associação desejada na [lista de associações com suporte](./functions-triggers-bindings.md#supported-bindings).

Substituir `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. As versões válidas são listadas nas páginas de pacote individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem ao Functions Runtime 1. x ou 2. x são especificadas no artigo de referência para a associação.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Exemplo de gatilho e associação do Azure function](./functions-bindings-example.md)
