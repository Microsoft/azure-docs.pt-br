---
title: Políticas entre domínios de Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas entre domínios disponíveis para uso no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 86c61679a73f03f7e54bba746107685796ec07c9
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442307"
---
# <a name="api-management-cross-domain-policies"></a>Políticas entre domínios de Gerenciamento de API
Este tópico fornece uma referência para as políticas de Gerenciamento de API a seguir. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a> Políticas entre domínios

- [Permitir chamadas entre domínios](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Torna a API acessível por meio de clientes Adobe Flash e Microsoft Silverlight baseados em navegadores.
- [CORS](api-management-cross-domain-policies.md#CORS) - Adicionar suporte de compartilhamento de recursos entre origens (CORS) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegadores.
- [JSONP](api-management-cross-domain-policies.md#JSONP) - Adiciona suporte JSON com preenchimento (JSONP) a uma operação ou a uma API para permitir chamadas entre domínios de clientes JavaScript baseados em navegadores.

## <a name="AllowCrossDomainCalls"></a> Permitir chamadas entre domínios
Use a política `cross-domain` para tornar a API acessível por clientes baseados em navegadores do Adobe Flash e do Microsoft Silverlight.

### <a name="policy-statement"></a>Declaração de política

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Exemplo

```xml
<cross-domain>
    <cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain>
</cross-domain>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|cross-domain|Elemento raiz. Elementos filho devem estar de acordo com a [Especificação de arquivo de política entre domínios do Adobe](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Sim|

### <a name="usage"></a>Uso
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

- **Seções de política:** de entrada
- **Escopos da política:** todos os escopos

## <a name="CORS"></a> CORS
A política `cors` adiciona suporte do CORS (compartilhamento de recurso entre origens) a uma operação ou API para permitir chamadas entre domínios de clientes baseados em navegador.

O CORS permite que um navegador e um servidor interajam e determina e solicitações entre origens específicas devem ou não ser aceitas (por exemplo, chamadas XMLHttpRequests feitas por meio de JavaScript em uma página da Web para outros domínios). Isso permite maior flexibilidade do que permitir somente solicitações com a mesma origem, mas é mais seguro do que permitir todas as solicitações entre origens.

### <a name="policy-statement"></a>Declaração de política

```xml
<cors allow-credentials="false|true">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Exemplo
Este exemplo demonstra como dar suporte a solicitações preliminares, como as com cabeçalhos personalizados ou métodos diferentes de GET e POST. Para oferecer suporte a cabeçalhos personalizados e verbos HTTP adicionais, use as seções `allowed-methods` e `allowed-headers` conforme mostrado no exemplo a seguir.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|Padrão|
|----------|-----------------|--------------|-------------|
|cors|Elemento raiz.|Sim|N/D|
|allowed-origins|Contém elementos `origin` que descrevem as origens permitidas para solicitações entre domínios. `allowed-origins` pode conter um único elemento `origin` que especifica `*` para permitir qualquer origem, ou um ou mais elementos `origin` que contêm uma URI.|Sim|N/D|
|origin|O valor pode ser `*` para permitir todas as origens ou um URI que especifica uma origem única. O URI deve incluir um esquema, um host e uma porta.|Sim|Se a porta for omitida em um URI, a porta 80 é usada para HTTP e a porta 443 é usada para HTTPS.|
|allowed-methods|Esse elemento é necessário se métodos diferentes de GET ou POST forem permitidos. Contém elementos `method` que especificam os verbos HTTP compatíveis.|Não|Se esta seção não estiver presente, GET e POST são compatíveis.|
|method|Especifica um verbo HTTP.|Pelo menos um elemento `method` é necessário se a seção `allowed-methods` estiver presente.|N/D|
|allowed-headers|Esse elemento contém elementos `header` que especificam os nomes dos cabeçalhos que podem ser incluídos na solicitação.|Não|N/D|
|expose-headers|Esse elemento contém elementos `header` que especificam os nomes dos cabeçalhos que ficarão acessíveis para o cliente.|Não|N/D|
|cabeçalho|Especifica um nome de cabeçalho.|Pelo menos um elemento `header` é necessário em `allowed-headers` ou `expose-headers` se a seção estiver presente.|N/D|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Padrão|
|----------|-----------------|--------------|-------------|
|allow-credentials|O cabeçalho `Access-Control-Allow-Credentials` na resposta preliminar será definido com o valor desse atributo e afetará a capacidade do cliente para enviar credenciais nas solicitações entre domínios.|Não|false|
|preflight-result-max-age|O cabeçalho `Access-Control-Max-Age` na resposta preliminar será definido com o valor desse atributo e afetará a capacidade do agente do usuário para colocar em cache a resposta preliminar.|Não|0|

### <a name="usage"></a>Uso
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

- **Seções de política:** de entrada
- **Escopos da política:** todos os escopos

## <a name="JSONP"></a> JSONP
A política `jsonp` adiciona suporte a JSONP com padding (JSONP) a uma operação ou API para permitir chamadas entre domínios de clientes JavaScript baseados em navegador. O JSONP é um método usado em programas JavaScript para solicitar dados de um servidor em um domínio diferente. O JSONP ignora a limitação aplicada pela maioria dos navegadores da Web quando o acesso às páginas da Web precisa ser do mesmo domínio.

### <a name="policy-statement"></a>Declaração de política

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Exemplo

```xml
<jsonp callback-parameter-name="cb" />
```

Se você chamar o método sem o parâmetro de retorno de chamada ?cb=XXX, será retornado o JSON simples (sem um wrapper de chamada de função).

Se você adicionar o parâmetro de retorno de chamada `?cb=XXX`, será retornado um resultado JSONP, dispondo os resultados JSON originais em torno da função de retorno de chamada como `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementos

|Nome|Descrição|Necessário|
|----------|-----------------|--------------|
|jsonp|Elemento raiz.|Sim|

### <a name="attributes"></a>Atributos

|Nome|Descrição|Necessário|Padrão|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|A chamada da função JavaScript entre domínios, prefixada com o nome do domínio onde a função reside totalmente qualificado.|Sim|N/D|

### <a name="usage"></a>Uso
Essa política pode ser usada nas [seções](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.

- **Seções de política:** saída
- **Escopos da política:** todos os escopos

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no Gerenciamento de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência de Política](api-management-policy-reference.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)
