---
title: Padrões de consulta comuns dos Gêmeos Digitais do Azure | Microsoft Docs
description: Aprenda os padrões comuns para consultar as APIs de gerenciamento dos Gêmeos Digitais do Azure.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: ff8638042fa10c939ff9c5fa7af99a660fcdc753
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924729"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Como consultar as APIs dos Gêmeos Digitais do Azure para tarefas comuns

Este artigo mostra os padrões de consulta para ajudá-lo a executar cenários comuns para sua instância dos Gêmeos Digitais do Azure. Isso pressupõe que sua instância dos Gêmeos Digitais já está em execução. Você pode usar qualquer cliente REST, como Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Consultas para tipos e espaços

Esta seção mostra exemplos de consultas para obter mais informações sobre seus espaços provisionados. Faça solicitações GET HTTP autenticadas com as consultas de exemplo, substituindo os espaços reservados pelos valores de sua configuração. 

- Obtenha espaços que não são nós raiz.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Obtenha um espaço por nome e inclua dispositivos, sensores, valores computados e valores de sensor. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Obtenha espaços e suas informações de sensor/dispositivo, cujo pai é a ID do espaço especificada e quais estão nos níveis dois a cinco [em relação ao espaço determinado](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Obtenha o espaço com a ID especificada e inclua os valores de sensor e computados.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Obtenha as chaves de propriedade para um determinado espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Obtenha espaços com a chave de propriedade chamada *AreaInSqMeters* e seu valor é 30. Você também pode realizar operações de cadeia de caracteres, por exemplo, obter espaços contendo chave de propriedade com `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Obter todos os nomes com o nome *Temperatura* e dependências e ontologias associadas.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Consultas para as funções e atribuições de função

Esta seção mostra algumas consultas para obter mais informações sobre funções e suas atribuições. 

- Obtenha todas as funções compatíveis com os Gêmeos Digitais do Azure.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Obtenha todas as atribuições de função em sua instância dos Gêmeos Digitais. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Obtenha as atribuições de função em um caminho específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Consultas para dispositivos

Esta seção mostra alguns exemplos de como você pode usar as APIs de Gerenciamento para obter informações específicas sobre os dispositivos. Todas as chamadas à API precisam ser solicitações GET HTTP autenticadas.

- Obtenha todos os dispositivos.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Localize todos os status do dispositivo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Obtenha um dispositivo específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Obtenha todos os dispositivos conectados ao espaço raiz.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Obtenha todos os dispositivos conectados aos espaços nos níveis 2 a 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Obtenha todos os dispositivos conectados diretamente a uma ID de espaço em particular.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Obtenha todos os dispositivos conectados a um determinado espaço e seus descendentes.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Obtenha todos os dispositivos conectados aos descendentes de um espaço, exceto aquele espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Obtenha todos os dispositivos conectados aos filhos diretos de um espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Obtenha todos os dispositivos conectados a um dos ancestrais de um espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Obtenha todos os dispositivos conectados aos descendentes de um espaço que são de um nível menor ou igual a 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Obtenha todos os dispositivos conectados aos espaços que estão no mesmo nível que o espaço com a ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Obtenha a cadeia de conexão do dispositivo do Hub IoT para seu dispositivo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Obtenha o dispositivo com a ID de hardware especificada, incluindo sensores conectados.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Obtenha sensores para os tipos de dados em particular, neste caso *Movimento* e *Temperatura*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Consultas para elementos de correspondência e funções definidas pelo usuário 

- Obtenha todos os elementos de correspondência provisionados e suas IDs.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Obtenha detalhes sobre um determinado elemento de correspondência, incluindo os espaços e a função definida pelo usuário associados a ele.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Avalie um elemento de correspondência em relação a um sensor e habilite o log para fins de depuração. O retorno dessa mensagem GET HTTP indica se o elemento de correspondência e o sensor pertencem ao tipo de dados. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Obtenha a ID das funções definidas pelo usuário. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Obter o conteúdo de uma determinada função definida pelo usuário 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Consultas para usuários

Esta seção mostra alguns exemplos de consultas de API para gerenciar usuários em Gêmeos Digitais do Azure. Faça uma solicitação GET HTTP substituindo os espaços reservados pelos valores de sua configuração. 

- Obtenha todos os usuários. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Obtenha um usuário específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Próximas etapas

Para saber como autenticar-se com a API de gerenciamento, leia [Autenticar com APIs](./security-authenticating-apis.md).

Para ver todos os pontos de extremidade da API, leia [Como usar o Swagger de Gêmeos Digitais](./how-to-use-swagger.md).
