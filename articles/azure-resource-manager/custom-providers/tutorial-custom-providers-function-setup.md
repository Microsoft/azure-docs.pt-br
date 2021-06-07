---
title: Configurar o Azure Functions
description: Este tutorial descreve como criar um aplicativo de funções do Azure e configurá-lo para funcionar com os Provedores Personalizados do Azure
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 55554678047faeedd16b78dea61a42d50fd59491
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737313"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurar o Azure Functions para os Provedores Personalizados do Azure

Um provedor personalizado é um contrato entre o Azure e um ponto de extremidade. Com provedores personalizados, você pode alterar os fluxos de trabalho no Azure. Este tutorial mostra como configurar um aplicativo de funções do Azure para funcionar como um ponto de extremidade de provedor personalizado.

## <a name="create-the-azure-function-app"></a>Criar o aplicativo de funções do Azure

> [!NOTE]
> Neste tutorial, você criará um ponto de extremidade de serviço simples que usa um aplicativo de funções do Azure. No entanto, um provedor personalizado pode usar qualquer ponto de extremidade publicamente acessível. As alternativas incluem Aplicativos Lógicos do Azure, Gerenciamento de API do Azure e o recurso Aplicativos Web do Serviço de Aplicativo do Azure.

Para iniciar este tutorial, primeiro você deve seguir o tutorial [Criar seu primeiro aplicativo de funções do Azure no portal do Azure](../../azure-functions/functions-get-started.md). Esse tutorial cria uma função de webhook do .NET Core que pode ser modificada no portal do Azure. Também é a base para o tutorial atual.

## <a name="install-azure-table-storage-bindings"></a>Instalar as associações do Armazenamento de Tabelas do Azure

Para instalar as associações do Armazenamento de Tabelas do Azure:

1. Acesse a guia **Integrar** do HttpTrigger.
1. Selecione **+ Nova Entrada**.
1. Selecione **Armazenamento de Tabelas do Azure**.
1. Instale a extensão Microsoft.Azure.WebJobs.Extensions.Storage, caso ainda não esteja instalada.
1. Na caixa **Nome do parâmetro de tabela**, insira **tableStorage**.
1. Na caixa **Nome da tabela**, insira **myCustomResources**.
1. Selecione **Salvar** para salvar o parâmetro de entrada atualizado.

![Visão geral do provedor personalizado mostrando as associações de tabela](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Atualizar os métodos HTTP RESTful

Para configurar a função do Azure para incluir os métodos de solicitação RESTful do provedor personalizado:

1. Acesse a guia **Integrar** do HttpTrigger.
1. Em **Métodos HTTP selecionados**, selecione **GET**, **POST**, **DELETE** e **PUT**.

![Visão geral do provedor personalizado mostrando os métodos HTTP](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Adicionar pacotes do NuGet do Azure Resource Manager

> [!NOTE]
> Se o arquivo de projeto C# estiver ausente do diretório do projeto, adicione-o manualmente. Ou ele será exibido depois que a extensão Microsoft.Azure.WebJobs.Extensions.Storage for instalada no aplicativo de funções.

Em seguida, atualize o arquivo de projeto C# para incluir bibliotecas úteis do NuGet. Essas bibliotecas facilitam a análise das solicitações de entrada de provedores personalizados. Siga as etapas para [adicionar extensões por meio do portal](../../azure-functions/functions-bindings-register.md) e atualize o arquivo de projeto C# para incluir as seguintes referências de pacote:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

O seguinte elemento XML é um arquivo de projeto C# de exemplo:

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

Neste tutorial, você configurou um aplicativo de funções do Azure para funcionar como um ponto de extremidade de provedor personalizado do Azure.

Para saber como criar um ponto de extremidade de provedor personalizado RESTful, confira [Tutorial: Como criar um ponto de extremidade do provedor personalizado RESTful](./tutorial-custom-providers-function-authoring.md).