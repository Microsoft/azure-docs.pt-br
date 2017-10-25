---
title: Entender a linguagem de consulta do Hub IoT do Azure | Microsoft Azure
description: "Guia do desenvolvedor – descrição da linguagem de consulta do Hub IoT semelhante a SQL, usada para recuperar informações sobre dispositivos gêmeos e trabalhos do seu Hub IoT."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/17
ms.author: elioda
ms.openlocfilehash: a7650104eda58923558892f6f0f6666d16dbce28
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="reference---iot-hub-query-language-for-device-twins-jobs-and-message-routing"></a>Referência – linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens

O Hub IoT fornece uma linguagem avançada semelhante à SQL para recuperação de informações sobre [dispositivos gêmeos][lnk-twins] e [trabalhos][lnk-jobs] e [encaminhamento de mensagens][lnk-devguide-messaging-routes]. Este artigo apresenta:

* Uma introdução aos principais recursos da linguagem de consulta do Hub IoT e
* Uma descrição mais detalhada da linguagem.

## <a name="get-started-with-device-twin-queries"></a>Introdução às consultas com dispositivos gêmeos
Os [dispositivos gêmeos][lnk-twins] podem conter objetos JSON arbitrários como tags e propriedades. O Hub IoT permite consultar dispositivos gêmeos como um único documento JSON que contém todas as informações do dispositivo gêmeo.
Por exemplo, suponha que seus dispositivos gêmeos do Hub IoT tenham a seguinte estrutura:

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

O Hub IoT expõe os dispositivos gêmeos como uma coleção de documentos chamada **dispositivos**.
Então, a consulta a seguir recupera o conjunto completo de dispositivos gêmeos:

```sql
SELECT * FROM devices
```

> [!NOTE]
> Os [SDKs do Hub IoT][lnk-hub-sdks] dão suporte à paginação de resultados grandes.

O Hub IoT permite a você recuperar a filtragem de dispositivos gêmeos com condições arbitrárias. Por exemplo,

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

recupera os dispositivos gêmeos com a tag **location.region** definida como **US**.
Os operadores boolianos e as comparações aritméticas também têm suporte, por exemplo,

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

recupera todos os dispositivos gêmeos localizados nos Estados Unidos configurados para enviar telemetria com menos frequência do que a cada minuto. Como uma conveniência, também é possível usar constantes de matriz com os operadores **IN** e **NIN** (não in). Por exemplo,

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

recupera todos os dispositivos gêmeos que relataram conectividade WiFi ou com fio. Normalmente, é necessário identificar todos os dispositivos gêmeos que contêm uma propriedade específica. O Hub IoT oferece suporte à função `is_defined()` para essa finalidade. Por exemplo,

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

recuperou todos os dispositivos gêmeos que definem a propriedade reportada `connectivity`. Consulte a seção [Cláusula WHERE][lnk-query-where] para encontrar a referência completa dos recursos de filtragem.

Também há suporte para agrupamento e agregações. Por exemplo,

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

retorna a contagem dos dispositivos em cada status de configuração de telemetria.

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

O exemplo anterior ilustra uma situação em que três dispositivos relataram a configuração bem-sucedida, dois ainda estão aplicando a configuração e um relatou um erro.

### <a name="c-example"></a>Exemplo de C#
A funcionalidade de consulta é exposta pelo [SDK de serviço de C#][lnk-hub-sdks] na classe **RegistryManager**.
Aqui está um exemplo de uma consulta simples:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

Observe como o objeto **query** é instanciado com um tamanho de página (até 1000) e, em seguida, várias páginas podem ser recuperadas chamando os métodos **GetNextAsTwinAsync** várias vezes.
Observe que o objeto de consulta expõe vários **Avançar\***, dependendo da opção de desserialização necessária para a consulta, como dispositivos gêmeos ou objetos de trabalho ou JSON simples usado ao utilizar projeções.

### <a name="nodejs-example"></a>Exemplo do Node.js
A funcionalidade de consulta é exposta pelo [SDK de serviço IoT do Azure para Node.js][lnk-hub-sdks] no objeto **Registry**.
Aqui está um exemplo de uma consulta simples:

```nodejs
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

Observe como o objeto **query** é instanciado com um tamanho de página (até 1000) e, em seguida, várias páginas podem ser recuperadas chamando os métodos **nextAsTwin** várias vezes.
Observe que o objeto de consulta expõe vários **avançar\***, dependendo da opção de desserialização necessária para a consulta, como dispositivos gêmeos ou objetos de trabalho ou JSON simples usado ao utilizar projeções.

### <a name="limitations"></a>Limitações
> [!IMPORTANT]
> Os resultados da consulta podem ter alguns minutos de atraso em relação aos valores mais recentes em dispositivos gêmeos. Ao consultar dispositivos gêmeos individuais por ID, é sempre preferível usar a API de recuperação de dispositivo gêmeo, a qual sempre contém os valores mais recentes e tem limites maiores.

Atualmente, há suporte para as comparações apenas entre tipos primitivos (sem objetos), por exemplo `... WHERE properties.desired.config = properties.reported.config` tem suporte apenas se essas propriedades tiverem valores primitivos.

## <a name="get-started-with-jobs-queries"></a>Introdução às consultas de trabalhos
Os [Trabalhos][lnk-jobs] fornecem uma maneira de executar operações em conjuntos de dispositivos. Cada dispositivo gêmeo contém as informações dos trabalhos dos quais ele faz parte em uma coleção chamada **jobs**.
Logicamente,

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Atualmente, essa coleção pode ser consultada como **devices.jobs** na linguagem de consulta do Hub IoT.

> [!IMPORTANT]
> Atualmente, a propriedade jobs nunca é retornada ao consultar dispositivos gêmeos (ou seja, consultas que contém “FROM devices”). Ela só pode ser acessada diretamente com consultas usando `FROM devices.jobs`.
>
>

Por exemplo, para obter todos os trabalhos (agendados e anteriores) que afetam um único dispositivo, você pode usar a seguinte consulta:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Observe como essa consulta fornece o status específico do dispositivo (e possivelmente a resposta do método direto) de cada trabalho retornado.
Também é possível filtrar com condições boolianas arbitrárias em todas as propriedades dos objetos na coleção **devices.jobs**.
Por exemplo, a consulta a seguir:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

recupera todos os trabalhos de atualização do dispositivo gêmeo concluídos para o dispositivo **myDeviceId** que foram criados depois de setembro de 2016.

Também é possível recuperar os resultados por dispositivo de um único trabalho.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Limitações
No momento, as consultas em **devices.jobs** não dão suporte a:

* Projeções, portanto, apenas `SELECT *` é possível.
* Condições que se referem ao dispositivo gêmeo além das propriedades de trabalho (consulte a seção anterior).
* Realização de agregações, tal como count, avg e group by.

## <a name="get-started-with-device-to-cloud-message-routes-query-expressions"></a>Começar a usar expressões de consulta de rotas de mensagem do dispositivo para a nuvem

Usando as [rotas do dispositivo para nuvem][lnk-devguide-messaging-routes], você pode configurar o Hub IoT para distribuir mensagens de dispositivo para a nuvem para diferentes pontos de extremidade com base em expressões avaliadas em relação a mensagens individuais.

A [condição][lnk-query-expressions] da rota usa a mesma linguagem de consulta que o Hub IoT como condições em consultas gêmeas e de trabalho. Condições de rota são avaliadas no corpo e nos cabeçalhos de mensagem. A expressão de consulta de direcionamento pode envolver somente cabeçalhos de mensagens, apenas o corpo da mensagem ou os cabeçalhos e o corpo da mensagem. O Hub IoT pressupõe que haja um esquema específico para os cabeçalhos e o corpo da mensagem para direcionar mensagens. As seções a seguir descrevem o que é necessário para que o Hub IoT encaminhe corretamente:

### <a name="routing-on-message-headers"></a>Encaminhamento em cabeçalhos de mensagens

O Hub IoT pressupõe que haja a seguinte representação JSON dos cabeçalhos de mensagem para direcionamento de mensagens:

```json
{
    "$messageId": "",
    "$enqueuedTime": "",
    "$to": "",
    "$expiryTimeUtc": "",
    "$correlationId": "",
    "$userId": "",
    "$ack": "",
    "$connectionDeviceId": "",
    "$connectionDeviceGenerationId": "",
    "$connectionAuthMethod": "",
    "$content-type": "",
    "$content-encoding": "",

    "userProperty1": "",
    "userProperty2": ""
}
```

As propriedades do sistema de mensagens são fixadas previamente com o símbolo `'$'`.
As propriedades do usuário sempre serão acessadas com seu nome. Se um nome de propriedade de usuário coincidir com uma propriedade do sistema (como `$to`), a propriedade de usuário será recuperada com a expressão `$to`.
Você sempre pode acessar a propriedade do sistema usando colchetes `{}`: por exemplo, é possível usar a expressão `{$to}` para acessar a propriedade de sistema `to`. Nomes de propriedade entre colchetes sempre recuperam a propriedade do sistema correspondente.

Lembre-se que os nomes de propriedade não diferenciam maiúsculas de minúsculas.

> [!NOTE]
> Todas as propriedades de mensagem são cadeias de caracteres. As propriedades do sistema, conforme descrito no [guia do desenvolvedor][lnk-devguide-messaging-format], não estão disponíveis para serem usadas em consultas no momento.
>

Por exemplo, se você usar uma propriedade `messageType`, poderá querer rotear toda a telemetria para um ponto de extremidade e todos os alertas para outro. Você pode escrever a expressão a seguir para rotear a telemetria:

```sql
messageType = 'telemetry'
```

E a expressão a seguir para rotear as mensagens de alerta:

```sql
messageType = 'alert'
```

Também há suporte para expressões e funções boolianas. Esse recurso permite distinguir entre o nível de severidade, por exemplo:

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Consulte a seção [Expressão e condições][lnk-query-expressions] para ver a lista completa de funções e operadores com suporte.

### <a name="routing-on-message-bodies"></a>Encaminhamento em corpos de mensagem

O Hub IoT só poderá direcionar com base no conteúdo do corpo da mensagem se o corpo da mensagem estiver corretamente formado em JSON, codificado em UTF-8, UTF-16 ou UTF-32. Você deve definir o tipo de conteúdo da mensagem como `application/json` e a codificação do conteúdo como uma das codificações UTF com suporte nos cabeçalhos da mensagem para permitir que o Hub IoT direcione a mensagem com base no conteúdo do corpo. Se qualquer um dos cabeçalhos não for especificado, o Hub IoT não tentará avaliar qualquer expressão de consulta que envolva o corpo em relação à mensagem. Se a mensagem não for uma mensagem JSON ou se não especificar o tipo de conteúdo e a codificação de conteúdo, você ainda poderá usar o direcionamento de mensagens para direcionar a mensagem com base em cabeçalhos de mensagens.

Você pode usar `$body` na expressão de consulta para direcionar a mensagem. Você pode usar uma referência de corpo simples, referência de matriz de corpo ou várias referências de corpo na expressão de consulta. A expressão de consulta também pode combinar uma referência de corpo a uma referência de cabeçalho de mensagem. Por exemplo, a seguir estão todas as expressões de consulta válidas:

```sql
$body.message.Weather.Location.State = 'WA'
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.message.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Noções básicas de uma consulta de Hub IoT
Todas as consultas de Hub IoT são compostas por cláusulas SELECT e FROM cláusulas WHERE e GROUP BY opcionais. Cada consulta é executada em uma coleção de documentos JSON, por exemplo, dispositivos gêmeos. A cláusula FROM indica a coleção de documentos a ser iterada em (**devices** ou **devices.jobs**). Em seguida, o filtro na cláusula WHERE é aplicado. Nas agregações, os resultados desta etapa são agrupados como especificado na cláusula GROUP BY e, para cada grupo, uma linha é gerada conforme especificado na cláusula SELECT.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Cláusula FROM
A cláusula **FROM <from_specification>** pode assumir somente dois valores: **FROM devices**, para consultar dispositivos gêmeos, ou **FROM devices.jobs**, para consultar os detalhes de trabalho por dispositivo.

## <a name="where-clause"></a>Cláusula WHERE
A cláusula **WHERE <filter_condition>** é opcional. Ela especifica uma ou mais condições que os documentos JSON na coleção FROM devem satisfazer para serem incluídos como parte dos resultados. Todos os documentos JSON devem avaliar as condições especificadas como “true” para ser incluídos no resultado.

As condições permitidas são descritas na seção [Expressões e condições][lnk-query-expressions].

## <a name="select-clause"></a>Cláusula SELECT
A cláusula SELECT (**SELECT <select_list>**) é obrigatória e especifica quais valores são recuperados pela consulta. Ela especifica os valores JSON a serem usado para gerar novos objetos JSON.
Para cada elemento do subconjunto filtrado (e opcionalmente agrupado) da coleção FROM, a fase de projeção gera um novo objeto JSON, construído com os valores especificados na cláusula SELECT.

Veja a seguir a gramática da cláusula SELECT:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

em que **attribute_name** refere-se a qualquer propriedade do documento JSON na coleção FROM. Alguns exemplos de cláusulas SELECT podem ser encontrados na seção [Introdução às consultas de dispositivo gêmeo][lnk-query-getstarted].

No momento, as cláusulas de seleção diferentes de **SELECT \*** têm suporte apenas em consultas de agregação em dispositivos gêmeos.

## <a name="group-by-clause"></a>Cláusula GROUP BY
A cláusula **GROUP BY <group_specification>** é uma etapa opcional que pode ser executada após o filtro especificado na cláusula WHERE e antes da projeção especificada em SELECT. Ela agrupa documentos com base no valor de um atributo. Esses grupos são usados para gerar valores agregados conforme especificado na cláusula SELECT.

Um exemplo de uma consulta usando GROUP BY é:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

A sintaxe formal para GROUP BY é:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

em que **attribute_name** refere-se a qualquer propriedade do documento JSON na coleção FROM.

No momento, a cláusula GROUP BY tem suporte apenas ao consultar dispositivos gêmeos.

## <a name="expressions-and-conditions"></a>Expressões e condições
Em um alto nível, uma *expressão*:

* Avalia para uma instância de um tipo JSON (por exemplo, booliano, número, cadeia de caracteres, matriz ou objeto) e
* É definida pela manipulação de dados provenientes do documento JSON de dispositivo e constantes usando as funções e operadores internos.

*Condições* são expressões que retornam um valor booliano. Qualquer constante diferente de booliana **true** é considerada como **false** (incluindo **null**, **undefined**, qualquer instância de matriz ou objeto, qualquer cadeia de caracteres e claramente o booliano **false**).

A sintaxe de expressões é:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

onde:

| Símbolo | Definição |
| --- | --- |
| attribute_name | Qualquer propriedade do documento JSON na coleção **FROM**. |
| binary_operator | Qualquer operador binário listado na seção [Operadores](#operators). |
| function_name| Qualquer função listada na seção [Funções](#functions). |
| decimal_literal |Um float expresso em notação decimal. |
| hexadecimal_literal |Um número expresso pela cadeia de caracteres '0x' seguida por uma cadeia de caracteres de dígitos hexadecimais. |
| string_literal |Literais de cadeia de caracteres são cadeias de caracteres Unicode representadas por uma sequência de zero ou mais caracteres Unicode ou sequências de escape. As literais de cadeia de caracteres são colocadas entre aspas simples (apóstrofe: ') ou aspas duplas (aspas: "). Escapes permitidos: `\'`, `\"`, `\\`, `\uXXXX` para caracteres Unicode definidos por quatro dígitos hexadecimais. |

### <a name="operators"></a>Operadores
Há suporte para os seguintes operadores:

| Família | Operadores |
| --- | --- |
| Aritmético |+,-,*,/,% |
| Lógico |AND, OR, NOT |
| Comparação |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funções
Ao consultar gêmeos e trabalhos, a única função com suporte é:

| Função | Descrição |
| -------- | ----------- |
| IS_DEFINED(propriedade) | Retorna um valor booliano que indica se um valor foi atribuído à propriedade (incluindo `null`). |

Em condições de rotas, há suporte para as seguintes funções matemáticas:

| Função | Descrição |
| -------- | ----------- |
| ABS(x) | Retorna o valor absoluto (positivo) da expressão numérica especificada. |
| EXP(x) | Retorna o valor exponencial da expressão numérica especificada (e^x). |
| POWER(x,y) | Retorna o valor da expressão especificada para a potência indicada (x^y).|
| SQUARE(x) | Retorna o quadrado do valor numérico especificado. |
| CEILING(x) | Retorna o menor valor de número inteiro maior ou igual à expressão numérica especificada. |
| FLOOR(x) | Retorna o maior inteiro menor ou igual à expressão numérica especificada. |
| SIGN(x) | Retorna o sinal positivo (+1), zero (0) ou negativo (-1) da expressão numérica especificada.|
| SQRT(x) | Retorna o quadrado do valor numérico especificado. |

Em condições de rotas, há suporte para as seguintes funções de verificação de tipo e conversão de tipo:

| Função | Descrição |
| -------- | ----------- |
| AS_NUMBER | Converte a cadeia de caracteres de entrada em um número. `noop` se a entrada for um número; `Undefined` se a cadeia de caracteres não representar um número.|
| IS_ARRAY | Retorna um valor booliano que indica se o tipo da expressão especificada é uma matriz. |
| IS_BOOL | Retorna um valor booliano que indica se o tipo da expressão especificada é um booliano. |
| IS_DEFINED | Retorna um valor booliano que indica se um valor foi atribuído à propriedade. |
| IS_NULL | Retorna um valor booliano que indica se o tipo da expressão especificada é nulo. |
| IS_NUMBER | Retorna um valor booliano que indica se o tipo da expressão especificada é um número. |
| IS_OBJECT | Retorna um valor booliano que indica se o tipo da expressão especificada é um objeto JSON. |
| IS_PRIMITIVE | Retorna um valor booliano que indica se o tipo da expressão especificada é um primitivo (cadeia de caracteres, booliano, numérico ou `null`). |
| IS_STRING | Retorna um valor booliano que indica se o tipo da expressão especificada é uma cadeia de caracteres. |

Em condições de rotas, há suporte para as seguintes funções de cadeias de caracteres:

| Função | Descrição |
| -------- | ----------- |
| CONCAT(x, …) | Retorna uma cadeia de caracteres que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres. |
| LENGTH(x) | Retorna o número de caracteres da expressão de cadeia de caracteres especificada.|
| LOWER(x) | Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres maiúsculos em minúsculos. |
| UPPER(x) | Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres minúsculos em maiúsculos. |
| SUBSTRING(cadeia de caracteres, início [, tamanho]) | Retorna parte de uma expressão de cadeia de caracteres começando na posição baseada em zero do caractere especificado e continua até o comprimento especificado ou até o final da cadeia de caracteres. |
| INDEX_OF (cadeia de caracteres, fragmento) | Retorna a posição inicial da primeira ocorrência da segunda expressão de cadeia de caracteres dentro da primeira expressão de cadeia de caracteres especificada, ou -1 se a cadeia de caracteres não for encontrada.|
| STARTS_WITH(x, y) | Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres começa com a segunda. |
| ENDS_WITH(x, y) | Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda. |
| CONTAINS(x,y) | Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres contém a segunda. |

## <a name="next-steps"></a>Próximas etapas
Saiba como executar consultas em seus aplicativos usando [SDKs do IoT do Azure][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
