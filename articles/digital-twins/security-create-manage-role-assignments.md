---
title: Criar e gerenciar atribuições de função nos Gêmeos Digitais do Azure | Microsoft Docs
description: Criar e gerenciar atribuições de função nos Gêmeos Digitais do Azure.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyhughes
ms.custom: seodec18
ms.openlocfilehash: a57089eb2cd87b08ba647afed002d90d6f14891a
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846653"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Criar e gerenciar atribuições de função nos Gêmeos Digitais do Azure

Os Gêmeos Digitais do Azure usam [RBAC](./security-role-based-access-control.md) (controle de acesso baseado em função) para gerenciar o acesso aos recursos.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="role-assignments-overview"></a>Visão geral das atribuições de função

Cada atribuição de função está em conformidade com a seguinte definição:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

A tabela abaixo descreve cada atributo:

| Atributo | Nome | Obrigatório | Tipo | DESCRIÇÃO |
| --- | --- | --- | --- | --- |
| roleId | Identificador de definição de função | Sim | Cadeia de caracteres | A ID exclusiva da atribuição de função desejada. Encontre definições de funções e seus identificadores consultando a API do Sistema ou examinando a tabela abaixo. |
| objectId | Identificador de objeto | Sim | Cadeia de caracteres | Uma ID do Azure Active Directory, ID de objeto de entidade de serviço ou nome de domínio. Para o que atribuições de função é atribuída. A atribuição de função precisa ser formatada de acordo com seu tipo associado. Para o ObjectIdType `DomainName`, ObjectId precisa começar com o caractere `“@”`. |
| objectIdType | Tipo de identificador de objeto | Sim | Cadeia de caracteres | O tipo de identificador de Objeto usado. Consulte **ObjectIdTypes com suporte** abaixo. |
| path | Caminho de espaço | Sim | Cadeia de caracteres | O caminho de acesso completo para o objeto `Space`. Um exemplo é `/{Guid}/{Guid}`. Se um identificador precisar da atribuição de função para todo o gráfico, especifique `"/"`. Esse caractere designa a raiz, mas seu uso é desencorajado. Sempre siga o princípio de privilégios mínimos. |
| tenantId | Identificador de locatário | Varia | Cadeia de caracteres | Na maioria dos casos, uma ID de locatário do Azure Active Directory. Não permitido para ObjectIdTypes `DeviceId` e `TenantId`. Obrigatório para ObjectIdTypes `UserId` e `ServicePrincipalId`. Opcional para o ObjectIdType DomainName. |

### <a name="supported-role-definition-identifiers"></a>Identificadores de definição de função com suporte

Cada atribuição de função associa uma definição de função a uma entidade no dos Gêmeos Digitais do Azure.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Tipos de identificador de objeto com suporte

Anteriormente, o atributo **objectIdType** foi introduzido.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operações de atribuição de função

Os Gêmeos Digitais do Azure oferecem suporte para operações *CREATE*, *READ* e *DELETE* completas para atribuições de função. As operações *UPDATE* são tratadas pela adição de atribuições de função, remoção de atribuições de função, ou modificação de nós [Spatial Intelligence Graph](./concepts-objectmodel-spatialgraph.md) aos quais as atribuições de função concedem acesso.

![Ponto de extremidade de atribuição de função][1]

A documentação de referência do Swagger fornecida contém informações adicionais sobre todos os pontos de extremidade disponíveis, operações de solicitação e definições da API.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>Conceder permissões para a entidade de serviço

Conceder permissões à entidade de serviço geralmente é uma das primeiras etapas que você usará ao trabalhar com os Gêmeos Digitais do Azure. Isso envolve:

1. Fazer logon na sua instância do Azure por meio do PowerShell.
1. Adquirir informações da sua entidade de serviço.
1. Atribuir a função desejada para sua entidade de serviço.

Sua ID de aplicativo é fornecida no Azure Active Directory. Para saber mais informações sobre como configurar e provisionar um Gêmeos Digitais do Azure no Active Directory, leia o [Início Rápido](./quickstart-view-occupancy-dotnet.md).

Após criar a ID do aplicativo, execute os seguintes comandos do PowerShell:

```shell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId  <ApplicationId>
```

Um usuário com a função **Admin** pode então atribuir a função Administrador de Espaço para um usuário, fazendo uma solicitação HTTP POST autenticada para a URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Com o corpo JSON a seguir:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>Recuperar todas as funções

![Funções do sistema][2]

Para listar todas as funções disponíveis (definições de função), faça uma solicitação HTTP GET autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Uma solicitação com êxito retornará uma matriz JSON com as entradas para cada função que pode ser atribuída:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>Verificar uma atribuição de função específica

Para verificar uma atribuição de função específica, faça uma solicitação HTTP GET autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Valor de parâmetro** | **Necessária** |  **Tipo** |  **Descrição** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  verdadeiro | Cadeia de caracteres |   A objectId para o UserId objectIdType. |
| YOUR_PATH | verdadeiro | Cadeia de caracteres |   O caminho escolhido para verificar o acesso. |
| YOUR_ACCESS_TYPE |  verdadeiro | Cadeia de caracteres |   O tipo de acesso para verificar. |
| YOUR_RESOURCE_TYPE | verdadeiro | Cadeia de caracteres |  O recurso para verificar. |

Uma solicitação bem-sucedida retornará um booliano `true` ou `false` para indicar se o tipo de acesso foi atribuído ao usuário para o caminho especificado e o recurso determinado.

### <a name="get-role-assignments-by-path"></a>Obter atribuições de função por caminho

Para obter todas as atribuições de função para um caminho, faça uma solicitação HTTP GET autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Valor | Substitua por |
| --- | --- |
| YOUR_PATH | O caminho completo para o espaço |

Uma solicitação com êxito retornará uma matriz JSON com cada atribuição de função associada ao parâmetro **caminho** selecionado:

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Revogar uma permissão

Para revogar uma permissão de um destinatário, exclua a atribuição de função fazendo uma solicitação HTTP DELETE autenticada:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | A **id** da atribuição de função a ser removida |

Uma solicitação DELETE com êxito retornará um status de resposta 204. Verifique se a remoção da atribuição de função pela [verificação](#check) ainda mantém a atribuição de função.

### <a name="create-a-role-assignment"></a>Criar uma atribuição de função

Para criar uma atribuição de função, faça uma solicitação HTTP POST autenticada para a URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Verifique se que o corpo JSON está em conformidade com o esquema a seguir:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Uma solicitação com êxito retornará um status de resposta 201 juntamente com a **id** da atribuição de função recém-criada:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Exemplos de configuração

Os exemplos a seguir demonstram como configurar o corpo JSON em vários cenários de atribuição de função comumente encontrados.

* **Exemplo**: Um usuário precisa de acesso administrativo a um andar de um espaço de locatário.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Exemplo**: Um aplicativo executa cenários de teste simulando dispositivos e sensores.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Exemplo**: Todos os usuários que fazem parte de um domínio recebem acesso de leitura para espaços, sensores e usuários. Esse acesso inclui seus objetos relacionados correspondentes.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Próximas etapas

- Para examinar o controle de acesso baseado em função dos Gêmeos Digitais do Azure, leia [Controle de acesso baseado em função](./security-authenticating-apis.md).

- Para saber mais sobre a autenticação da API dos Gêmeos Digitais do Azure, leia [Autenticação de API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
