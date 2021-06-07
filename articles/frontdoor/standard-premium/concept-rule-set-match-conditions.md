---
title: Configurar condições de correspondência do conjunto de regras padrão/Premium da porta do Azure
description: Este artigo fornece uma lista das várias condições de correspondência disponíveis com o conjunto de regras Standard/Premium do Azure front door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: yuajia
ms.openlocfilehash: 039effb885463c1c53085535a6980601be890340
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561400"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Condições de correspondência do conjunto de regras Standard/Premium (visualização) do Azure front door

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

No [conjunto de regras](concept-rule-set.md)Standard/Premium do Azure front door, uma regra consiste em zero ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência que você pode usar no conjunto de regras Standard/Premium do Azure front door.

A primeira parte de uma regra é uma condição de correspondência ou um conjunto de condições de correspondência. Uma regra pode consistir em até 10 condições de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para as quais as ações definidas são executadas. Se você usar várias condições de correspondência, as condições de correspondência serão agrupadas em conjunto usando a lógica AND. Para todas as condições de correspondência que dão suporte a vários valores, ou a lógica é usada.

Você pode usar uma condição de correspondência para:

* Filtrar solicitações com base em um endereço IP, país ou região específico.
* Filtrar solicitações por informações de cabeçalho.
* Filtrar solicitações de dispositivos móveis ou dispositivos de desktop.
* Filtrar solicitações de nome do arquivo de solicitação e extensão de arquivo.
* Filtrar solicitações da URL de solicitação, protocolo, caminho, Cadeia de caracteres de consulta, postar argumentos, etc.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="device-type"></a><a name="IsDevice"></a> Tipo de dispositivo

Use a condição de correspondência de **tipo de dispositivo** para identificar solicitações que foram feitas de um dispositivo móvel ou dispositivo de desktop.  

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-------|------------------|
| Operador | <ul><li>Na portal do Azure: `Equal` , `Not Equal`</li><li>Em modelos ARM: `Equal` ; use a `negateCondition` propriedade para especificar _não igual_ |
| Valor | `Mobile`, `Desktop` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações que foram detectadas como provenientes de um dispositivo móvel.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="Captura de tela do portal mostrando a condição de correspondência do tipo de dispositivo.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Postar argumentos

Use a condição **post args** match para identificar solicitações com base nos argumentos fornecidos no corpo de uma solicitação post. Uma única condição de correspondência corresponde a um único argumento do corpo da solicitação POST. Você pode especificar vários valores para correspondência, que serão combinados usando ou lógica.

> [!NOTE]
> A condição **postar args** Match funciona com o `application/x-www-form-urlencoded` tipo de conteúdo.

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Postar argumentos | Um valor de cadeia de caracteres que representa o nome do argumento POST. |
| Operador | Qualquer operador da [lista de operadores padrão](#operator-list). |
| Valor | Um ou mais valores de cadeia de caracteres ou inteiros que representam o valor do argumento POST para correspondência. Se vários valores forem especificados, eles serão avaliados usando lógica ou. |
| Transformação de maiúsculas e minúsculas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações POST em que um `customerName` argumento é fornecido no corpo da solicitação e onde o valor de `customerName` começa com a letra `J` ou `K` . Usamos uma transformação de caso para converter os valores de entrada em letras maiúsculas para que os valores que começam com `J` , `j` , `K` e `k` todos sejam correspondidos.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="Captura de tela do portal mostrando a condição de correspondência de argumentos post.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Cadeia de consulta

Use a condição de correspondência de **cadeia de caracteres de consulta** para identificar solicitações que contêm uma cadeia de caracteres de consulta específica. Você pode especificar vários valores para correspondência, que serão combinados usando ou lógica.

> [!NOTE]
> A cadeia de caracteres de consulta inteira é correspondida como uma única cadeia de caracteres, sem a entrelinha `?` .

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão](#operator-list). |
| Cadeia de consulta | Um ou mais valores de cadeia de caracteres ou inteiros que representam o valor da cadeia de caracteres de consulta para correspondência. Não inclua o `?` no início da cadeia de caracteres de consulta. Se vários valores forem especificados, eles serão avaliados usando lógica ou. |
| Transformação de maiúsculas e minúsculas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações em que a cadeia de caracteres de consulta contém a cadeia de caracteres `language=en-US` . Queremos que a condição de correspondência seja diferenciar maiúsculas de minúsculas, portanto, não transformamos o caso.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="Captura de tela do portal mostrando a condição de correspondência da cadeia de consulta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Endereço remoto

A condição de correspondência de **endereço remoto** identifica solicitações com base no local ou endereço IP do solicitante. Você pode especificar vários valores para correspondência, que serão combinados usando ou lógica.

* Use a notação CIDR ao especificar blocos de endereço IP. Isso significa que a sintaxe de um bloco de endereço IP é o endereço IP base seguido por uma barra e o tamanho do prefixo. Por exemplo:
    * **Exemplo de IPv4**: `5.5.5.64/26` corresponde a todas as solicitações que chegam de endereços 5.5.5.64 por meio de 5.5.5.127.
    * **Exemplo de IPv6**: `1:2:3:/48` corresponde a todas as solicitações que chegam de endereços 1:2:3:0:0:0:0:0 a 1:2:3: ffff: ffff: ffff: ffff: ffff.
* Quando você especifica vários endereços IP e blocos de endereço IP, a lógica ' OR ' é aplicada.
    * **Exemplo de IPv4**: se você adicionar dois endereços IP `1.2.3.4` e `10.20.30.40` a condição for correspondida para todas as solicitações que chegam de um endereço 1.2.3.4 ou 10.20.30.40.
    * **Exemplo de IPv6**: se você adicionar dois endereços IP `1:2:3:4:5:6:7:8` e `10:20:30:40:50:60:70:80` a condição for correspondida para todas as solicitações que chegam de um dos endereços 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Operador | <ul><li>Na portal do Azure: `Geo Match` , `Geo Not Match` , `IP Match` ou `IP Not Match`</li><li>Em modelos ARM: `GeoMatch` , `IPMatch` ; use a `negateCondition` propriedade para especificar a _não correspondência geográfica_ ou _IP não correspondente_</li></ul> |
| Valor | <ul><li>Para os `IP Match` `IP Not Match` operadores ou: especifique um ou mais intervalos de endereços IP. Se vários intervalos de endereços IP forem especificados, eles serão avaliados usando lógica ou.</li><li>Para os `Geo Match` `Geo Not Match` operadores ou: especifique um ou mais locais usando seu código de país.</li></ul> |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações em que a solicitação não foi originada da Estados Unidos.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="Captura de tela do portal mostrando a condição de correspondência de endereço remoto.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> Corpo da solicitação

A condição de correspondência do **corpo da solicitação** identifica solicitações com base em um texto específico que aparece no corpo da solicitação. Você pode especificar vários valores para correspondência, que serão combinados usando ou lógica.

> [!NOTE]
> Se um corpo de solicitação exceder 64 KB, somente os primeiros 64 KB serão considerados para a condição de correspondência do **corpo da solicitação** .

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão](#operator-list). |
| Valor | Um ou mais valores de cadeia de caracteres ou inteiros que representam o valor do texto do corpo da solicitação a ser correspondido. Se vários valores forem especificados, eles serão avaliados usando lógica ou. |
| Transformação de maiúsculas e minúsculas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações em que o corpo da solicitação contém a cadeia de caracteres `ERROR` . Podemos transformar o corpo da solicitação em maiúsculas antes de avaliar a correspondência, portanto, `error` e outras variações de caso também dispararão essa condição de correspondência.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="Captura de tela do portal mostrando a condição de correspondência do corpo da solicitação.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> Nome do arquivo de solicitação

A condição de correspondência do **nome do arquivo de solicitação** identifica solicitações que incluem o nome de arquivo especificado na URL da solicitação. Você pode especificar vários valores para correspondência, que serão combinados usando ou lógica.

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão](#operator-list). |
| Valor | Um ou mais valores de cadeia de caracteres ou inteiros que representam o valor do nome do arquivo de solicitação para correspondência. Se vários valores forem especificados, eles serão avaliados usando lógica ou. |
| Transformação de maiúsculas e minúsculas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações em que o nome do arquivo de solicitação é `media.mp4` . Podemos transformar o nome do arquivo em minúsculas antes de avaliar a correspondência, portanto, `MEDIA.MP4` e outras variações de caso também dispararão essa condição de correspondência.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="Captura de tela do portal mostrando condição de correspondência do nome do arquivo de solicitação.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> Extensão do arquivo de solicitação

A condição de correspondência da **extensão do arquivo de solicitação** identifica solicitações que incluem a extensão de arquivo especificada no nome do arquivo na URL da solicitação. Você pode especificar vários valores para correspondência, que serão combinados usando ou lógica.

> [!NOTE]
> Não inclua um ponto à esquerda. Por exemplo, use `html` ao invés de `.html`.

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão](#operator-list). |
| Valor | Um ou mais valores de cadeia de caracteres ou inteiros que representam o valor da extensão do arquivo de solicitação para correspondência. Não inclua um ponto à esquerda. Se vários valores forem especificados, eles serão avaliados usando lógica ou. |
| Transformação de maiúsculas e minúsculas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações em que a extensão do arquivo de solicitação é `pdf` ou `docx` . Podemos transformar a extensão do arquivo de solicitação em minúsculas antes de avaliar a correspondência, portanto `PDF` , `DocX` e outras variações de caso também dispararão essa condição de correspondência.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="Captura de tela do portal mostrando condição de correspondência da extensão do arquivo de solicitação.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> Cabeçalho da solicitação

A condição de correspondência do **cabeçalho de solicitação** identifica solicitações que incluem um cabeçalho específico na solicitação. Você pode usar essa condição de correspondência para verificar se um cabeçalho existe qualquer valor ou para verificar se o cabeçalho corresponde a um valor especificado. Você pode especificar vários valores para correspondência, que serão combinados usando ou lógica.

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Nome do cabeçalho | Um valor de cadeia de caracteres que representa o nome do argumento POST. |
| Operador | Qualquer operador da [lista de operadores padrão](#operator-list). |
| Valor | Um ou mais valores de cadeia de caracteres ou inteiros que representam o valor do cabeçalho de solicitação para correspondência. Se vários valores forem especificados, eles serão avaliados usando lógica ou. |
| Transformação de maiúsculas e minúsculas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações em que a solicitação contém um cabeçalho chamado `MyCustomHeader` , independentemente de seu valor.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="Captura de tela do portal mostrando a condição de correspondência do cabeçalho de solicitação.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Método de solicitação

A condição de correspondência do **método de solicitação** identifica solicitações que usam o método de solicitação HTTP especificado. Você pode especificar vários valores para correspondência, que serão combinados usando ou lógica.

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Operador | <ul><li>Na portal do Azure: `Equal` , `Not Equal`</li><li>Em modelos ARM: `Equal` ; use a `negateCondition` propriedade para especificar _não igual_ |
| Método de solicitação | Um ou mais métodos HTTP de: `GET` , `POST` , `PUT` , `DELETE` , `HEAD` , `OPTIONS` , `TRACE` . Se vários valores forem especificados, eles serão avaliados usando lógica ou. |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações em que a solicitação usa o `DELETE` método.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="Captura de tela do portal mostrando a condição de correspondência do método de solicitação.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> Caminho da solicitação

A condição de correspondência do **caminho de solicitação** identifica solicitações que incluem o caminho especificado na URL da solicitação. Você pode especificar vários valores para correspondência, que serão combinados usando ou lógica.

> [!NOTE]
> O caminho é a parte da URL após o nome do host e uma barra. Por exemplo, na URL `https://www.contoso.com/files/secure/file1.pdf` , o caminho é `files/secure/file1.pdf` .

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão](#operator-list). |
| Valor | Um ou mais valores de cadeia de caracteres ou inteiros que representam o valor do caminho de solicitação para correspondência. Não inclua a barra à esquerda. Se vários valores forem especificados, eles serão avaliados usando lógica ou. |
| Transformação de maiúsculas e minúsculas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações nas quais o caminho do arquivo de solicitação começa `files/secure/` . Podemos transformar a extensão do arquivo de solicitação em minúsculas antes de avaliar a correspondência, portanto, as solicitações para `files/SECURE/` e outras variações de caso também dispararão essa condição de correspondência.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="Captura de tela do portal mostrando a condição de correspondência do caminho de solicitação.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> Protocolo de solicitação

A condição de correspondência de **protocolo de solicitação** identifica solicitações que usam o protocolo especificado (http ou HTTPS).

> [!NOTE]
> Às vezes, o *protocolo* também é chamado de *esquema*.

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Operador | <ul><li>Na portal do Azure: `Equal` , `Not Equal`</li><li>Em modelos ARM: `Equal` ; use a `negateCondition` propriedade para especificar _não igual_ |
| Método de solicitação | `HTTP`, `HTTPS` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações em que a solicitação usa o `HTTP` protocolo.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="Captura de tela do portal mostrando condição de correspondência de protocolo de solicitação.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> URL da solicitação

Identifica as solicitações que correspondem à URL especificada. A URL inteira é avaliada. Você pode especificar vários valores para correspondência, que serão combinados usando ou lógica.

> [!TIP]
> Ao usar essa condição de regra, certifique-se de incluir o protocolo. Por exemplo, use `https://www.contoso.com` em vez de apenas `www.contoso.com` .

### <a name="properties"></a>Propriedades

| Propriedade | Valores com suporte |
|-|-|
| Operador | Qualquer operador da [lista de operadores padrão](#operator-list). |
| Valor | Um ou mais valores de cadeia de caracteres ou inteiros que representam o valor da URL de solicitação para correspondência. Se vários valores forem especificados, eles serão avaliados usando lógica ou. |
| Transformação de maiúsculas e minúsculas | `Lowercase`, `Uppercase` |

### <a name="example"></a>Exemplo

Neste exemplo, correspondemos a todas as solicitações nas quais a URL de solicitação começa `https://api.contoso.com/customers/123` . Podemos transformar a extensão do arquivo de solicitação em minúsculas antes de avaliar a correspondência, portanto, as solicitações para `https://api.contoso.com/Customers/123` e outras variações de caso também dispararão essa condição de correspondência.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="Captura de tela do portal mostrando a condição de correspondência de URL de solicitação.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> Lista de operadores

Para regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

| Operador                   | Descrição                                                                                                                    | Suporte ao modelo ARM                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Qualquer                        | Corresponde quando há qualquer valor, independentemente do que for.                                                                     | `operator`: `Any`                                               |
| Igual                      | Corresponde quando o valor corresponde exatamente à cadeia de caracteres especificada.                                                                   | `operator`: `Equal`                                             |
| Contém                   | Corresponde quando o valor contém a cadeia de caracteres especificada.                                                                          | `operator`: `Contains`                                          |
| Menor que                  | Corresponde quando o comprimento do valor é menor que o inteiro especificado.                                                       | `operator`: `LessThan`                                          |
| Maior que               | Corresponde quando o comprimento do valor é maior que o inteiro especificado.                                                    | `operator`: `GreaterThan`                                       |
| Menor ou igual a         | Corresponde quando o comprimento do valor é menor ou igual ao inteiro especificado.                                           | `operator`: `LessThanOrEqual`                                   |
| Maior ou igual a      | Corresponde quando o comprimento do valor é maior ou igual ao inteiro especificado.                                        | `operator`: `GreaterThanOrEqual`                                |
| Começa Com                | Corresponde quando o valor começa com a cadeia de caracteres especificada.                                                                       | `operator`: `BeginsWith`                                        |
| Termina com                  | Corresponde quando o valor termina com a cadeia de caracteres especificada.                                                                         | `operator`: `EndsWith`                                          |
| RegEx                      | Corresponde quando o valor corresponde à expressão regular especificada. [Consulte abaixo para obter mais detalhes.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Não qualquer                    | Corresponde quando não há valor.                                                                                                | `operator`: `Any` e `negateCondition` : `true`                |
| Diferente                  | Corresponde quando o valor não corresponde à cadeia de caracteres especificada.                                                                    | `operator`: `Equal` e `negateCondition` : `true`              |
| Não contém               | Corresponde quando o valor não contém a cadeia de caracteres especificada.                                                                  | `operator`: `Contains` e `negateCondition` : `true`           |
| Não é menor que              | Corresponde quando o comprimento do valor não é menor que o inteiro especificado.                                                   | `operator`: `LessThan` e `negateCondition` : `true`           |
| Não é maior que           | Corresponde quando o comprimento do valor não é maior que o inteiro especificado.                                                | `operator`: `GreaterThan` e `negateCondition` : `true`        |
| Não é menor ou igual a     | Corresponde quando o comprimento do valor não é menor ou igual ao inteiro especificado.                                       | `operator`: `LessThanOrEqual` e `negateCondition` : `true`    |
| Não é maior ou igual a | Corresponde quando o comprimento do valor não é maior ou igual ao inteiro especificado.                                    | `operator`: `GreaterThanOrEqual` e `negateCondition` : `true` |
| Não começa com            | Corresponde quando o valor não começa com a cadeia de caracteres especificada.                                                               | `operator`: `BeginsWith` e `negateCondition` : `true`         |
| Não termina com              | Corresponde quando o valor não termina com a cadeia de caracteres especificada.                                                                 | `operator`: `EndsWith` e `negateCondition` : `true`           |
| Não RegEx                  | Corresponde quando o valor não corresponde à expressão regular especificada. [Consulte abaixo para obter mais detalhes.](#regular-expressions) | `operator`: `RegEx` e `negateCondition` : `true`              |

> [!TIP]
> Para operadores numéricos como *Menor que* e *Maior ou igual a*, a comparação usada é baseada no comprimento. O valor na condição de correspondência deve ser um inteiro que especifica o comprimento que você deseja comparar.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> Expressões regulares

As expressões regulares não dão suporte às seguintes operações:

* Referências anteriores e captura de subexpressãos.
* Asserções de largura zero arbitrárias.
* Referências de sub-rotinas e padrões recursivos.
* Padrões condicionais.
* Verbos de controle de retrocesso.
* A `\C` diretiva de byte único.
* A `\R` diretiva de correspondência de nova linha.
* A `\K` diretiva de início de redefinição de correspondência.
* Textos explicativos e código inserido.
* Agrupamento atômico e quantificadores Possessive.

## <a name="arm-template-support"></a>Suporte ao modelo ARM

Os conjuntos de regras podem ser configurados usando modelos de Azure Resource Manager. [Consulte um modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set). Você pode adicionar condições de correspondência usando os trechos de código JSON ou bicep incluídos nos exemplos acima.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [conjunto de regras](concept-rule-set.md).
* Saiba como [configurar seu primeiro conjunto de regras](how-to-configure-rule-set.md).
* Saiba mais sobre as [ações do conjunto de regras](concept-rule-set-actions.md).
