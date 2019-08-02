---
title: Configurar o Azure Functions para provedores personalizados do Azure
description: Este tutorial apresentará como criar uma função do Azure e configurá-la para funcionar com Provedores Personalizados do Azure
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799115"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Configurar o Azure Functions para provedores personalizados do Azure

Provedores personalizados permitem que você personalize os fluxos de trabalho no Azure. Um provedor personalizado é um contrato entre o Azure e um `endpoint`. Este tutorial percorrerá o processo de configuração de uma Função do Azure para operar como um provedor personalizado `endpoint`.

Este tutorial é dividido nas seguintes etapas:

- Como criar a função do Azure
- Instalar as associações de Tabela do Azure
- Atualizar os métodos HTTP RESTful
- Adicionar pacotes do NuGet do Azure Resource Manager

Este tutorial criará os seguintes tutoriais:

- [Como criar sua primeira função do Azure por meio do portal do Azure](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Como criar a função do Azure

> [!NOTE]
> Neste tutorial, estamos criando um ponto de extremidade de serviço simples usando uma Função do Azure, mas um provedor personalizado pode usar qualquer `endpoint` acessível ao público. Aplicativos Lógicos do Azure, Gerenciamento de API do Azure e Aplicativos Web do Azure são algumas excelentes alternativas.

Para iniciar este tutorial, você deve seguir o tutorial [Como criar sua primeira Função do Azure no portal do Azure](../azure-functions/functions-create-first-azure-function.md). O tutorial criará uma função de webhook do .NET Core que pode ser modificada no portal do Azure.

## <a name="install-azure-table-bindings"></a>Instalar as associações de Tabela do Azure

Esta seção apresentará as etapas rápidas para instalar as associações de armazenamento da Tabela do Azure.

1. Navegue até a guia `Integrate` para o HttpTrigger.
2. Clique em `+ New Input`.
3. Selecione `Azure Table Storage`.
4. Instale o `Microsoft.Azure.WebJobs.Extensions.Storage` se ele ainda não estiver instalado.
5. Atualize o `Table parameter name` para "tableStorage" e o `Table name` para "myCustomResources".
6. Salve o parâmetro de entrada atualizado.

![Visão geral do provedor personalizado](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Atualizar os métodos HTTP RESTful

Esta seção apresentará as etapas rápidas para configurar a Função do Azure para incluir os métodos de solicitação RESTful do provedor personalizado.

1. Navegue até a guia `Integrate` para o HttpTrigger.
2. Atualize `Selected HTTP methods` para: GET, POST, DELETE e PUT.

![Visão geral do provedor personalizado](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>Como modificar o csproj

> [!NOTE]
> Se o csproj estiver ausente do diretório, ele poderá ser adicionado manualmente ou será exibido quando a extensão `Microsoft.Azure.WebJobs.Extensions.Storage` for instalada na função.

Em seguida, atualizaremos o arquivo csproj para incluir bibliotecas úteis do NuGet, que tornarão mais fácil analisar as solicitações recebidas de provedores personalizados. Siga as etapas em [adicionar extensões do portal](../azure-functions/install-update-binding-extensions-manual.md) e atualize o csproj para incluir as seguintes referências de pacote:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Arquivo csproj de exemplo:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, configuramos a uma Função do Azure para funcionar como um Provedor Personalizado do Azure `endpoint`. Vá para o próximo artigo para saber como criar um provedor personalizado RESTful `endpoint`.

- [Tutorial: Como criar um ponto de extremidade de provedor personalizado RESTful](./tutorial-custom-providers-function-authoring.md)
