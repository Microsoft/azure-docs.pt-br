---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020324"
---
Se não for possível usar os pacotes de extensão, você poderá usar Azure Functions Core Tools localmente para instalar os pacotes de extensão específicos exigidos pelo seu projeto.

> [!IMPORTANT]
> Você não pode instalar extensões explicitamente em um aplicativo de funções que esteja usando pacotes de extensão. Remova a `extensionBundle` seção em *host.jsantes de* instalar explicitamente as extensões.

Os itens a seguir descrevem alguns motivos pelos quais você pode precisar instalar extensões manualmente:

* Você precisa acessar uma versão específica de uma extensão não disponível em um pacote.
* Você precisa acessar uma extensão personalizada não disponível em um pacote.
* Você precisa acessar uma combinação específica de extensões não disponíveis em um único pacote.

> [!NOTE]
> Para instalar manualmente as extensões usando as ferramentas básicas, você deve ter o [SDK do .NET Core 2. x](https://dotnet.microsoft.com/download) instalado. O SDK do .NET Core é usado pelo Azure Functions Core Tools para instalar extensões do NuGet. Você não precisa conhecer o .NET para usar as extensões de Azure Functions.

Quando você instala extensões explicitamente, um arquivo de projeto do .NET chamado Extensions. csproj é adicionado à raiz do seu projeto. Esse arquivo define o conjunto de pacotes NuGet exigidos por suas funções. Embora você possa trabalhar com as [referências do pacote NuGet](/nuget/consume-packages/package-references-in-project-files) neste arquivo, as ferramentas principais permitem que você instale extensões sem precisar editar manualmente o arquivo.

Há várias maneiras de usar as ferramentas básicas para instalar as extensões necessárias em seu projeto local. 

#### <a name="install-all-extensions"></a>Instalar todas as extensões 

Use o seguinte comando para adicionar automaticamente todos os pacotes de extensão usados pelas associações em seu projeto local:

```dotnetcli
func extensions install
```
O comando lê o *function.jsno* arquivo para ver quais pacotes são necessários, instala-os e recria o projeto de extensões (Extensions. csproj). Ele adiciona as novas associações na versão atual, mas não atualiza as associações existentes. Use a opção `--force` para atualizar as associações existentes para a versão mais recente nas novas instalações.

Se seu aplicativo de funções usar associações que as ferramentas principais não reconhecem, você deverá instalar manualmente a extensão específica.

#### <a name="install-a-specific-extension"></a>Instalar uma extensão específica

Use o comando a seguir para instalar um pacote de extensão específico em uma versão específica, neste caso, a extensão de armazenamento:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
