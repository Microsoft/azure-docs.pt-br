---
title: API REST de pesquisa de log do Log Analytics | Microsoft Docs
description: "Este guia fornece um tutorial básico que descreve como você pode usar a API REST da Pesquisa do Log Analytics no OMS (Operations Management Suite) e fornece exemplos que mostram como usar os comandos."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: bwren
ms.openlocfilehash: 5b51c6fcc69c8dff6579a1a1221e88822eccc1a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-log-search-rest-api"></a>API REST de pesquisa de log do Log Analytics
Este guia fornece um tutorial básico, incluindo exemplos de como você pode usar a API REST do Log Analytics Search. O Log Analytics faz parte do OMS (Operations Management Suite).

> [!NOTE]
> Se o espaço de trabalho foi atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), você deverá consultar a [documentação da nova versão da API de pesquisa de logs](https://dev.loganalytics.io/).

> [!NOTE]
> O Log Analytics chamava-se Operational Insights e é por isso que esse é o nome usado nos no provedor de recursos.
>
>

## <a name="overview-of-the-log-search-rest-api"></a>Visão geral da API REST de pesquisa de log
A API REST de Pesquisa do Log Analytics é RESTful e pode ser acessada por meio da API do Azure Resource Manager. Este artigo fornece exemplo de como acessar a API por meio do [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API do Azure Resource Manager. O uso do ARMClient é uma das muitas opções para acessar a API do Log Analytics Search. Outra opção é usar o módulo do Azure PowerShell para OperationalInsights, que inclui cmdlets para acessar a pesquisa. Com essas ferramentas, você pode utilizar a API do Azure Resource Manager para fazer chamadas aos espaços de trabalho do OMS e executar comandos de pesquisa dentro deles. A API produz resultados da pesquisa no formato JSON, permitindo que você use os resultados da pesquisa de diferentes maneiras por meio de programação.

O Azure Resource Manager pode ser usado por meio de uma [Biblioteca para .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx), bem como por meio da [API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Para saber mais, leia as páginas da Web vinculadas.

> [!NOTE]
> Se você usar um comando de agregação como `|measure count()` ou `distinct`, cada chamada de pesquisa poderá retornar até 500.000 registros. Pesquisas que não incluem um comando de agregação retornam até 5.000 registros.
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Tutorial básico da API REST de Pesquisa do Log Analytics
### <a name="to-use-armclient"></a>Para usar o ARMClient
1. Instale o [Chocolatey](https://chocolatey.org/), que é um gerenciador de pacotes de software livre para Windows. Abra uma janela do prompt de comando como administrador e execute o seguinte comando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. Instale o ARMClient executando o seguinte comando:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>Para realizar uma pesquisa usando o ARMClient
1. Entre usando a conta da Microsoft ou sua conta corporativa ou de estudante:

    ```
    armclient login
    ```

    Um logon bem-sucedido lista todas as assinaturas vinculadas à conta especificada:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. Obtenha os espaços de trabalho do Operations Management Suite:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Uma chamada Get bem-sucedida geraria todos os espaços de trabalho associados à assinatura:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Crie sua variável de pesquisa:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Pesquise usando sua nova variável de pesquisa:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Exemplos de referência da API REST de Pesquisa do Log Analytics
Os exemplos a seguir mostram como você pode usar a API de pesquisa.

### <a name="search---actionread"></a>Pesquisa - ação/leitura
**Url de exemplo:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Solicitação:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
A tabela a seguir descreve as propriedades que estão disponíveis.

| **Propriedade** | **Descrição** |
| --- | --- |
| top |O número máximo de resultados a serem retornados. |
| highlight |Contém parâmetros anteriores e subsequentes, geralmente usados para realçar campos correspondentes |
| pre |Usado como prefixo da cadeia de caracteres específica para os campos correspondentes. |
| post |Acrescenta a cadeia de caracteres específica para os campos correspondentes. |
| query |A consulta de pesquisa usada para coletar e retornar os resultados. |
| iniciar |O início da janela de tempo em que você deseja que os resultados sejam encontrados. |
| end |O fim da janela de tempo em que você deseja que os resultados sejam encontrados. |

**Resposta:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Pesquisa/{ID} - ação/leitura
**Solicite o conteúdo de uma pesquisa salva:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> Se a pesquisa retornar com um status 'Pendente', a sondagem dos resultados atualizados poderá ser feita por essa API. Após seis minutos, o resultado da pesquisa será eliminado do cache e HTTP Gone será retornado. Se a solicitação inicial de pesquisa retornar um status “Bem-sucedido” imediatamente, os resultados não serão adicionados ao cache, fazendo essa API retornar HTTP Gone se consultada. O conteúdo de um resultado de HTTP 200 estará no mesmo formato que a solicitação inicial de pesquisa, apenas com valores atualizados.
>
>

### <a name="saved-searches"></a>Pesquisas salvas
**Solicite a lista de pesquisas salvas:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Métodos com suporte: GET, PUT e DELETE

Métodos de coleção com suporte: GET

A tabela a seguir descreve as propriedades que estão disponíveis.

| Propriedade | Descrição |
| --- | --- |
| ID |O identificador exclusivo. |
| Etag |**Obrigatório para o Patch**. Atualizado pelo servidor em cada gravação. O valor deve ser igual ao valor armazenado atual ou '*' para atualizar. 409 retornado para valores antigos ou inválidos. |
| properties.query |**Obrigatório**. A consulta de pesquisa. |
| properties.displayName |**Obrigatório**. O nome de exibição da consulta definido pelo usuário. |
| properties.category |**Obrigatório**. A categoria definida pelo usuário para a consulta. |

> [!NOTE]
> A API de pesquisa do Log Analytics atualmente retorna pesquisas salvas criadas pelo usuário quando sondada para pesquisas salvas em um espaço de trabalho. A API não retorna pesquisas salvas fornecidas por soluções.
>
>

### <a name="create-saved-searches"></a>Criar pesquisas salvas
**Solicitação:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisismyid?api-version=2015-03-20 $savedSearchParametersJson
```

> [!NOTE]
> O nome para todas as pesquisas, agendas e ações salvas criadas com a API do Log Analytics deve estar em letras minúsculas.

### <a name="delete-saved-searches"></a>Excluir pesquisas salvas
**Solicitação:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Atualizar pesquisas salvas
 **Solicitação:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Metadados - somente JSON
Eis aqui uma maneira de ver os campos para todos os tipos de log para os dados coletados em seu espaço de trabalho. Por exemplo, se você quiser saber se o tipo de Evento tiver um campo chamado Computador, essa consulta será uma maneira de verificar.

**Solicitação por campos:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Resposta:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

A tabela a seguir descreve as propriedades que estão disponíveis.

| **Propriedade** | **Descrição** |
| --- | --- |
| name |Nome do campo. |
| displayName |O nome de exibição do campo. |
| type |O tipo do valor do campo. |
| facetable |Combinação das propriedades 'indexed', 'stored' e 'facet' atuais. |
| display |Propriedade 'display' atual. True se o campo está visível na pesquisa. |
| ownerType |Reduzido a somente aqueles tipos que pertencem a endereços IP incorporados. |

## <a name="optional-parameters"></a>Parâmetros opcionais
As informações a seguir descrevem os parâmetros opcionais disponíveis.

### <a name="highlighting"></a>Realce
O parâmetro "Highlight" é um parâmetro opcional que você pode usar para solicitar que o subsistema de pesquisa inclua um conjunto de marcadores em sua resposta.

Esses marcadores indicam o início e término do texto realçado que coincide com os termos fornecidos na consulta de pesquisa.
Você pode especificar os marcadores de início e término que serão usados pela pesquisa para encapsular o termo realçado.

**Exemplo de consulta de pesquisa**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Resultado de exemplo:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Observe que o resultado anterior contém uma mensagem de erro que foi acrescida de um prefixo e anexada.

## <a name="computer-groups"></a>Grupos de computadores
Grupos de computadores são pesquisas especiais salvas que retornam um conjunto de computadores.  Você pode usar um grupo de computadores em outras consultas para limitar os resultados para os computadores no grupo.  Um grupo de computadores é implementado como uma pesquisa salva com uma marca de Grupo com um valor de Computador.

A seguir está um exemplo de resposta para um grupo de computadores.

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>Recuperando grupos de computadores
Para recuperar um grupo de computadores, use o método Get com a ID do grupo.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Criar ou atualizar um grupo de computadores
Para criar um grupo de computadores, use o método Put com uma ID de pesquisa salva exclusiva. Se você usar uma ID de grupo de computador existente, ela será modificada. Quando você cria um grupo de computadores no portal do Log Analytics, a ID é criada por meio do grupo e do nome.

A consulta usada para a definição de grupo deve retornar um conjunto de computadores para o grupo funcionar corretamente.  É recomendável terminar sua consulta com `| Distinct Computer` para garantir que os dados corretos sejam retornados.

A definição da pesquisa salva deve incluir uma marca chamada Grupo com um valor de Computador para a pesquisa ser classificada como um grupo de computadores.

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>Excluindo grupos de computadores
Para excluir um grupo de computadores, use o método Delete com a ID do grupo.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para criar consultas usando campos personalizados para os critérios.
