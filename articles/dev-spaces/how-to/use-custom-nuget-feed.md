---
title: Como usar um feed do NuGet personalizado no Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Use um feed NuGet personalizado para acessar e usar os pacotes NuGet em um Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
manager: gwallace
ms.openlocfilehash: 44a87491d276e09e1fa8fed3f5e6803648c3e4a2
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305393"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Use um feed NuGet personalizado em um Azure Dev Space

Um feed NuGet fornece uma maneira conveniente de incluir fontes de pacotes em um projeto. Azure Dev Spaces precisa acessar esse feed para que as dependências sejam instaladas corretamente no contêiner do Docker.

## <a name="set-up-a-nuget-feed"></a>Configurar um feed NuGet

Adicione uma [referência de pacote](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) para sua dependência no `*.csproj` arquivo sob o `PackageReference` nó. Por exemplo:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Crie um arquivo [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) na pasta do projeto e defina as `packageSources` seções `packageSourceCredentials` e para o feed do NuGet. A `packageSources` seção contém a URL do feed, que deve ser acessível publicamente. O `packageSourceCredentials` são as credenciais para acessar o feed. Por exemplo:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Atualize seu Dockerfiles para copiar o `NuGet.Config` arquivo para a imagem. Por exemplo:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> No Windows, `NuGet.Config`, `Nuget.Config`e `nuget.config` todos funcionam como nomes de arquivo válidos. No Linux, apenas `NuGet.Config` um nome de arquivo válido para esse arquivo. Como o Azure Dev Spaces usa o Docker e o Linux, esse arquivo `NuGet.Config`deve ser nomeado. Você pode corrigir a nomenclatura manualmente ou executando `dotnet restore --configfile nuget.config`.


Se você estiver usando o Git, não deverá ter as credenciais para o feed do NuGet no controle de versão. Adicione `NuGet.Config` `NuGet.Config` ao para o seu projeto para que o arquivo não seja adicionado ao controle de versão. `.gitignore` Azure dev Spaces precisará desse arquivo durante o processo de criação da imagem de contêiner, mas, por padrão, ele respeita `.gitignore` as `.dockerignore` regras definidas no e durante a sincronização. Para alterar o padrão e permitir que Azure dev Spaces Sincronize o `NuGet.Config` arquivo, atualize o `azds.yaml` arquivo:

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

Se você não estiver usando o Git, poderá ignorar esta etapa.

Na próxima vez que você `azds up` executar ou `F5` acessar o Visual Studio Code ou o Visual Studio, Azure dev Spaces sincronizará o arquivo e o usará para instalar as dependências do `NuGet.Config` pacote.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o NuGet e como ele funciona](https://docs.microsoft.com/nuget/what-is-nuget).