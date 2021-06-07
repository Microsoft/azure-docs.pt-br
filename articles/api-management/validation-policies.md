---
title: Políticas de validação do gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas que você pode usar no gerenciamento de API do Azure para validar solicitações e respostas.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 1a835d26b4c41c92b9849856a2f31b3550947bd8
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801886"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>Políticas de gerenciamento de API para validar solicitações e respostas

Este artigo fornece uma referência para as seguintes políticas de gerenciamento de API. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](./api-management-policies.md).

Use políticas de validação para validar solicitações e respostas de API em um esquema OpenAPI e proteger contra vulnerabilidades como injeção de cabeçalhos ou carga. Embora não seja uma substituição para um firewall do aplicativo Web, as políticas de validação fornecem flexibilidade para responder a uma classe adicional de ameaças que não são cobertas por produtos de segurança que dependem de regras predefinidas e estáticas.

## <a name="validation-policies"></a>Políticas de validação

- [Validar conteúdo](#validate-content) – valida o tamanho ou o esquema JSON de um corpo de solicitação ou resposta em relação ao esquema de API. 
- [Validar parâmetros](#validate-parameters) – valida os parâmetros do cabeçalho, da consulta ou do caminho da solicitação em relação ao esquema da API.
- [Validar cabeçalhos](#validate-headers) – valida os cabeçalhos de resposta em relação ao esquema de API.
- [Validar o código de status](#validate-status-code) -valida os códigos de status HTTP em respostas em relação ao esquema de API.

> [!NOTE]
> O tamanho máximo do esquema de API que pode ser usado por uma política de validação é 4 MB. Se o esquema exceder esse limite, as políticas de validação retornarão erros no tempo de execução. Para aumentá-lo, entre em contato com o [suporte](https://azure.microsoft.com/support/options/). 

## <a name="actions"></a>Ações

Cada política de validação inclui um atributo que especifica uma ação, que o gerenciamento de API usa ao validar uma entidade em uma solicitação de API ou resposta em relação ao esquema de API. Uma ação pode ser especificada para elementos que são representados no esquema de API e, dependendo da política, para elementos que não são representados no esquema de API. Uma ação especificada no elemento filho de uma política substitui uma ação especificada para seu pai.

Ações disponíveis:

| Ação         | Descrição          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| ignore | Ignorar validação. |
| prevenir | Bloqueie o processamento da solicitação ou da resposta, registre o erro de validação detalhada e retorne um erro. O processamento é interrompido quando o primeiro conjunto de erros é detectado. |
| detectar | Erros de validação de log, sem interromper o processamento de solicitação ou resposta. |

## <a name="logs"></a>Logs

Os detalhes sobre os erros de validação durante a execução da política são registrados na variável `context.Variables` especificada no `errors-variable-name` atributo no elemento raiz da política. Quando configurado em uma `prevent` ação, um erro de validação bloqueia o processamento de solicitação ou resposta adicional e também é propagado para a `context.LastError` propriedade. 

Para investigar os erros, use uma política de [rastreamento](api-management-advanced-policies.md#Trace) para registrar em log os erros de variáveis de contexto para [Application insights](api-management-howto-app-insights.md).

## <a name="performance-implications"></a>Implicações de desempenho

Adicionar políticas de validação pode afetar a taxa de transferência da API. Os seguintes princípios gerais se aplicam:
* Quanto maior o tamanho do esquema da API, menor será a taxa de transferência. 
* Quanto maior a carga em uma solicitação ou resposta, menor será a taxa de transferência. 
* O tamanho do esquema da API tem um impacto maior no desempenho do que o tamanho da carga. 
* A validação em relação a um esquema de API que tenha vários megabytes de tamanho pode causar tempos limite de solicitação ou resposta em algumas condições. O efeito é mais pronunciado nas camadas de  **consumo** e de **desenvolvedor** do serviço. 

É recomendável executar testes de carga com suas cargas de trabalho de produção esperadas para avaliar o impacto das políticas de validação na taxa de transferência da API.

## <a name="validate-content"></a>Validar o conteúdo

A `validate-content` política valida o tamanho ou o esquema JSON de um corpo de solicitação ou resposta em relação ao esquema de API. Formatos diferentes de JSON não têm suporte.

### <a name="policy-statement"></a>Declaração de política

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Exemplo

No exemplo a seguir, a carga JSON em solicitações e respostas é validada no modo de detecção. Mensagens com cargas maiores que 100 KB são bloqueadas. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Elementos

| Nome         | Descrição                                                                                                                                   | Obrigatório |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validar-conteúdo | Elemento raiz.                                                                                                                               | Sim      |
| conteúdo | Adicione um ou mais desses elementos para validar o tipo de conteúdo na solicitação ou resposta e execute a ação especificada.  | Não |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Obrigatório | Padrão |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| ação de tipo de conteúdo não especificado | [Ação](#actions) a ser executada para solicitações ou respostas com um tipo de conteúdo que não é especificado no esquema de API. |  Sim     | N/D   |
| tamanho máximo | O comprimento máximo do corpo da solicitação ou resposta em bytes, verificado em relação ao `Content-Length` cabeçalho. Se o corpo da solicitação ou o corpo da resposta for compactado, esse valor será o comprimento descompactado. Valor máximo permitido: 102.400 bytes (100 KB).  | Sim       | N/D   |
| tamanho-excedido-ação | [Ação](#actions) a ser executada para solicitações ou respostas cujo corpo excede o tamanho especificado em `max-size` . |  Sim     | N/D   |
| erros-variável-nome | Nome da variável no `context.Variables` para registrar erros de validação para.  |   Sim    | N/D   |
| type | Tipo de conteúdo para executar a validação de corpo para, verificado em relação ao `Content-Type` cabeçalho. Esse valor não diferencia maiúsculas de minúsculas. Se estiver vazio, ele se aplicará a todos os tipos de conteúdo especificados no esquema de API. |   Não    |  N/D  |
| validar como | Mecanismo de validação a ser usado para validação do corpo de uma solicitação ou resposta com um tipo de conteúdo correspondente. Atualmente, o único valor com suporte é "JSON".   |  Sim     |  N/D  |
| ação | [Ação](#actions) a ser executada para solicitações ou respostas cujo corpo não corresponde ao tipo de conteúdo especificado.  |  Sim      | N/D   |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** de entrada, de saída, em caso de erro

-   **Escopos da política:** todos os escopos

## <a name="validate-parameters"></a>Validar parâmetros

A `validate-parameters` política valida os parâmetros de cabeçalho, de consulta ou de caminho em solicitações em relação ao esquema de API.

> [!IMPORTANT]
> Se você importou uma API usando uma versão de API de gerenciamento anterior ao `2021-01-01-preview` , a `validate-parameters` política pode não funcionar. Talvez seja necessário [reimportar sua API usando a](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) versão `2021-01-01-preview` de API de gerenciamento ou posterior.


### <a name="policy-statement"></a>Declaração de política

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Exemplo

Neste exemplo, todos os parâmetros de consulta e caminho são validados no modo de prevenção e nos cabeçalhos no modo de detecção. A validação é substituída para vários parâmetros de cabeçalho:

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>Elementos

| Nome         | Descrição                                                                                                                                   | Obrigatório |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validate-Parameters | Elemento raiz. Especifica ações de validação padrão para todos os parâmetros em solicitações.                                                                                                                              | Sim      |
| headers | Adicione este elemento para substituir as ações de validação padrão para parâmetros de cabeçalho em solicitações.   | Não |
| Consulta | Adicione este elemento para substituir as ações de validação padrão para parâmetros de consulta em solicitações.  | Não |
| caminho | Adicione este elemento para substituir as ações de validação padrão para parâmetros de caminho de URL em solicitações.  | Não |
| parâmetro | Adicione um ou mais elementos para parâmetros nomeados para substituir a configuração de nível superior das ações de validação. | Não |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Obrigatório | Padrão |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| ação de parâmetro especificada | [Ação](#actions) a ser executada para parâmetros de solicitação especificados no esquema de API. <br/><br/> Quando fornecido em um `headers` `query` elemento,, ou `path` , o valor substitui o valor de `specified-parameter-action` no `validate-parameters` elemento.  |  Sim     | N/D   |
| ação de parâmetro não especificada | [Ação](#actions) a ser executada para parâmetros de solicitação que não são especificados no esquema de API. <br/><br/>Quando fornecido em um `headers` `query` elemento ou, o valor substitui o valor de `unspecified-parameter-action` no `validate-parameters` elemento. |  Sim     | N/D   |
| erros-variável-nome | Nome da variável no `context.Variables` para registrar erros de validação para.  |   Sim    | N/D   |
| name | Nome do parâmetro para o qual substituir a ação de validação. Esse valor não diferencia maiúsculas de minúsculas.  | Sim | N/D |
| ação | [Ação](#actions) a ser executada para o parâmetro com o nome correspondente. Se o parâmetro for especificado no esquema de API, esse valor substituirá a configuração de nível superior `specified-parameter-action` . Se o parâmetro não for especificado no esquema de API, esse valor substituirá a configuração de nível superior `unspecified-parameter-action` .| Sim | N/D | 

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** de entrada

-   **Escopos da política:** todos os escopos

## <a name="validate-headers"></a>Validar cabeçalhos

A `validate-headers` política valida os cabeçalhos de resposta em relação ao esquema de API.

> [!IMPORTANT]
> Se você importou uma API usando uma versão de API de gerenciamento anterior ao `2021-01-01-preview` , a `validate-headers` política pode não funcionar. Talvez seja necessário reimportar sua API usando a versão `2021-01-01-preview` de API de gerenciamento ou posterior.

### <a name="policy-statement"></a>Declaração de política

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Exemplo

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Elementos

| Nome         | Descrição                                                                                                                                   | Obrigatório |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validar cabeçalhos | Elemento raiz. Especifica as ações de validação padrão para todos os cabeçalhos em respostas.                                                                                                                              | Sim      |
| header | Adicione um ou mais elementos para cabeçalhos nomeados para substituir as ações de validação padrão para cabeçalhos em respostas. | Não |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Obrigatório | Padrão |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| especificado-cabeçalho-ação | [Ação](#actions) a ser executada para cabeçalhos de resposta especificados no esquema de API.  |  Sim     | N/D   |
| não especificado-cabeçalho-ação | [Ação](#actions) a ser executada para cabeçalhos de resposta que não estão especificados no esquema de API.  |  Sim     | N/D   |
| erros-variável-nome | Nome da variável no `context.Variables` para registrar erros de validação para.  |   Sim    | N/D   |
| name | Nome do cabeçalho para o qual substituir a ação de validação. Esse valor não diferencia maiúsculas de minúsculas. | Sim | N/D |
| ação | [Ação](#actions) a ser executada para o cabeçalho com o nome correspondente. Se o cabeçalho for especificado no esquema de API, esse valor substituirá o valor de `specified-header-action` no `validate-headers` elemento. Caso contrário, ele substitui o valor de `unspecified-header-action` no elemento Validate-Headers. | Sim | N/D | 

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** saída, em caso de erro

-   **Escopos da política:** todos os escopos

## <a name="validate-status-code"></a>Validar o código de status

A `validate-status-code` política valida os códigos de status HTTP em respostas em relação ao esquema de API. Essa política pode ser usada para evitar o vazamento de erros de back-end, que podem conter rastreamentos de pilha. 

### <a name="policy-statement"></a>Declaração de política

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Exemplo

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Elementos

| Nome         | Descrição                                                                                                                                   | Obrigatório |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validate-status-código | Elemento raiz.                                                                                                | Sim      |
| status-code | Adicione um ou mais elementos para códigos de status HTTP para substituir a ação de validação padrão para códigos de status em respostas. | Não |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Obrigatório | Padrão |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| não especificado-status-ação-Action | [Ação](#actions) a ser executada para códigos de status HTTP em respostas que não são especificadas no esquema de API.  |  Sim     | N/D   |
| erros-variável-nome | Nome da variável no `context.Variables` para registrar erros de validação para.  |   Sim    | N/D   |
| code | Código de status HTTP para o qual substituir a ação de validação. | Sim | N/D |
| ação | [Ação](#actions) a ser executada para o código de status correspondente, que não está especificado no esquema de API. Se o código de status for especificado no esquema de API, essa substituição não terá efeito. | Sim | N/D | 

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

-   **Seções de política:** saída, em caso de erro

-   **Escopos da política:** todos os escopos


## <a name="validation-errors"></a>Erros de validação
A tabela a seguir lista todos os possíveis erros das políticas de validação. 

* **Detalhes** -podem ser usados para investigar erros. Não deve ser compartilhado publicamente.
* **Resposta pública** -erro retornado ao cliente. Não vaza detalhes de implementação.

| **Nome**                             | **Tipo**                                                        | **Regra de validação** | **Detalhes**                                                                                                                                       | **Resposta pública**                                                                                                                       | **Ação**           |
|----|----|---|---|---|----|
| **validar-conteúdo** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | O corpo da solicitação tem {size} bytes de comprimento e excede o limite configurado de {maxSize} bytes.                                                       | O corpo da solicitação tem {size} bytes de comprimento e excede o limite de {maxSize} bytes.                                                          | detectar/impedir |
||ResponseBody                                                    | SizeLimit           | O corpo da resposta tem {size} bytes de comprimento e excede o limite configurado de {maxSize} bytes.                                                      | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| {messageContentType}                 | RequestBody                                                     | Não Especificado         | O tipo de conteúdo não especificado {messageContentType} não é permitido.                                                                                     | O tipo de conteúdo não especificado {messageContentType} não é permitido.                                                                             | detectar/impedir |
| {messageContentType}                 | ResponseBody                                                    | Não Especificado         | O tipo de conteúdo não especificado {messageContentType} não é permitido.                                                                                     | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| | ApiSchema                                                       |                     | O esquema da API não existe ou não pôde ser resolvido.                                                                                          | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
|                                      | ApiSchema                                                       |                     | O esquema da API não especifica definições.                                                                                                        | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| {messageContentType}                 | RequestBody/ResponseBody                                      | MissingDefinition   | O esquema da API não contém a definição {definitionname}, que está associada ao tipo de conteúdo {messageContentType}.                        | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| {messageContentType}                 | RequestBody                                                     | IncorrectMessage    | O corpo da solicitação não está de acordo com a definição {definitionname}, que está associada ao tipo de conteúdo {messageContentType}.<br/><br/>{valError. Message} linha: {valError. LineNumber}, posição: {valError. LinePosition}                  | O corpo da solicitação não está de acordo com a definição {definitionname}, que está associada ao tipo de conteúdo {messageContentType}.<br/><br/>{valError. Message} linha: {valError. LineNumber}, posição: {valError. LinePosition}            | detectar/impedir |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | O corpo da resposta não está de acordo com a definição {definitionname}, que está associada ao tipo de conteúdo {messageContentType}.<br/><br/>{valError. Message} linha: {valError. LineNumber}, posição: {valError. LinePosition}                                       | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
|                                      | RequestBody                                                     | Validaçãoexception | O corpo da solicitação não pode ser validado para o tipo de conteúdo {messageContentType}.<br/><br/>{detalhes da exceção}                                                                | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
|                                      | ResponseBody                                                    | Validaçãoexception | O corpo da resposta não pode ser validado para o tipo de conteúdo {messageContentType}.<br/><br/>{detalhes da exceção}                                                                | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| **Validate-Parameter/Validate-Headers** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName}/{HeaderName}           | QueryParameter / PathParameter / RequestHeader                  | Não Especificado         | {Path/parâmetro de consulta/header não especificado} {paramName} não é permitido.                                                               | {Path/parâmetro de consulta/header não especificado} {paramName} não é permitido.                                                       | detectar/impedir |
| HeaderName                         | ResponseHeader                                                  | Não Especificado         | O cabeçalho não especificado {HeaderName} não é permitido.                                                                                                   | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
|                                      |ApiSchema                                                       |                     | O esquema da API não existe ou não pôde ser resolvido.                                                                                            | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
|                                       | ApiSchema                                                       |                     | O esquema de API não especifica definições.                                                                                                          | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| paramName                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | O esquema da API não contém a definição {definitionname}, que está associada ao {parâmetro/cabeçalho de parâmetro/caminho de consulta} {paramName}.  | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| paramName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | A solicitação não pode conter vários valores para o {parâmetro/cabeçalho do parâmetro/caminho de consulta} {paramName}.                                           | A solicitação não pode conter vários valores para o {parâmetro/cabeçalho do parâmetro/caminho de consulta} {paramName}.                                   | detectar/impedir |
| HeaderName                         | ResponseHeader                                                  | IncorrectMessage    | A resposta não pode conter vários valores para o cabeçalho {HeaderName}.                                                                              | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| paramName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | O valor do {Parameter/parâmetro de caminho/cabeçalho de consulta} {paramName} não está de acordo com a definição.<br/><br/>{valError. Message} linha: {valError. LineNumber}, posição: {valError. LinePosition}                                          | O valor do {Parameter/parâmetro de caminho/cabeçalho de consulta} {paramName} não está de acordo com a definição.<br/><br/>{valError. Message} linha: {valError. LineNumber}, posição: {valError. LinePosition}                              | detectar/impedir |
| HeaderName                         | ResponseHeader                                                  | IncorrectMessage    | O valor do cabeçalho {HeaderName} não está de acordo com a definição.<br/><br/>{valError. Message} linha: {valError. LineNumber}, posição: {valError. LinePosition}                                                                              | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| paramName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | O valor do {parâmetro de consulta/parâmetro/cabeçalho do caminho} {paramName} não pode ser analisado de acordo com a definição. <br/><br/>Estendi. Exibida                                | O valor do {parâmetro de consulta/parâmetro/cabeçalho do caminho} {paramName} não pôde ser analisado de acordo com a definição. <br/><br/>Estendi. Exibida                      | detectar/impedir |
| HeaderName                         | ResponseHeader                                                  | IncorrectMessage    | O valor do cabeçalho {HeaderName} não pôde ser analisado de acordo com a definição.                                                                  | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| paramName                          | QueryParameter / PathParameter / RequestHeader                  | ValidationError     | {Parâmetro/cabeçalho de parâmetro/caminho de consulta} {paramName} não pode ser validado.<br/><br/>{detalhes da exceção}                                                                      | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| HeaderName                         | ResponseHeader                                                  | ValidationError     | O cabeçalho {HeaderName} não pode ser validado.<br/><br/>{detalhes da exceção}                                                                                                          | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |
| **Validate-status-código**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {status-código}                        | StatusCode                                                      | Não Especificado         | O código de status de resposta {status-código} não é permitido.                                                                                                | A solicitação não pôde ser processada devido a um erro interno. Contate o proprietário da API.                                                       | detectar/impedir |


A tabela a seguir lista todos os valores possíveis de motivo de um erro de validação junto com os possíveis valores de mensagem:

|  **Motivo**         |    **Mensagem** |
|---|---|  
| Solicitação incorreta       |     {Details} para variável de contexto, {resposta pública} para o cliente|
| Resposta não permitida  | {Details} para variável de contexto, {resposta pública} para o cliente |






## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como trabalhar com políticas, consulte:

-   [Políticas no Gerenciamento de API](api-management-howto-policies.md)
-   [Transformar APIs](transform-api.md)
-   [Referência de política](./api-management-policies.md) para uma lista completa de instruções de política e suas configurações
-   [Exemplos de política](./policy-reference.md)
-   [Tratamento de erros](./api-management-error-handling-policies.md)
