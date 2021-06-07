---
title: 'Início rápido: Criar um grupo de gerenciamento com o Go'
description: Neste guia de início rápido, você usará o Go para criar um grupo de gerenciamento a fim de organizar seus recursos em uma hierarquia de recursos.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: bf2d2c556cfd6ada6d31fc6ee797888ed0899573
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091439"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Início rápido: Criar um grupo de gerenciamento com o Go

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído. Para obter mais informações, confira [Configuração inicial dos grupos de gerenciamento](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Uma entidade de serviço do Azure, incluindo _clientId_ e _clientSecret_. Se você não tiver uma entidade de serviço para uso com o Azure Policy ou quiser criar uma, confira [Bibliotecas de gerenciamento do Azure para autenticação do .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ignore a etapa para instalar os pacotes do .NET Core, pois faremos isso nas próximas etapas.

- Qualquer usuário do Azure AD no locatário poderá criar um grupo de gerenciamento sem ter recebido a permissão de gravação do grupo de gerenciamento se a [proteção de hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver habilitada. Esse novo grupo de gerenciamento passa a ser um filho do grupo de gerenciamento raiz ou o [grupo de gerenciamento padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group), e o criador recebe uma atribuição de função "Proprietário". O serviço de grupo de gerenciamento permite essa capacidade para que as atribuições de função não sejam necessárias no nível raiz. Nenhum usuário tem acesso ao grupo de gerenciamento raiz quando ele é criado. Para evitar problemas de localização dos administradores globais do Azure AD para começar a usar os grupos de gerenciamento, permitimos a criação dos grupos de gerenciamento iniciais no nível raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>Adicionar o pacote de grupo de gerenciamento

A fim de habilitar o Go para gerenciar grupos de gerenciamento, o pacote precisa ser adicionado. Esse pacote funciona em qualquer lugar em que o Go possa ser usado, incluindo o [bash no Windows 10](/windows/wsl/install-win10) ou instalado localmente.

1. Verifique se o Go mais recente está instalado (no mínimo, a versão **1.15**). Se ele ainda não tiver sido instalado, baixe-o em [Golang.org](https://golang.org/dl/).

1. Verifique se a CLI do Azure mais recente está instalada (pelo menos a versão **2.5.1**). Se ela ainda não tiver sido instalada, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > A CLI do Azure é necessária para habilitar o Go para usar o método `auth.NewAuthorizerFromCLI()` no exemplo a seguir. Para obter informações sobre outras opções, confira [SDK do Azure para linguagem Go – mais detalhes de autenticação](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Autentique-se por meio da CLI do Azure.

   ```azurecli
   az login
   ```

1. No ambiente do Go de sua escolha, instale os pacotes necessários para os grupos de gerenciamento:

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Instalação do aplicativo

Com os pacotes Go adicionados ao ambiente escolhido, é hora de configurar o aplicativo Go que pode criar um grupo de gerenciamento.

1. Crie o aplicativo Go e salve a seguinte fonte como `mgCreate.go`:

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Compilar o aplicativo Go:

   ```bash
   go build mgCreate.go
   ```

1. Crie um grupo de gerenciamento usando o aplicativo Go compilado. Substitua `<Name>` pelo nome do seu grupo de gerenciamento:

   ```bash
   mgCreate "<Name>"
   ```

O resultado é um novo grupo de gerenciamento no grupo de gerenciamento raiz.

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover os pacotes instalados do seu ambiente do Go, você poderá fazer isso usando o seguinte comando:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um grupo de gerenciamento para organizar sua hierarquia de recursos. O grupo de gerenciamento pode conter assinaturas ou outros grupos de gerenciamento.

Para saber mais sobre grupos de gerenciamento e como gerenciar sua hierarquia de recursos, prossiga para:

> [!div class="nextstepaction"]
> [Gerenciar seus recursos com grupos de gerenciamento](./manage.md)