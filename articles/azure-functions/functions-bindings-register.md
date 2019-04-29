---
title: Registrar as extensões de associação de funções do Azure
description: Saiba como registrar uma extensão de associação de funções do Azure com base em seu ambiente.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 04/26/2019
ms.author: v-junlch
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437847"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrar as extensões de associação de funções do Azure

O Azure Functions dá suporte a HTTP e timer fora da caixa. Para trabalhar com outros serviços, você precisa instalar ou *registre* um [associação](./functions-triggers-bindings.md) extensão. As extensões de associação são fornecidas por meio de pacotes do NuGet ou de ferramentas básicas do Azure. 

A tabela a seguir indica quando e como você registra associações.

| Ambiente de desenvolvimento |Registro<br/> em funções de 1. x  |Registro<br/> em funções 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|[Automático com o prompt](#azure-portal-development)|
|Linguagens não .NET ou desenvolvimento de ferramentas básicas do Azure local|Automático|[Use os comandos principais ferramentas CLI](#local-development-azure-functions-core-tools)|
|Biblioteca de classes C# usando o Visual Studio de 2017|[Usar as ferramentas do NuGet](#c-class-library-with-visual-studio-2017)|[Usar as ferramentas do NuGet](#c-class-library-with-visual-studio-2017)|
|Biblioteca de classes C# usando o código do Visual Studio|N/D|[Use o .NET Core CLI](#c-class-library-with-visual-studio-code)|

Os seguintes tipos de associação são exceções que não exigem registro explícito porque são registrados automaticamente em todas as versões e ambientes: HTTP e temporizador.

> [!IMPORTANT]
> Este conteúdo para o restante deste artigo se aplica apenas ao Functions 2.x. As extensões de associação explicitamente não estão registradas no Functions 1.x, exceto quando [criando um C# biblioteca de classes usando o Visual Studio 2017](#local-csharp).

## <a name="azure-portal-development"></a>Desenvolvimento do portal do Azure

Quando você cria uma função ou adiciona uma associação, você será solicitado quando a extensão para o gatilho ou a associação requer o registro. Responder ao aviso clicando **instalar** para registrar a extensão. Instalação pode levar até 10 minutos em um plano de consumo. 

Você só precisa instalar cada extensão uma vez para um aplicativo de função determinada. Para associações com suporte que não estão disponíveis no portal ou para atualizar uma extensão instalada, você também pode [instalar ou atualizar manualmente as extensões de associação do Azure Functions no portal](install-update-binding-extensions-manual.md).  

## <a name="local-development-azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions para desenvolvimento local

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Biblioteca de classes C# com 2017 do Visual Studio

Em **2017 do Visual Studio**, você pode instalar os pacotes do Console do Gerenciador de pacotes usando o [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) de comando, conforme mostrado no exemplo a seguir:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

O nome do pacote a ser usado para uma associação fornecida é fornecido no artigo de referência para essa associação. Para obter um exemplo, consulte o [pacotes seção do artigo de referência de associação do barramento de serviço](functions-bindings-service-bus.md#packages---functions-1x).

Substituir `<target_version>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. Versões válidas são listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem às funções de tempo de execução 1. x ou 2. x são especificadas no artigo de referência para a associação.

## <a name="c-class-library-with-visual-studio-code"></a>Biblioteca de classes C# com o código do Visual Studio

Em **código do Visual Studio**, você pode instalar os pacotes do prompt de comando usando o [dotnet Adicionar pacote](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) comando no .NET Core CLI, conforme mostrado no exemplo a seguir:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

O .NET Core CLI só pode ser usado para o desenvolvimento do Azure Functions 2.x.

O nome do pacote a ser usado para uma associação fornecida é fornecido no artigo de referência para essa associação. Para obter um exemplo, consulte o [pacotes seção do artigo de referência de associação do barramento de serviço](functions-bindings-service-bus.md#packages---functions-1x).

Substituir `<target_version>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. Versões válidas são listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem às funções de tempo de execução 1. x ou 2. x são especificadas no artigo de referência para a associação.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Exemplo de gatilho e associação de função do Azure](./functions-bindings-example.md)


