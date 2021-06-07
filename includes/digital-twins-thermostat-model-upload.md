---
author: baanders
description: incluir arquivo para carregar um modelo na instância do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950580"
---
O modelo se parece com este:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Para **carregar esse modelo em sua instância do gêmeos**, execute o seguinte comando CLI do Azure, que carrega o modelo acima como JSON embutido. Você pode executar o comando em [Azure cloud Shell](../articles/cloud-shell/overview.md) no navegador ou em seu computador se tiver a CLI [instalada localmente](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> Se você estiver usando Cloud Shell no ambiente do PowerShell, talvez seja necessário escapar os caracteres de aspas nos campos JSON embutidos para que seus valores sejam analisados corretamente. Aqui está o comando para carregar o modelo com essa modificação:
>
> Carregar modelo:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```