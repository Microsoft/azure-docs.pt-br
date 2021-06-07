---
title: Tutorial – Desenvolver um ponto de extremidade do SCIM de provisionamento de usuários para aplicativos do Azure AD
description: O sistema para SCIM (Sistema de Gerenciamento de Usuários entre Domínios) padroniza o provisionamento automático de usuários. Neste tutorial, você aprenderá a desenvolver um ponto de extremidade do SCIM, integrar sua API do SCIM ao Azure Active Directory e começar a automatizar o provisionamento de usuários e grupos em aplicativos de nuvem.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: e8192c5c6734009f69e3f741531251dd85675b47
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449442"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Tutorial: Desenvolver e planejar o provisionamento para um ponto de extremidade SCIM

Como desenvolvedor de aplicativos, você pode usar a API de gerenciamento de usuários do SCIM (Sistema de Gerenciamento de Usuários entre Domínios) para habilitar o provisionamento automático de usuários e grupos entre seu aplicativo e o AAD (Azure AD). Este artigo descreve como criar um ponto de extremidade do SCIM e integrá-lo ao serviço de provisionamento do AAD. A especificação do SCIM fornece um esquema de usuário comum para provisionamento. Quando usado em conjunto com padrões de federação como SAML ou OpenID Connect, o SCIM fornece aos administradores uma solução de ponta a ponta baseada em padrões para o gerenciamento de acesso.

![Provisionamento do Azure AD para um aplicativo com SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM é uma definição padronizada de dois pontos de extremidade: um ponto de extremidade `/Users` e um ponto de extremidade `/Groups`. Ele usa verbos REST comuns para criar, atualizar e excluir objetos e um esquema predefinido para atributos comuns, como nome do grupo, nome de usuário, nome, sobrenome e email. Os aplicativos que oferecem uma API REST 2.0 do SCIM podem reduzir ou eliminar o problema de trabalhar com uma API de gerenciamento de usuários proprietária. Por exemplo, qualquer cliente em conformidade com o SCIM sabe como fazer um HTTP POST de um objeto JSON para o ponto de extremidade `/Users` para criar uma entrada de usuário. Em vez de precisar de uma API um pouco diferente para as mesmas ações básicas, os aplicativos que estão em conformidade com o padrão SCIM podem aproveitar instantaneamente os clientes, as ferramentas e o código preexistentes. 

O esquema de objeto de usuário padrão e as APIs REST para gerenciamento definidos no SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) permitem que provedores de identidade e aplicativos sejam integrados com mais facilidade entre si. Os desenvolvedores de aplicativos que criam um ponto de extremidade SCIM podem se integrar a qualquer cliente compatível com SCIM sem precisar fazer personalizações.

Para automatizar o provisionamento para um aplicativo serão necessárias a criação e a integração de um ponto de extremidade do SCIM com o cliente SCIM do Azure AD. Use as etapas a seguir para iniciar o provisionamento de usuários e grupos em seu aplicativo. 
    
1. Projete seu esquema de usuário e grupo

   Identifique os objetos e atributos do aplicativo para determinar como eles serão mapeados para o esquema de usuário e grupo com suporte da implementação de SCIM do AAD.

1. Entenda a implementação do SCIM do AAD

   Entenda como o cliente de SCIM do AAD é implementado para modelar o tratamento e as respostas de solicitação do protocolo SCIM.

1. Crie um ponto de extremidade do SCIM

   Um ponto de extremidade deve ser compatível com o SCIM 2.0 para integração com o serviço de provisionamento do AAD. Como opção, use as bibliotecas de CLI (Common Language Infrastructure) da Microsoft e os exemplos de código para criar seu ponto de extremidade. Esses exemplos são apenas para referência e teste; não é recomendável usá-los como dependências em seu aplicativo de produção.

1. Integrar seu ponto de extremidade do SCIM com o cliente SCIM do AAD 

   Se sua organização usa um aplicativo de terceiros para implementar um perfil do SCIM 2.0 compatível com o AAD, você pode automatizar o provisionamento e o desprovisionamento de usuários e grupos rapidamente.

1. Publicar seu aplicativo na galeria de aplicativos do AAD 

   Permita que os clientes descubram com facilidade seu aplicativo e configurem facilmente o provisionamento. 

![Etapas para integrar um ponto de extremidade do SCIM com o Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="design-your-user-and-group-schema"></a>Projete seu esquema de usuário e grupo

Cada aplicativo requer atributos diferentes para criar um usuário ou grupo. Inicie sua integração identificando os objetos (usuários, grupos) e atributos (nome, gerente, cargo etc.) necessários do seu aplicativo. 

O padrão SCIM define um esquema para gerenciar usuários e grupos. 

O esquema de usuário **principal** requer apenas três atributos (todos os outros atributos são opcionais):

- `id`, identificador definido pelo provedor de serviços
- `externalId`, identificador definido pelo cliente
- `meta`, metadados *somente leitura* mantidos pelo provedor de serviços

Além do esquema de usuário **principal**, o padrão SCIM define uma extensão de usuário **corporativo** com um modelo para estender o esquema do usuário para atender às necessidades do seu aplicativo. 

Por exemplo: se seu aplicativo exigir o email e o gerente de um usuário, use o esquema **principal** para coletar o email do usuário e o esquema **corporativo** para coletar o gerente do usuário.

Para criar o esquema, siga estas etapas:

1. Liste os atributos que seu aplicativo requer e, em seguida, categorize como atributos necessários para autenticação (por exemplo, loginName e email), atributos necessários para gerenciar o ciclo de vida do usuário (por exemplo, status/ativo) e todos os outros atributos necessários para que o aplicativo funcione (por exemplo, gerenciador, marca).

1. Verifique se esses atributos já estão definidos no esquema de usuário **principal** ou no esquema de usuário **corporativo**. Caso contrário, você deve definir uma extensão para o esquema de usuário que abranja os atributos ausentes. Confira o exemplo abaixo de uma extensão para o usuário para permitir o provisionamento de um usuário `tag`.

1. Mapeie os atributos SCIM para os atributos de usuário no Azure AD. Se um dos atributos que você definiu em seu ponto de extremidade do SCIM não tiver uma contraparte clara no esquema de usuário do Azure AD, oriente o administrador de locatários a estender o esquema ou use um atributo de extensão, conforme mostrado abaixo, para a propriedade `tags`.

|Atributo de aplicativo necessário|Atributo SCIM mapeado|Atributo do Azure AD mapeado|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.familyName|surName|
|workMail|emails[type eq "work"].value|Email|
|manager|manager|manager|
|marca|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|ativo|isSoftDeleted (valor calculado não armazenado no usuário)|

**Lista de exemplo de atributos necessários**

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen@testuser.com",
     "id": "48af03ac28ad4fb88478",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
}   
```
**Esquema de exemplo definido por um conteúdo JSON**

> [!NOTE]
> Além dos atributos necessários para o aplicativo, a declaração JSON também inclui os atributos obrigatórios `id`, `externalId` e `meta`.

Isso ajuda a categorizar entre `/User` e `/Group` para mapear os atributos de usuário padrão no Azure AD para a RFC SCIM. Confira [como os atributos personalizados são mapeados entre o Azure AD e o ponto de extremidade do SCIM](customize-application-attributes.md).


| Usuário do Active Directory do Azure | “urn:ietf:params:scim:schemas:extension:enterprise:2.0:User” |
| --- | --- |
| IsSoftDeleted |ativo |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |título |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| Serviço Móvel |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| sobrenome |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

**Lista de exemplo de atributos de usuário e de grupo**

| Grupo do Active Directory do Azure | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| membros |membros |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

**Lista de exemplo de atributos de grupo**

> [!NOTE]
> Você não precisa dar suporte a usuários e grupos, nem a todos os atributos mostrados aqui; esta é apenas uma referência de como os atributos no Azure AD geralmente são mapeados para propriedades no protocolo SCIM. 

Há vários pontos de extremidade definidos no SCIM RFC. Você pode começar com o ponto de extremidade `/User` e, em seguida, expandir com base nele. 

|Ponto de extremidade|Descrição|
|--|--|
|/User|Executa operações CRUD em um objeto de usuário.|
|/Group|Executa operações CRUD em um objeto de grupo.|
|/Schemas|O conjunto de atributos com suporte de cada cliente e provedor de serviços pode variar. Um provedor de serviços poderá incluir `name`, `title` e `emails`, enquanto outro provedor de serviços usará `name`, `title` e `phoneNumbers`. O ponto de extremidade de esquemas permite a descoberta dos atributos com suporte.|
|/Bulk|As operações em lote permitem que você execute operações em uma grande coleção de objetos de recurso em uma única operação (por exemplo, atualizar associações para um grupo grande).|
|/ServiceProviderConfig|Fornece detalhes sobre os recursos do padrão SCIM que têm suporte, por exemplo, os recursos com suporte e o método de autenticação.|
|/ResourceTypes|Especifica os metadados sobre cada recurso.|

**Lista de exemplos de pontos de extremidade**

> [!NOTE]
> Use o ponto de extremidade `/Schemas` para dar suporte a atributos personalizados ou se o esquema for alterado com frequência, pois ele permite que um cliente recupere o esquema mais atualizado automaticamente. Use o ponto de extremidade `/Bulk` para dar suporte a grupos.

## <a name="understand-the-aad-scim-implementation"></a>Entender a implementação do SCIM do AAD

Para dar suporte a uma API de gerenciamento de usuário do SCIM 2.0, esta seção descreve como o cliente SCIM do AAD é implementado e mostra como modelar o tratamento e as respostas de solicitação do protocolo SCIM.

> [!IMPORTANT]
> O comportamento da implementação do SCIM do Azure AD foi atualizado pela última vez em 18 de dezembro de 2018. Para obter informações sobre o que mudou, consulte [Conformidade do protocolo SCIM 2.0 do serviço de provisionamento de usuário do Azure AD](application-provisioning-config-problem-scim-compatibility.md).

Dentro da [especificação do protocolo SCIM 2.0](http://www.simplecloud.info/#Specification), seu aplicativo deve dar suporte a esses requisitos:

|Requisito|Notas de referência (protocolo SCIM)|
|-|-|
|Criar usuários e, opcionalmente, também criar grupos|[seção 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)|
|Modificar usuários ou grupos com solicitações de PATCH|[seção 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). O suporte garantirá que grupos e usuários sejam provisionados de modo a obter um desempenho adequado.|
|Recuperar um recurso conhecido para um usuário ou grupo criado anteriormente|[seção 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)|
|Consultar usuários ou grupos|[seção 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Por padrão, os usuários são recuperados por seu `id` e consultados por seus `username` e `externalId`, e os grupos são consultados por `displayName`.|
|Consultar usuário por ID e por gerente|seção 3.4.2|
|Consultar grupos por ID e por membro|seção 3.4.2|
|O filtro [excludedAttributes=members](#get-group) ao consultar o recurso de grupo|seção 3.4.2.5|
|Aceitar um único token de portador para autenticação e autorização do AAD para seu aplicativo.||
|Exclusão reversível de um usuário `active=false` e restauração do usuário `active=true`|O objeto de usuário deverá ser retornado em uma solicitação, não importa se o usuário está ativo ou não. A única vez em que o usuário não deverá ser retornado será quando ele for excluído de maneira irreversível do aplicativo.|
|Suporte ao /Schemas endpoint|[Seção 7](https://tools.ietf.org/html/rfc7643#page-30): o ponto de extremidade de descoberta de esquema será usado para descobrir atributos adicionais.|

Use estas diretrizes gerais ao implementar um ponto de extremidade de SCIM para garantir a compatibilidade com o AAD:

* `id` é uma propriedade obrigatória para todos os recursos. Cada resposta que retorna um recurso deve garantir que cada recurso tenha essa propriedade, exceto para `ListResponse` com zero membros.
* A resposta a uma solicitação de consulta/filtro sempre deve ser `ListResponse`.
* Os grupos são opcionais, mas só têm suporte se a implementação do SCIM dá suporte a solicitações **PATCH**.
* Não é necessário incluir o recurso inteiro na resposta **PATCH**.
* O Microsoft AAD usa apenas os seguintes operadores: `eq`, `and`
* Não exija uma correspondência que diferencie maiúsculas de minúsculas em elementos estruturais no SCIM, em particular valores de operação `op` **PATCH**, conforme definido na [seção 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). O AAD emite os valores de `op` como **Adicionar**, **Substituir** e **Remover**.
* O Microsoft AAD faz com que as solicitações busquem um usuário aleatório e um grupo para garantir que o ponto de extremidade e as credenciais estão válidos. Isso também é feito como parte do fluxo **Testar a Conexão** no [portal do Azure](https://portal.azure.com). 
* O atributo no qual os recursos podem ser consultados deve ser definido como um atributo correspondente no aplicativo no [portal do Azure](https://portal.azure.com); confira [Personalizando mapeamentos de atributo de provisionamento de usuário](customize-application-attributes.md).
* Suporte HTTPS em seu ponto de extremidade do SCIM
* [Descoberta de esquema](#schema-discovery)
  * A descoberta de esquema atualmente não é compatível com um aplicativo personalizado, porém ela está sendo usada em determinados aplicativos da galeria. No futuro, a descoberta de esquema será usada como o método principal de adição de atributos a um conector. 
  * Caso um valor não esteja presente, não envie valores nulos.
  * Os valores da propriedade deverão ter uma concatenação com maiúsculas e minúsculas (por exemplo, readWrite).
  * Será necessário retornar uma resposta da lista.
  
### <a name="user-provisioning-and-deprovisioning"></a>Provisionamento e desprovisionamento de usuários

A seguinte ilustração mostra as mensagens que o AAD envia a um serviço SCIM para gerenciar o ciclo de vida de um usuário no repositório de identidades do seu aplicativo.  

![Mostra a sequência de provisionamento e desprovisionamento de usuários](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sequência de provisionamento e desprovisionamento de usuários*

### <a name="group-provisioning-and-deprovisioning"></a>Provisionamento e desprovisionamento de grupos

O provisionamento e desprovisionamento de grupos são opcionais. Quando implementado e habilitado, a ilustração a seguir mostra as mensagens que o AAD envia a um serviço SCIM para gerenciar o ciclo de vida de um grupo no repositório de identidades do seu aplicativo. Essas mensagens são diferentes das mensagens sobre os usuários em dois aspectos:

* As solicitações para recuperar grupos especificam que o atributo de membros deve ser excluído de qualquer recurso fornecido em resposta à solicitação.  
* As solicitações para determinar se um atributo de referência tem um determinado valor são sobre o atributo de membros.  

![Mostra a sequência de provisionamento e desprovisionamento de grupos](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sequência de provisionamento e desprovisionamento de grupos*

### <a name="scim-protocol-requests-and-responses"></a>Solicitações e respostas do protocolo SCIM
Esta seção fornece exemplos de solicitações SCIM emitidas pelo cliente SCIM do AAD e exemplos de respostas esperadas. Para obter melhores resultados, você deve codificar seu aplicativo para lidar com essas solicitações nesse formato e emitir as respostas esperadas.

> [!IMPORTANT]
> Para entender como e quando o serviço de provisionamento de usuários do AAD emite as operações descritas abaixo, confira a seção [Ciclos de provisionamento: inicial e incremental](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) em [Como funciona o provisionamento](how-provisioning-works.md).

[Operações do Usuário](#user-operations)
  - [Criar Usuário](#create-user) ([Solicitação](#request) / [Resposta](#response))
  - [Obter Usuário](#get-user) ([Solicitação](#request-1) / [Resposta](#response-1))
  - [Obter Usuário por consulta](#get-user-by-query) ([Solicitação](#request-2) / [Resposta](#response-2))
  - [Obter Usuário por consulta - Zero resultados](#get-user-by-query---zero-results) ([Solicitação](#request-3) / [Resposta](#response-3))
  - [Atualizar Usuário [Propriedades com vários valores]](#update-user-multi-valued-properties) ([Solicitação](#request-4) / [Resposta](#response-4))
  - [Atualizar Usuário [Propriedades de valor único]](#update-user-single-valued-properties) ([Solicitação](#request-5) / [Resposta](#response-5)) 
  - [Desabilitar Usuário](#disable-user) ([Solicitação](#request-14) / [Resposta](#response-14))
  - [Excluir Usuário](#delete-user) ([Solicitação](#request-6) / [Resposta](#response-6))

[Operações de Grupo](#group-operations)
  - [Criar Grupo](#create-group) ([Solicitação](#request-7) / [Resposta](#response-7))
  - [Obter Grupo](#get-group) ([Solicitação](#request-8) / [Resposta](#response-8))
  - [Obter Grupo por displayName](#get-group-by-displayname) ([Solicitação](#request-9) / [Resposta](#response-9))
  - [Atualizar Grupo [Atributos de não membro]](#update-group-non-member-attributes) ([Solicitação](#request-10) / [Resposta](#response-10))
  - [Atualizar Grupo [Adicionar Membros]](#update-group-add-members) ([Solicitação](#request-11) / [Resposta](#response-11))
  - [Atualizar Grupo [Remover Membros]](#update-group-remove-members) ([Solicitação](#request-12) / [Resposta](#response-12))
  - [Excluir Grupo](#delete-group) ([Solicitação](#request-13) / [Resposta](#response-13))

[Descoberta de esquema](#schema-discovery)
  - [Descobrir esquema](#discover-schema) ([solicitação](#request-15) / [resposta](#response-15))

### <a name="user-operations"></a>Operações do Usuário

* Os usuários podem ser consultados por atributos `userName` ou `email[type eq "work"]`.  

#### <a name="create-user"></a>Criar Usuário

##### <a name="request"></a>Solicitação

*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Resposta

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Obter Usuário

###### <a name="request"></a><a name="request-1"></a>Solicitação
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Resposta (Usuário encontrado)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Solicitação
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Resposta (Usuário não encontrado. Observe que os detalhes não são obrigatórios, apenas status.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Obter Usuário por consulta

##### <a name="request"></a><a name="request-2"></a>Solicitação

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="get-user-by-query---zero-results"></a>Obter Usuário por consulta - Zero resultados

##### <a name="request"></a><a name="request-3"></a>Solicitação

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a><a name="response-3"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-user-multi-valued-properties"></a>Atualizar Usuário [Propriedades com vários valores]

##### <a name="request"></a><a name="request-4"></a>Solicitação

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Atualizar Usuário [Propriedades de valor único]

##### <a name="request"></a><a name="request-5"></a>Solicitação

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Desabilitar Usuário

##### <a name="request"></a><a name="request-14"></a>Solicitação

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Resposta

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Excluir usuário

##### <a name="request"></a><a name="request-6"></a>Solicitação

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="group-operations"></a>Operações de Grupo

* Os grupos sempre serão criados com uma lista de membros vazios.
* Grupos podem ser consultados pelo atributo `displayName`.
* A solicitação PATCH para atualizar para o grupo deve exibir *HTTP 204 Sem Conteúdo* na resposta. Retornar um corpo com uma lista de todos os membros não é aconselhável.
* Não é necessário dar suporte ao retorno de todos os membros do grupo.

#### <a name="create-group"></a>Criar Grupo

##### <a name="request"></a><a name="request-7"></a>Solicitação

*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Resposta

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Obter Grupo

##### <a name="request"></a><a name="request-8"></a>Solicitação

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Resposta
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Obter Grupo por displayName

##### <a name="request"></a><a name="request-9"></a>Solicitação
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Atualizar Grupo [Atributos de não membro]

##### <a name="request"></a><a name="request-10"></a>Solicitação

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="update-group-add-members"></a>Atualizar Grupo [Adicionar Membros]

##### <a name="request"></a><a name="request-11"></a>Solicitação

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

#### <a name="update-group-remove-members"></a>Atualizar Grupo [Remover Membros]

##### <a name="request"></a><a name="request-12"></a>Solicitação

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

#### <a name="delete-group"></a>Excluir grupo

##### <a name="request"></a><a name="request-13"></a>Solicitação

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="schema-discovery"></a>Descoberta de Esquema
#### <a name="discover-schema"></a>Descobrir esquema

##### <a name="request"></a><a name="request-15"></a>Solicitação
*GET /Schemas* 
##### <a name="response"></a><a name="response-15"></a>Resposta
*HTTP/1.1 200 OK*
```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:ListResponse"
    ],
    "itemsPerPage": 50,
    "startIndex": 1,
    "totalResults": 3,
    "Resources": [
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:User",
    "name" : "User",
    "description" : "User Account",
    "attributes" : [
      {
        "name" : "userName",
        "type" : "string",
        "multiValued" : false,
        "description" : "Unique identifier for the User, typically
used by the user to directly authenticate to the service provider.
Each User MUST include a non-empty userName value.  This identifier
MUST be unique across the service provider's entire set of Users.
REQUIRED.",
        "required" : true,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "server"
      },                
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:User"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:Group",
    "name" : "Group",
    "description" : "Group",
    "attributes" : [
      {
        "name" : "displayName",
        "type" : "string",
        "multiValued" : false,
        "description" : "A human-readable name for the Group.
REQUIRED.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:Group"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
    "name" : "EnterpriseUser",
    "description" : "Enterprise User",
    "attributes" : [
      {
        "name" : "employeeNumber",
        "type" : "string",
        "multiValued" : false,
        "description" : "Numeric or alphanumeric identifier assigned
to a person, typically based on order of hire or association with an
organization.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
"/v2/Schemas/urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
    }
  }
]
}
```

### <a name="security-requirements"></a>Requisitos de segurança
**Versões do Protocolo TLS**

As únicas versões aceitáveis do protocolo TLS são TLS 1.2 e TLS 1.3. Nenhuma outra versão do TLS é permitida. Nenhuma versão do SSL é permitida. 
- As chaves RSA devem ter pelo menos 2.048 bits.
- Chaves ECC devem ter pelo menos 256 bits, geradas usando uma curva elíptica aprovada

**Tamanhos de Chaves**

Todos os serviços devem usar certificados X.509 gerados usando chaves de criptografia de tamanho suficiente, o que significa:

**Conjuntos de Criptografia**

Todos os serviços devem ser configurados para usar os seguintes conjuntos de criptografia, na ordem exata especificada abaixo. Observe que, se você tiver apenas um certificado RSA, os conjuntos de criptografia ECDSA não terão nenhum efeito. </br>

Barra mínima dos conjuntos de criptografia TLS 1.2:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>Intervalos de IP
O serviço de provisionamento do Azure AD atualmente opera em Intervalos de IP para o AzureActiveDirectory, conforme listado [aqui](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all). É possível adicionar os intervalos de IP listados na marca AzureActiveDirectory para permitir o tráfego do serviço de provisionamento do Azure AD em seu aplicativo. Observe que será necessário examinar com atenção a lista de intervalos de IP para os endereços computados. Um endereço como '40.126.25.32' poderá ser representado na lista de intervalos de IP como ' 40.126.0.0/18 '. Também é possível recuperar de modo programático a lista de intervalos de IP usando a [API](/rest/api/virtualnetwork/servicetags/list) a seguir.

## <a name="build-a-scim-endpoint"></a>Crie um ponto de extremidade do SCIM

Agora que você criou seu esquema e entendeu a implementação do SCIM do Azure AD, pode começar a desenvolver seu ponto de extremidade do SCIM. Em vez de começar do zero e criar a implementação completamente por conta própria, você pode contar com várias bibliotecas de SCIM de software livre publicadas pela Comunidade do SCIM.

Para obter diretrizes sobre como criar um ponto de extremidade SCIM, incluindo exemplos, confira [Desenvolver um ponto de extremidade SCIM de exemplo](use-scim-to-build-users-and-groups-endpoints.md).

O [exemplo de código de referência](https://aka.ms/SCIMReferenceCode) do .NET Core de software livre publicado pela equipe de provisionamento do Azure AD é um recurso que pode impulsionar seu desenvolvimento. Depois de criar seu ponto de extremidade SCIM, será necessário testá-lo. Você pode usar a coleção de [testes do postmaster](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) fornecidos como parte do código de referência ou executar as solicitações/respostas de exemplo fornecidas [acima](#user-operations).  

   > [!Note]
   > O código de referência destina-se a ajudá-lo a começar a criar seu ponto de extremidade do SCIM e é fornecido "no estado em que se encontra". As contribuições da comunidade são úteis para ajudar a criar e manter o código.

A solução é composta por dois projetos, _Microsoft.SCIM_ e _Microsoft.SCIM.WebHostSample_.

O projeto _Microsoft.SCIM_ é a biblioteca que define os componentes do serviço Web que está de acordo com a especificação SCIM. Ele declara a interface _Microsoft.SCIM.IProvider_, as solicitações são convertidas em chamadas para os métodos do provedor, que seriam programados para operar em um repositório de identidades.

![Detalhamento: uma solicitação convertida em chamadas para os métodos do provedor](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

O projeto _Microsoft.SCIM.WebHostSample_ é um aplicativo Web do Visual Studio ASP.NET Core, com base no modelo _Empty_. Isso permite que o código de exemplo seja implantado como autônomo, hospedado em contêineres ou dentro de Serviços de Informações da Internet. Ele também implementa a interface _Microsoft.SCIM.IProvider_ mantendo classes na memória como um repositório de identidade de exemplo.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Criando um ponto de extremidade SCIM personalizado

O serviço do SCIM deve ter um endereço HTTP e um certificado de autenticação de servidor do qual a autoridade de certificação raiz é destes nomes:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign
* DST Raiz CA X3

O SDK do .NET Core inclui um certificado de desenvolvimento HTTPS que pode ser usado durante o desenvolvimento, o certificado é instalado como parte da experiência de primeira execução. Dependendo de como você executa o Aplicativo Web ASP.NET Core, ele escutará em uma porta diferente:

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Para saber mais sobre HTTPS no ASP.NET Core, use o seguinte link: [Impor HTTPS no ASP.NET Core](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Manipulando a autenticação do ponto de extremidade

As solicitações do Active Directory do Azure incluem um token de portador do OAuth 2.0. Qualquer serviço que recebe a solicitação deve autenticar o emissor como sendo o Active Directory do Azure para o locatário esperado do Active Directory do Azure.

No token, o emissor é identificado por uma declaração iss, como `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`. Neste exemplo, o endereço básico do valor da declaração, `https://sts.windows.net`, identifica o Azure Active Directory como o emissor, enquanto o segmento de endereço relativo, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, é um identificador exclusivo do locatário do Azure Active Directory para o qual o token foi emitido.

O público-alvo do token será a ID do modelo de aplicativo para o aplicativo na Galeria, cada um dos aplicativos registrados em um único locatário pode receber a mesma declaração de `iss` com solicitações SCIM. A ID do modelo de aplicativo para todos os aplicativos personalizados é _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_. O token gerado pelo serviço de provisionamento do Azure AD só deve ser usado para teste. Ele não devem ser usados em ambientes de produção.

No código de exemplo, as solicitações são autenticadas usando o pacote Microsoft.AspNetCore.Authentication.JwtBearer. O código a seguir impõe que as solicitações para qualquer um dos pontos de extremidade do serviço sejam autenticadas usando o token de portador emitido pelo Azure Active Directory para um locatário especificado:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Um token de portador também é necessário para usar os [testes do postmaster](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) fornecidos e executar a depuração local usando localhost. O código de exemplo usa ambientes ASP.NET Core para alterar as opções de autenticação durante o estágio de desenvolvimento e habilitar o uso de um token autoassinado.

Para obter mais informações sobre vários ambientes no ASP.NET Core, confira [Usar vários ambientes no ASP.NET Core](/aspnet/core/fundamentals/environments).

O código a seguir impõe que as solicitações para qualquer um dos pontos de extremidade do serviço sejam autenticadas usando um token de portador assinado com uma chave personalizada:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        })
        .AddJwtBearer(options =>
        {
            options.TokenValidationParameters =
                new TokenValidationParameters
                {
                    ValidateIssuer = false,
                    ValidateAudience = false,
                    ValidateLifetime = false,
                    ValidateIssuerSigningKey = false,
                    ValidIssuer = "Microsoft.Security.Bearer",
                    ValidAudience = "Microsoft.Security.Bearer",
                    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                };
        });
    }
...
```

Enviar uma solicitação GET para o controlador de token para obter um token de portador válido, o método _GenerateJSONWebToken_ é responsável por criar um token que corresponda aos parâmetros configurados para desenvolvimento:

```csharp
private string GenerateJSONWebToken()
{
    // Create token key
    SymmetricSecurityKey securityKey =
        new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
    SigningCredentials credentials =
        new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

    // Set token expiration
    DateTime startTime = DateTime.UtcNow;
    DateTime expiryTime = startTime.AddMinutes(120);

    // Generate the token
    JwtSecurityToken token =
        new JwtSecurityToken(
            "Microsoft.Security.Bearer",
            "Microsoft.Security.Bearer",
            null,
            notBefore: startTime,
            expires: expiryTime,
            signingCredentials: credentials);

    string result = new JwtSecurityTokenHandler().WriteToken(token);
    return result;
}
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Realização do provisionamento e o desprovisionamento de usuários

***Exemplo 1. Confira o serviço para obter um usuário correspondente***

O AAD (Azure Active Directory) consulta o serviço para encontrar um usuário com um valor de atributo `externalId` correspondente ao valor de atributo mailNickname de um usuário no AAD. A consulta é expressa como solicitação HTTP como no exemplo, na qual jyoung é o exemplo de um mailNickname de um usuário no Azure Active Directory.

>[!NOTE]
> Este é apenas um exemplo. Nem todos os usuários terão um atributo mailNickname, e o valor que um usuário tem pode não ser exclusivo no diretório. Além disso, o atributo usado para correspondência (que nesse caso é `externalId`) será configurável nos [mapeamentos de atributos do AAD](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

No código de exemplo, a solicitação é convertida em uma chamada para o método QueryAsync do provedor do serviço. Veja a assinatura desse método: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  
// Microsoft.SCIM.IQueryParameters is defined in 
// Microsoft.SCIM.Protocol.  

Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

Na consulta de exemplo, para um usuário com um valor fornecido para o atributo `externalId`, os valores dos argumentos passados para o método QueryAsync serão:

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***Exemplo 2. Provisione um usuário***

Caso a resposta a uma consulta ao serviço Web para um usuário com um valor de atributo `externalId` que corresponde ao valor de atributo mailNickname de um usuário não retorne nenhum usuário, o AAD solicitará que o serviço provisione um usuário correspondente ao usuário no AAD.  Veja um exemplo de tal solicitação: 

```
POST https://.../scim/Users HTTP/1.1
Authorization: Bearer ...
Content-type: application/scim+json
{
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung@testuser.com",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

No código de exemplo, a solicitação é convertida em uma chamada para o método CreateAsync do provedor do serviço. Veja a assinatura desse método: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  

Task<Resource> CreateAsync(IRequest<Resource> request);
```

Em uma solicitação para provisionar um usuário, o valor do argumento do recurso é uma instância da classe Microsoft.SCIM.Core2EnterpriseUser, definida na biblioteca Microsoft.SCIM.Schemas.  Se a solicitação para provisionar o usuário for realizada com sucesso, a implementação do método deverá retornar uma instância da classe Microsoft.SCIM.Core2EnterpriseUser, com o valor da propriedade Identifier definido como o identificador exclusivo do usuário recentemente provisionado.  

***Exemplo 3. Confira o estado atual de um usuário*** 

Para atualizar um usuário conhecido que existe em um repositório de identidades administrado por um SCIM, o Azure Active Directory prossegue solicitando o estado atual desse usuário no serviço com uma solicitação como: 

```
GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

No código de exemplo, a solicitação é convertida em uma chamada para o método RetrieveAsync do provedor do serviço. Veja a assinatura desse método: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource and 
// Microsoft.SCIM.IResourceRetrievalParameters 
// are defined in Microsoft.SCIM.Schemas 

Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

No exemplo de uma solicitação para recuperar o estado atual de um usuário, os valores das propriedades do objeto fornecidos como o valor do argumento de parâmetros são: 
  
* Identificador: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Exemplo 4. Confira o valor de um atributo de referência a ser atualizado*** 

Se um atributo de referência deve ser atualizado, o Azure Active Directory consulta o serviço para determinar se o valor atual do atributo de referência no repositório de identidades administrado pelo serviço já corresponde ao valor desse atributo no Azure Active Directory. Para usuários, o único atributo no qual o valor atual é consultado dessa forma é o atributo de gerenciador. Veja o exemplo de uma solicitação para determinar se o atributo de gerenciador de um objeto de usuário atualmente tem um determinado valor: No código de exemplo, a solicitação é convertida em uma chamada para o método QueryAsync do provedor do serviço. O valor das propriedades do objeto fornecido como o valor do argumento de parâmetros é: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: “ID”
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): “ID”
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Aqui, o valor do índice x pode ser 0 e o valor do índice y pode ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, dependendo da ordem das expressões do parâmetro do filtro de consulta.   

***Exemplo 5. Solicitação do Azure AD para um serviço SCIM para atualizar um usuário*** 

Veja um exemplo de uma solicitação do Azure Active Directory a um serviço SCIM para atualizar um usuário: 

```
PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
Content-type: application/scim+json
{
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

No código de exemplo, a solicitação é convertida em uma chamada para o método UpdateAsync do provedor do serviço. Veja a assinatura desse método: 

```csharp
// System.Threading.Tasks.Tasks and 
// System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in
// Microsoft.SCIM.Service.
// Microsoft.SCIM.IPatch, 
// is defined in Microsoft.SCIM.Protocol. 

Task UpdateAsync(IRequest<IPatch> request);
```

No exemplo de uma solicitação para atualizar um usuário, o objeto fornecido como valor do argumento de patch tem estes valores de propriedade: 

|Argumento|Valor|
|-|-|
|ResourceIdentifier.Identifier|"54D382A4-2050-4C03-94D1-E769F1D15682"|
|ResourceIdentifier.SchemaIdentifier|“urn:ietf:params:scim:schemas:extension:enterprise:2.0:User”|
|(PatchRequest as PatchRequest2).Operations.Count|1|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName|OperationName.Add|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath|"manager"|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count|1|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference|http://.../scim/Users/2819c223-7f76-453a-919d-413861904646|
|(PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value| 2819c223-7f76-453a-919d-413861904646|

***Exemplo 6. Desprovisione um usuário***

Para desprovisionar um usuário de um repositório de identidades administrado por um serviço SCIM, o AAD envia uma solicitação como esta:

```
DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

No código de exemplo, a solicitação é convertida em uma chamada para o método DeleteAsync do provedor do serviço. Veja a assinatura desse método: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.IResourceIdentifier, 
// is defined in Microsoft.SCIM.Protocol. 

Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

O objeto fornecido como valor do argumento resourceIdentifier tem estes valores de propriedade no exemplo de uma solicitação para desprovisionar um usuário: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="integrate-your-scim-endpoint-with-the-aad-scim-client"></a>Integrar seu ponto de extremidade do SCIM com o cliente SCIM do AAD

O Azure AD pode ser configurado para provisionar automaticamente usuários e grupos atribuídos a aplicativos que implementam um perfil específico do [protocolo SCIM 2.0](https://tools.ietf.org/html/rfc7644). As especificidades do perfil estão documentadas em [Entender a implementação do SCIM do Azure AD](#understand-the-aad-scim-implementation).

Verifique com o seu provedor de aplicativo ou na documentação do seu provedor de aplicativo as declarações de compatibilidade com esses requisitos.

> [!IMPORTANT]
> A implementação do SCIM do Azure AD é criada sobre o serviço de provisionamento de usuários do Azure AD, projetado para manter constantemente os usuários sincronizados entre o Azure AD e o aplicativo de destino, e implementa um conjunto muito específico de operações padrão. É importante entender esses comportamentos para entender o comportamento do cliente SCIM do Azure AD. Para saber mais, confira a seção [Ciclos de provisionamento: inicial e incremental](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) em [Como funciona o provisionamento](how-provisioning-works.md).

### <a name="getting-started"></a>Introdução

Os aplicativos que dão suporte ao perfil SCIM descrito neste artigo podem ser conectados ao Azure Active Directory usando o recurso de "aplicativo não galeria" na galeria de aplicativos do Azure AD. Uma vez conectado, o Azure AD executa um processo de sincronização a cada 40 minutos, em que ele consulta o ponto de extremidade SCIM do aplicativo para os usuários e grupos atribuídos e os cria ou modifica de acordo com os detalhes da atribuição.

**Para conectar um aplicativo que dê suporte a SCIM:**

1. Entre no [portal do AAD](https://aad.portal.azure.com). Observe que você pode acessar uma avaliação gratuita do Azure Active Directory com licenças P2 inscrevendo-se no [programa do desenvolvedor](https://developer.microsoft.com/office/dev-program)
1. Selecione **Aplicativos empresariais** no painel esquerdo. É mostrada uma lista com todos os aplicativos configurados, incluindo aplicativos que foram adicionados da galeria.
1. Selecione **+ Novo aplicativo** >  **+ Criar seu próprio aplicativo**.
1. Insira um nome para o aplicativo, escolha a opção "*integre qualquer outro aplicativo que você não encontre na galeria*" e selecione **Adicionar** para criar um objeto de aplicativo. O novo aplicativo é adicionado à lista de aplicativos empresariais e é aberto em sua tela de gerenciamento de aplicativo.

   ![Captura de tela que mostra a galeria de aplicativos do Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
   *Galeria de aplicativos do Azure AD*

   > [!NOTE]
   > Se você estiver usando a experiência antiga da galeria de aplicativos, siga a tela guia abaixo.
   
   ![Captura de tela que mostra a experiência antiga da galeria de aplicativos do Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
   *Experiência antiga da galeria de aplicativos do Azure AD*

1. Na tela de gerenciamento de aplicativos, selecione **P Aprovisionamento** no painel esquerdo.
1. No menu **Modo de Provisionamento**, selecione **Automático**.

   ![Exemplo: Página de Provisionamento de um aplicativo no portal do Azure](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Configurar o provisionamento no portal do Azure*

1. No campo **URL do locatário** , insira a URL do ponto de extremidade do SCIM do aplicativo. Exemplo: `https://api.contoso.com/scim/`
1. Se o ponto de extremidade SCIM exigir um token de portador OAuth de um emissor diferente do Azure AD, copie o token de portador OAuth necessário para o campo opcional **Token Secreto**. Se esse campo estiver em branco, o Azure AD inclui um token de portador OAuth emitido do Azure AD com cada solicitação. Aplicativos que usam o Azure AD como provedor de identidade podem validar esse token emitido pelo Azure AD. 
   > [!NOTE]
   > ***Não*** é recomendável deixar esse campo em branco e contar com um token gerado pelo Azure AD. Essa opção está disponível principalmente para fins de teste.
1. Selecione **Testar conectividade** o Azure Active Directory tente se conectar ao ponto de extremidade do SCIM. Se a tentativas falhar, informações de erro serão exibidas.  

    > [!NOTE]
    > **Testar Conexão** consulta o ponto de extremidade SCIM para um usuário que não existe, usando um GUID aleatório como a propriedade correspondente selecionada na configuração do Azure AD. A resposta correta esperada é HTTP 200 OK com uma mensagem de SCIM ListResponse vazia.

1. Se as tentativas de conexão ao aplicativo forem bem-sucedidas, selecione **Salvar** para salvar as credenciais de administrador.
1. Na seção **Mapeamento**, há dois conjuntos selecionáveis de [mapeamentos de atributos](customize-application-attributes.md): um para objetos de usuário e outro para objetos de grupo. Selecione cada um para revisar os atributos que são sincronizados do Azure Active Directory para seu aplicativo. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre os usuários e os grupos no seu aplicativo para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

    > [!NOTE]
    > Opcionalmente, você pode desabilitar a sincronização dos objetos de grupo desabilitando o mapeamento "grupos".

1. Em **Configurações**, o campo **Escopo** define quais usuários e grupos são sincronizados. Selecione **Sincronizar apenas usuários e grupos atribuídos** (recomendado) sincroniza somente usuários e grupos atribuídos na guia **Usuários e grupos**.
1. Depois que a configuração estiver concluída, defina o **Status de provisionamento** para **Ligado**.
1. Selecione **Salvar** para iniciar o serviço de provisionamento do Azure AD.
1. Caso esteja sincronizando apenas usuários e grupos atribuídos (recomendado), certifique-se de selecionar a guia **Usuários e grupos** e designar os usuários ou grupos que você deseja sincronizar.

Depois que o ciclo inicial for iniciado, você poderá selecionar **Logs de provisionamento** no painel esquerdo para monitorar o progresso, que mostra todas as ações realizadas pelo serviço de provisionamento em seu aplicativo. Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](check-status-user-account-provisioning.md).

> [!NOTE]
> O ciclo inicial demora mais do que as sincronizações posteriores, que ocorrem aproximadamente a cada 40 minutos desde que o serviço esteja em execução.

## <a name="publish-your-application-to-the-aad-application-gallery"></a>Publicar seu aplicativo na galeria de aplicativos do AAD

Se você estiver criando um aplicativo que será usado por mais de um locatário, poderá disponibilizá-lo na galeria de aplicativos do Azure AD. Isso permitirá que organizações descubram o aplicativo e configurem o provisionamento com facilidade. Publicar seu aplicativo na galeria do Azure AD e disponibilizar o provisionamento para outras pessoas é fácil. Confira as etapas [aqui](../develop/v2-howto-app-gallery-listing.md). A Microsoft trabalhará com você para integrar seu aplicativo em nossa galeria, testar seu ponto de extremidade e publicar [documentação](../saas-apps/tutorial-list.md) de integração para uso dos clientes.

### <a name="gallery-onboarding-checklist"></a>Lista de verificação de integração da galeria
Use a lista de verificação para integrar seu aplicativo rapidamente e para que os clientes tenham uma experiência de implantação tranquila. As informações serão coletadas quando você estiver realizando a integração à galeria. 
> [!div class="checklist"]
> * Dar suporte a um ponto de extremidade de usuário e grupo do [SCIM 2.0](#understand-the-aad-scim-implementation) (apenas um é necessário, mas ambos são recomendados)
> * Dar suporte a pelo menos 25 solicitações por segundo e por locatário para garantir que usuários e grupos sejam provisionados e desprovisionados sem atraso (obrigatório)
> * Estabelecer contatos de engenharia e suporte para orientar os clientes após a integração à galeria (obrigatório)
> * 3 credenciais de teste sem expiração para seu aplicativo (obrigatório)
> * Dar suporte à concessão de código de autorização OAuth ou a um token de vida útil longa, conforme é descrito abaixo (obrigatório)
> * Estabelecer um ponto de contato de engenharia e suporte para ajudar os clientes após a integração à galeria (obrigatório)
> * [Dar suporte à descoberta de esquema (obrigatório)](https://tools.ietf.org/html/rfc7643#section-6)
> * Dar suporte à atualização de várias associações de grupo com um único PATCH
> * Documentar seu ponto de extremidade do SCIM publicamente

### <a name="authorization-to-provisioning-connectors-in-the-application-gallery"></a>Autorização para conectores de provisionamento na galeria de aplicativos
A especificação do SCIM não define um esquema específico de SCIM para autenticação e autorização e se baseia no uso de padrões existentes do setor.

|Método de autorização|Vantagens|Desvantagens|Suporte|
|--|--|--|--|
|Nome de usuário e senha (não recomendado ou com suporte do Azure AD)|Fácil de implementar|Inseguro – [Sua Pa$$word não importa](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Com suporte caso a caso para aplicativos da galeria. Sem suporte para aplicativos que não são da galeria.|
|Token de portador de vida útil longa|Os tokens de vida útil longa não exigem que um usuário esteja presente. Eles são fáceis para os administradores usarem ao configurar o provisionamento.|Os tokens de vida útil longa podem ser difíceis de compartilhar com um administrador sem usar métodos inseguros, como email. |Com suporte para aplicativos que não são e que são da galeria. |
|Concessão de código de autorização OAuth|Os tokens de acesso são muito mais curtos do que as senhas e têm um mecanismo de atualização automatizado que os tokens de portador de vida útil longa não têm.  Um usuário real deve estar presente durante a autorização inicial, adicionando um nível de responsabilidade. |Exige que um usuário esteja presente. Se o usuário sair da organização, o token será inválido e a autorização precisará ser realizada novamente.|Com suporte para aplicativos da galeria, porém não para aplicativos inexistentes na galeria. No entanto, é possível fornecer um token de acesso na interface do usuário como um token secreto para fins de teste de curto prazo. O suporte para concessão de código OAuth em aplicativos que não são da galeria está em nossa lista de pendências, além do suporte a URLs de autenticação/token configuráveis no aplicativo da galeria.|
|Concessão de credenciais de cliente do OAuth|Os tokens de acesso são muito mais curtos do que as senhas e têm um mecanismo de atualização automatizado que os tokens de portador de vida útil longa não têm. A concessão de código de autorização e a concessão de credenciais de cliente criam o mesmo tipo de token de acesso; portanto, a transferência entre esses métodos é transparente para a API.  O provisionamento pode ser completamente automatizado e novos tokens podem ser silenciosamente solicitados sem interação do usuário. ||Sem suporte para aplicativos que não são e que são da galeria. O suporte está em nossa pendência.|

> [!NOTE]
> Não é recomendável deixar o campo de token em branco na interface do usuário do aplicativo personalizado de configuração de provisionamento do AAD. O token gerado está disponível principalmente para fins de teste.

### <a name="oauth-code-grant-flow"></a>Fluxo de concessão de código OAuth

O serviço de provisionamento dá suporte à [concessão de código de autorização](https://tools.ietf.org/html/rfc6749#page-24) e, depois de enviar sua solicitação para publicar seu aplicativo na galeria, nossa equipe trabalhará com você para coletar as seguintes informações:

- **URL de Autorização**, uma URL pelo cliente para obter autorização do proprietário do recurso por meio do redirecionamento de agente do usuário. O usuário é redirecionado para essa URL para autorizar o acesso. 

- **URL de troca de token**, uma URL pelo cliente para trocar uma concessão de autorização para um token de acesso, normalmente com autenticação de cliente.

- **ID de Cliente**, o servidor de autorização emite ao cliente registrado um identificador de cliente, que é uma cadeia de caracteres exclusiva que representa as informações de registro fornecidas pelo cliente.  O identificador do cliente não é um segredo; ele é exposto ao proprietário do recurso e **não deve** ser usado por si só para autenticação do cliente.  

- **Segredo do cliente**, um segredo gerado pelo servidor de autorização que deve ser um valor exclusivo conhecido apenas pelo servidor de autorização. 

> [!NOTE]
> A **URL de Autorização** e a **URL de troca de token** atualmente não são configuráveis por locatário.

> [!NOTE]
> O OAuth v1 não tem suporte devido à exposição do segredo do cliente. Há suporte para OAuth v2.  

Melhores práticas (recomendadas, mas não obrigatórias):
* Suporte a várias URLs de redirecionamento. Os administradores podem configurar o provisionamento de "portal.azure.com" e "aad.portal.azure.com". O suporte a várias URLs de redirecionamento garantirá que os usuários possam autorizar o acesso de ambos os portais.
* Dê suporte a vários segredos para fácil renovação, sem tempo de inatividade. 

#### <a name="how-to-setup-oauth-code-grant-flow"></a>Como configurar o fluxo de concessão de código OAuth

1. Entre no portal do Azure, acesse **Aplicativos empresariais** > **Aplicativo** > **Provisionamento** e selecione **Autorizar**.

   1. O portal do Azure redirecionará o usuário para a URL de Autorização (página de entrada para o aplicativo de terceiros).

   1. O administrador fornecerá credenciais para o aplicativo de terceiros. 

   1. O aplicativo de terceiros redirecionará o usuário de volta para o portal do Azure e fornecerá o código de concessão 

   1. Os serviços de provisionamento do Azure AD chamarão a URL do token e fornecerão o código de concessão. O aplicativo de terceiros responderá com o token de acesso, o token de atualização e a data de expiração

1. Quando o ciclo de provisionamento for iniciado, o serviço verificará se o token de acesso atual é válido e o trocará por um novo token, se necessário. Um token de acesso será fornecido em cada solicitação feita ao aplicativo e a validade da solicitação será verificada antes de cada solicitação.

> [!NOTE]
> Embora atualmente não seja possível configurar o OAuth nos aplicativos de fora da galeria, você poderá gerar um token de acesso de modo manual no servidor de autorização e inseri-lo como o token secreto de um aplicativo que não é da galeria. Isso permitirá verificar a compatibilidade do seu servidor SCIM com um cliente SCIM do AAD antes de realizar a integração à galeria de aplicativos, que é compatível com a concessão do código OAuth.  

**Tokens de portador OAuth de longa vida:** Se o seu aplicativo não der suporte ao fluxo de concessão de código de autorização OAuth, gere um token de portador OAuth de vida útil longa que um administrador possa usar para configurar a integração de provisionamento. O token deve ser perpétuo, caso contrário, o trabalho de provisionamento ficará [em quarentena](application-provisioning-quarantine-status.md) quando o token expirar.

Para métodos de autenticação e autorização adicionais, entre em contato pelo [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Lista de verificação de inicialização do lançamento na galeria
Para ajudar a impulsionar o reconhecimento e a demanda de nossa integração conjunta, recomendamos que você atualize sua documentação existente e amplifique a integração em seus canais de marketing.  A seguir está um conjunto de atividades de lista de verificação que recomendamos que você realize para dar suporte ao lançamento

> [!div class="checklist"]
> * Verifique se suas equipes de vendas e de suporte ao cliente estão cientes, prontas e podem se comunicar com os recursos de integração. Comunique suas equipes, forneça perguntas frequentes e inclua a integração em seus materiais de vendas. 
> * Crie uma postagem no blog ou um comunicado à imprensa que descreve a integração conjunta, os benefícios e como começar. [Exemplo: Comunicado à imprensa da Imprivata e do Azure Active Directory](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
> * Utilize redes sociais, como o Twitter, o Facebook ou o LinkedIn, para promover a integração aos seus clientes. Certifique-se de incluir @AzureAD de forma que possamos retweetar sua postagem. [Exemplo: Postagem do Twitter da Imprivata](https://twitter.com/azuread/status/1123964502909779968)
> * Crie ou atualize suas páginas/seu site de marketing (por exemplo, página de integração, página de parceiro, página de preços etc.) para incluir a disponibilidade da integração conjunta. [Exemplo: Página de integração do Pingboard](https://pingboard.com/org-chart-for), [página de integração do Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [página de preços do Monday.com](https://monday.com/pricing/) 
> * Crie um artigo da central de ajuda ou documentação técnica sobre como os clientes podem começar. [Exemplo: Integração do Envoy + Microsoft Azure Active Directory.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
> * Alerte os clientes da nova integração por meio de comunicação dos seus clientes (boletins informativos mensais, campanhas de email, notas de versão de produto). 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Desenvolver um exemplo de ponto de extremidade de SCIM](use-scim-to-build-users-and-groups-endpoints.md)
> [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
> [Personalizar mapeamentos de atributo para provisionamento de usuários](customize-application-attributes.md)
> [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
> [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
> [Notificações de provisionamento de conta](user-provisioning.md)
> [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
