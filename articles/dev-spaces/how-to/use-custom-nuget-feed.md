---
title: Como usar um personalizado do NuGet do feed em espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: conceptual
description: Use um feed NuGet personalizado para acessar e usar os pacotes NuGet em um Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
manager: ghogen
ms.openlocfilehash: 1a000e378a9b8ecfb09d778fd6444e3f24b3df7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686439"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Use um feed NuGet personalizado em um Azure Dev Space

Um feed NuGet fornece uma maneira conveniente de incluir fontes de pacotes em um projeto. O Azure Dev Spaces precisará acessar esse feed para que as dependências sejam instaladas corretamente no contêiner do Docker.

## <a name="set-up-a-nuget-feed"></a>Configurar um feed NuGet

Para configurar um feed NuGet:
1. Adicione uma [referência de pacote](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) no arquivo `*.csproj` no nó `PackageReference`.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Crie um arquivo [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) na pasta do projeto.
     * Use a seção `packageSources` para referenciar o local do feed NuGet. Importante: O feed NuGet deve estar publicamente acessível.
     * Use a seção `packageSourceCredentials` para configurar credenciais de nome de usuário e senha. 

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

3. Se você estiver usando o controle do código-fonte:
    - Referencie `NuGet.Config`no arquivo `.gitignore` para que você não confirme credenciais acidentalmente no repositório de origem.
    - Abra o arquivo `azds.yaml` no projeto, localize a seção `build` e insira o snippet de código a seguir para garantir que o arquivo `NuGet.Config` seja sincronizado com o Azure e utilizado durante o processo de compilação da imagem do container. (Por padrão, o Azure Dev Spaces não sincroniza arquivos que correspondam às regras `.gitignore` e `.dockerignore`.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Próximas etapas

Após concluir as etapas acima, na próxima vez em que você executar `azds up` (ou clicar `F5` no VSCode ou Visual Studio), o Azure Dev Spaces sincronizará o arquivo `NuGet.Config` com o Azure, o qual será usado por `dotnet restore` para instalar dependências de pacotes no contêiner.

