---
ms.openlocfilehash: b1be5e903994f0a2c4a3d457f0c2bb5572a889c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663022"
---
## <a name="add-secret-manager"></a>Adicionar o Gerenciador de Segredos

Uma ferramenta chamada Gerenciador de Segredos armazena dados confidenciais para o trabalho de desenvolvimento fora da árvore do seu projeto. Essa abordagem ajuda a impedir o compartilhamento acidental de segredos do aplicativo no código-fonte. Conclua as seguintes etapas para habilitar o uso do Gerenciador de Segredos no projeto do ASP.NET Core:

#### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

Navegue até o diretório raiz do projeto e execute o seguinte comando para habilitar o armazenamento de segredos no projeto:

```dotnetcli
dotnet user-secrets init
```

Um elemento `UserSecretsId` contendo um GUID é adicionado ao arquivo *.csproj*:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

Navegue até o diretório raiz do projeto e execute o seguinte comando para habilitar o armazenamento de segredos no projeto:

```dotnetcli
dotnet user-secrets init
```

Um elemento `UserSecretsId` contendo um GUID é adicionado ao arquivo *.csproj*:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

1. Abra o arquivo *.csproj*.

1. Adicione um elemento `UserSecretsId` ao arquivo *.csproj*, como mostrado aqui. Use o mesmo GUID ou substitua esse valor pelo seu próprio.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. Salve o arquivo *.csproj*.

---

> [!TIP]
> Para saber mais sobre o Gerenciador de Segredos, confira [Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core](/aspnet/core/security/app-secrets).
