---
title: Usar o tipo de conteúdo JSON para pares chave-valor
titleSuffix: Azure App Configuration
description: Saiba como usar o tipo de conteúdo JSON para valores de chave
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: ee262c0eb2431085e71d8ee0035bcdab9833d1cf
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565765"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>Aproveitar o tipo de conteúdo para armazenar valores de chave JSON na configuração do aplicativo

Os dados são armazenados na configuração do aplicativo como valores de chave, onde os valores são tratados como o tipo de cadeia de caracteres por padrão. No entanto, você pode especificar um tipo personalizado aproveitando a propriedade Content-Type associada a cada chave-valor, para que você possa preservar o tipo original de seus dados ou fazer com que seu aplicativo se comporte de forma diferente com base no tipo de conteúdo.


## <a name="overview"></a>Visão geral

Em configuração de aplicativo, você pode usar o tipo de mídia JSON como o tipo de conteúdo de seus valores de chave para obter benefícios semelhantes, como:
- **Gerenciamento de dados mais simples** : o gerenciamento de valores-chave, como matrizes, se tornará muito mais fácil na portal do Azure.
- **Exportação de dados aprimorada** : tipos primitivos, matrizes e objetos JSON serão preservados durante a exportação de dados.
- **Suporte nativo com o provedor de configuração de aplicativo** : valores de chave com tipo de conteúdo JSON funcionarão bem quando consumidos pelas bibliotecas de provedor de configuração de aplicativo em seus aplicativos.

#### <a name="valid-json-content-type"></a>Tipo de conteúdo JSON válido

Os tipos de mídia, conforme definido [aqui](https://www.iana.org/assignments/media-types/media-types.xhtml), podem ser atribuídos ao tipo de conteúdo associado a cada chave-valor.
Um tipo de mídia consiste em um tipo e um subtipo. Se o tipo for `application` e o subtipo (ou sufixo) for `json` , o tipo de mídia será considerado um tipo de conteúdo JSON válido.
Alguns exemplos de tipos de conteúdo JSON válidos são:

- aplicativo/json
- aplicativo/atividade + JSON
- application/vnd. foobar + JSON; charset = utf-8

#### <a name="valid-json-values"></a>Valores JSON válidos

Quando um valor de chave tem um tipo de conteúdo JSON, seu valor deve estar em um formato JSON válido para os clientes processá-lo corretamente. Caso contrário, os clientes podem falhar ou fazer fallback e tratá-lo como formato de cadeia de caracteres.
Alguns exemplos de valores JSON válidos são:

- "John Doe"
- 723
- false
- null
- "2020-01-01T12:34:56.789 Z"
- [1, 2, 3, 4]
- {"Objectsetting": {"direcionamento": {"default": true, "Level": "Information"}}}

> [!NOTE]
> Para o restante deste artigo, qualquer valor de chave na configuração de aplicativo que tenha um tipo de conteúdo JSON válido e um valor JSON válido será referido como **chave-valor JSON**. 

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar valores de chave JSON na configuração do aplicativo.
> * Importar valores de chave JSON de um arquivo JSON.
> * Exportar valores de chave JSON para um arquivo JSON.
> * Consumir valores de chave JSON em seus aplicativos.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requer a versão 2.10.0 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>Criar valores de chave JSON na configuração do aplicativo

Chave JSON-os valores podem ser criados usando portal do Azure, CLI do Azure ou importando de um arquivo JSON. Nesta seção, você encontrará instruções sobre como criar os mesmos valores de chave JSON usando todos os três métodos.

### <a name="create-json-key-values-using-azure-portal"></a>Criar valores de chave JSON usando portal do Azure

Navegue até o repositório de configuração do aplicativo e selecione **Configuration Explorer**  >  **criar**  >  **chave-valor** para adicionar os seguintes pares chave-valor:

| Chave | Valor | Tipo de conteúdo |
|---|---|---|
| Configurações: BackgroundColor | Amarela | aplicativo/json |
| Configurações: FontSize | 24 | aplicativo/json |
| Configurações: UseDefaultRouting | false | aplicativo/json |
| Configurações: BlockedUsers | null | aplicativo/json |
| Configurações: liberadas | "2020-08-04T12:34:56.789 Z" | aplicativo/json |
| Configurações: RolloutPercentage | [25, 50, 75100] | aplicativo/json |
| Configurações: registro em log | {"Test": {"Level": "debug"}, "Prod": {"nível": "Warning"}} | aplicativo/json |

Deixe o **rótulo** vazio e selecione **aplicar**.

### <a name="create-json-key-values-using-azure-cli"></a>Criar valores de chave JSON usando CLI do Azure

Os comandos a seguir criarão valores de chave JSON em seu repositório de configuração de aplicativo. Substitua `<appconfig_name>` pelo nome do repositório de configuração do aplicativo.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Se você estiver usando CLI do Azure ou Azure Cloud Shell para criar valores de chave JSON, o valor fornecido deverá ser uma cadeia de caracteres JSON de escape.

### <a name="import-json-key-values-from-a-file"></a>Importar valores de chave JSON de um arquivo

Crie um arquivo JSON chamado `Import.json` com o conteúdo a seguir e importe-o como valores de chave na configuração do aplicativo:

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> O `--depth` argumento é usado para mesclar dados hierárquicos de um arquivo em pares de chave-valor. Neste tutorial, a profundidade é especificada para demonstrar que você também pode armazenar objetos JSON como valores na configuração do aplicativo. Se Depth não for especificado, os objetos JSON serão achatados para o nível mais profundo por padrão.

Os valores de chave JSON que você criou devem ter a seguinte aparência na configuração do aplicativo:

![Repositório de configuração contendo valores de chave JSON](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>Exportar valores de chave JSON para um arquivo

Um dos principais benefícios do uso de valores de chave JSON é a capacidade de preservar o tipo de dados original de seus valores durante a exportação. Se um valor de chave na configuração de aplicativo não tiver tipo de conteúdo JSON, seu valor será tratado como cadeia de caracteres. 

Considere estes valores-chave sem o tipo de conteúdo JSON:

| Chave | Valor | Tipo de conteúdo |
|---|---|---|
| Configurações: FontSize | 24 | |
| Configurações: UseDefaultRouting | false | |

Quando você exporta esses valores de chave para um arquivo JSON, os valores serão exportados como cadeias de caracteres:
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

No entanto, quando você exporta valores de chave JSON para um arquivo, todos os valores preservarão seu tipo de dados original. Para verificar isso, exporte os valores de chave de sua configuração de aplicativo para um arquivo JSON. Você verá que o arquivo exportado tem o mesmo conteúdo que o `Import.json` arquivo que você importou anteriormente.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> Se o repositório de configurações de seu aplicativo tiver alguns valores de chave sem tipo de conteúdo JSON, eles também serão exportados para o mesmo arquivo no formato de cadeia de caracteres. Se você quiser exportar apenas os valores de chave JSON, atribua um rótulo ou prefixo exclusivo aos valores de chave JSON e use a filtragem de rótulo ou prefixo durante a exportação.


## <a name="consuming-json-key-values-in-applications"></a>Consumindo valores de chave JSON em aplicativos

A maneira mais fácil de consumir valores de chave JSON em seu aplicativo é por meio de bibliotecas de provedor de configuração de aplicativo. Com as bibliotecas de provedor, você não precisa implementar o tratamento especial de valores de chave JSON em seu aplicativo. Eles são sempre desserializados para seu aplicativo da mesma maneira que outras bibliotecas de provedor de configuração JSON. 

> [!Important]
> O suporte nativo para valores de chave JSON está disponível no provedor de configuração do .NET versão 4.0.0 (ou posterior). Consulte a seção [*próximas etapas*](#next-steps) para obter mais detalhes.

Se você estiver usando o SDK ou a API REST para ler valores-chave da configuração do aplicativo, com base no tipo de conteúdo, seu aplicativo será responsável por desserializar o valor de uma chave JSON-valor usando qualquer desserializador JSON padrão.


## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como trabalhar com valores de chave JSON em seu repositório de configuração de aplicativo, crie um aplicativo para consumir esses valores de chave:

* [Início Rápido do ASP.NET Core](./quickstart-aspnet-core-app.md)
    * Pré-requisito: [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) Package v 4.0.0 ou posterior.

* [Início Rápido do .NET Core](./quickstart-dotnet-core-app.md)
    * Pré-requisito: [Microsoft.Extensions.Configuração. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) pacote v 4.0.0 ou posterior.
