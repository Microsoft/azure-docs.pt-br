---
title: Registrar as extensões de associação de funções do Azure
description: Saiba como registrar uma extensão de associação de funções do Azure com base em seu ambiente.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 5969c3e0d270b45347f8132b2d655ba2e56cb2c0
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625890"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrar as extensões de associação de funções do Azure

No Azure Functions versão 2.x, [associações](./functions-triggers-bindings.md) estão disponíveis como pacotes separados do tempo de execução de funções. Enquanto funções .NET acessam associações por meio de pacotes do NuGet, pacotes de extensão permitem que outro acesso de funções para todas as associações por meio de uma definição de configuração.

Considere os seguintes itens relacionados para extensões de associação:

- As extensões de associação explicitamente não estão registradas no Functions 1.x, exceto quando [criando um C# biblioteca de classes usando o Visual Studio](#local-csharp).

- Gatilhos HTTP e timer têm suporte por padrão e não exigem uma extensão.

A tabela a seguir indica quando e como você registra associações.

| Ambiente de desenvolvimento |Registro<br/> em funções de 1. x  |Registro<br/> em funções 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|Automático|
|Linguagens não .NET ou desenvolvimento de ferramentas básicas do Azure local|Automático|[Use as ferramentas básicas do Azure Functions e os pacotes de extensão](#extension-bundles)|
|C#biblioteca de classes usando o Visual Studio|[Usar as ferramentas do NuGet](#vs)|[Usar as ferramentas do NuGet](#vs)|
|Biblioteca de classes C# usando o código do Visual Studio|N/D|[Use o .NET Core CLI](#vs-code)|

## <a name="extension-bundles"></a>Pacotes de extensão para o desenvolvimento local

Pacotes de extensão é uma tecnologia de desenvolvimento local para a versão 2.x tempo de execução que permite que você adicione um conjunto compatível de extensões de associação para seu projeto de aplicativo de função de funções. Esses pacotes de extensão, em seguida, são incluídos no pacote de implantação quando você implanta no Azure. Pacotes faz com que todas as associações publicadas pela Microsoft disponível por meio de uma configuração na *host. JSON* arquivo. Pacotes de extensão definidos em um pacote são compatíveis entre si, que ajuda a evitar conflitos entre pacotes. Quando localmente, desenvolvendo Verifique se você estiver usando a versão mais recente do [as ferramentas básicas do Azure Functions](functions-run-local.md#v2).

Use pacotes de extensão para todo o desenvolvimento local usando as ferramentas básicas do Azure Functions ou do Visual Studio Code.

Se você não usar pacotes de extensão, você deve instalar o .NET Core 2.x do SDK em seu computador local antes de instalar quaisquer extensões de associação. Pacotes elimina essa exigência para o desenvolvimento local. 

Para usar pacotes de extensão, atualize o *host. JSON* arquivo para incluir a seguinte entrada para `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

As seguintes propriedades estão disponíveis no `extensionBundle`:

| Propriedade | DESCRIÇÃO |
| -------- | ----------- |
| **`id`** | O namespace para pacotes de extensão do Microsoft Azure Functions. |
| **`version`** | A versão do pacote a instalar. O tempo de execução de funções sempre escolhe a versão permitida máximo definida pelo intervalo de versão ou intervalo. O valor de versão acima permite que todas as versões de pacote do 1.0.0 até, mas não incluindo 2.0.0. Para obter mais informações, consulte o [notação de intervalo para especificar os intervalos de versão](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Incremento de versões do pacote como pacotes na alteração do pacote. Alterações de versão principal ocorrem quando os pacotes no pacote incrementam por uma versão principal, que geralmente coincide com uma alteração na versão principal do tempo de execução de funções.  

O conjunto atual de extensões instalado pelo pacote padrão são enumeradas no [extensions.json arquivo](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="vs"></a> C\# biblioteca de classes com o Visual Studio

Na **Visual Studio**, você pode instalar os pacotes do Console do Gerenciador de pacotes usando o [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) de comando, conforme mostrado no exemplo a seguir:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

O nome do pacote usado para uma determinada vinculação é fornecido no artigo de referência para que a associação. Para obter um exemplo, consulte o [pacotes seção do artigo de referência de associação do barramento de serviço](functions-bindings-service-bus.md#packages---functions-1x).

Substituir `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. Versões válidas são listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem às funções de tempo de execução 1. x ou 2. x são especificadas no artigo de referência para a associação.

Se você usar `Install-Package` para fazer referência a uma associação, você não precisará usar [pacotes de extensão](#extension-bundles). Essa abordagem é específica para bibliotecas de classes criadas no Visual Studio.

## <a name="vs-code"></a> C#biblioteca de classes com Visual Studio Code

> [!NOTE]
> É recomendável usar [pacotes de extensão](#extension-bundles) ter funções instalar automaticamente um conjunto compatível de pacotes de extensão de associação.

Na **Visual Studio Code**, instalar pacotes para um C# projeto de biblioteca de classes do prompt de comando usando o [dotnet Adicionar pacote](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) comando na CLI do .NET Core. O exemplo a seguir demonstra como adicionar uma associação:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

O .NET Core CLI só pode ser usado para o desenvolvimento do Azure Functions 2.x.

Substitua `<BINDING_TYPE_NAME>` com o nome do pacote fornecido no artigo de referência para sua associação desejada. Você pode encontrar o artigo de referência de associação desejado na [lista de associações com suporte](./functions-triggers-bindings.md#supported-bindings).

Substituir `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. Versões válidas são listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem às funções de tempo de execução 1. x ou 2. x são especificadas no artigo de referência para a associação.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Exemplo de gatilho e associação de função do Azure](./functions-bindings-example.md)
