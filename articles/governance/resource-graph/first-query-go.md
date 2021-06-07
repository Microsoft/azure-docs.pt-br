---
title: 'Início Rápido: Sua primeira consulta Go'
description: Neste início rápido, você seguirá as etapas para habilitar o pacote do Resource Graph para Go e executará sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 4949801b3dc97904680d09e685fd225812a0e14d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920055"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>Início Rápido: Execute sua primeira consulta ao Resource Graph usando o Go

A primeira etapa para usar o Azure Resource Graph é verificar se os pacotes necessários para Go estão instalados. Este início rápido orienta você no processo de adição dos pacotes à instalação do Go.

No fim desse processo, você terá adicionado os pacotes à sua instalação do Go e executará a primeira consulta ao Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="add-the-resource-graph-package"></a>Adicionar o pacote do Resource Graph

Para habilitar o Go para consultar o Azure Resource Graph, o pacote deve ser adicionado. Esse pacote funciona em qualquer lugar em que o Go possa ser usado, incluindo o [bash no Windows 10](/windows/wsl/install-win10) ou instalado localmente.

1. Verifique se o Go mais recente está instalado (pelo menos a versão **1.14**). Se ele ainda não tiver sido instalado, baixe-o em [Golang.org](https://golang.org/dl/).

1. Verifique se a CLI do Azure mais recente está instalada (pelo menos a versão **2.5.1**). Se ela ainda não tiver sido instalada, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > A CLI do Azure é necessária para habilitar o Go para usar o método `auth.NewAuthorizerFromCLI()` no exemplo a seguir. Para obter informações sobre outras opções, confira [SDK do Azure para linguagem Go – mais detalhes de autenticação](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Autentique-se por meio da CLI do Azure.

   ```azurecli
   az login
   ```

1. No ambiente Go de sua escolha, instale os pacotes necessários para o Azure Resource Graph:

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Com os pacotes Go adicionados ao ambiente de sua escolha, é hora de experimentar uma consulta simples ao Resource Graph. A consulta retorna os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de Recurso** de cada um.

1. Crie o aplicativo Go e salve a seguinte fonte como `argQuery.go`:

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. Compilar o aplicativo Go:

   ```bash
   go build argQuery.go
   ```

1. execute sua primeira consulta do Azure Resource Graph usando o aplicativo Go compilado. Substitua `<SubID>` pela ID da assinatura:

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > Devido a essa consulta de exemplo não fornecer um modificador de classificação, tal como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Altere o primeiro parâmetro para `argQuery` e altere a consulta para `order by` a propriedade **Name**. Substitua `<SubID>` pela ID da assinatura:

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Essa ordem de comando limita primeiro os resultados da consulta e os ordena.

1. Altere o primeiro parâmetro para `argQuery` e altere a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados. Substitua `<SubID>` pela ID da assinatura:

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente esteja sendo alterado, os resultados retornados serão consistentes e ordenados segundo a propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover os pacotes instalados do seu ambiente do Go, você poderá fazer isso usando o seguinte comando:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você adicionou os pacotes do Resource Graph ao seu ambiente do Go e executou sua primeira consulta. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)