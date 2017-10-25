---
title: "Políticas de cache no Gerenciamento de API do Azure | Microsoft Docs"
description: "Saiba mais sobre as políticas de cache disponíveis para uso no Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: c7604fdb948a2f4d2adca5d6821d9ea36e96dae6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-caching-policies"></a>Políticas de cache do Gerenciamento de API
Este tópico fornece uma referência para as políticas de Gerenciamento de API a seguir. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CachingPolicies"></a> Políticas de cache  
  
-   Resposta das políticas de cache  
  
    -   [Obter do cache](api-management-caching-policies.md#GetFromCache): executa a pesquisa em cache e retorna uma resposta válida armazenada em cache quando disponível.  
  
    -   [Armazenar em cache](api-management-caching-policies.md#StoreToCache): armazena a resposta em cache de acordo com a configuração de controle de cache especificada.  
  
-   Valor das políticas de cache  
  
    -   [Obter valor do cache](#GetFromCacheByKey) - Recupere um item em cache por chave.  
  
    -   [Armazenar valor em cache](#StoreToCacheByKey) -Armazene um item no cache por chave.  
  
    -   [Remover o valor do cache](#RemoveCacheByKey) - remove um item no cache por chave.  
  
##  <a name="GetFromCache"></a> Obter do cache  
 Use a política `cache-lookup` para realizar pesquisas e retornar uma resposta válida em cache quando disponível. Esta política deve ser aplicada em casos nos quais o conteúdo da resposta permanece estático por um período de tempo. O cache das respostas reduz os requisitos de largura de banda e processamento exigidos do servidor Web de back-end e reduz a latência percebida pelos consumidores da API.  
  
> [!NOTE]
>  É necessário ter uma política correspondente de [Armazenar em cache](api-management-caching-policies.md#StoreToCache).  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">  
  <vary-by-header>Accept</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Accept-Charset</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Authorization</vary-by-header>  
  <!-- should be present when allow-authorized-response-caching is "true"-->  
  <vary-by-header>header name</vary-by-header>  
  <!-- optional, can repeated several times -->  
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>  
  <!-- optional, can repeated several times -->  
</cache-lookup>  
```  
  
### <a name="examples"></a>Exemplos  
  
#### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true">  
            <vary-by-query-parameter>version</vary-by-query-parameter>  
        </cache-lookup>           
    </inbound>  
    <outbound>  
        <cache-store duration="seconds" />  
        <base />          
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Exemplo usando expressões de política  
 Este exemplo mostra como configurar a duração do cache de resposta do Gerenciamento de API que corresponde ao cache de resposta do serviço de back-end, conforme especificado pela diretiva `Cache-Control` do serviço de backup. Para ver uma demonstração da configuração e do uso dessa política, veja [Abordagem da Nuvem Episódio 177: Mais Recursos de Gerenciamento de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avance para 25:25.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 Para saber mais, veja [Expressões de política](api-management-policy-expressions.md) e [Variável de contexto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|cache-lookup|Elemento raiz.|Sim|  
|vary-by-header|Inicie o cache de respostas por valor de cabeçalho especificado, como Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, Expect, From, Host, If-Match.|Não|  
|vary-by-query-parameter|Inicie o cache de respostas de acordo com o valor dos parâmetros de consulta especificados. Insira somente um ou múltiplos parâmetros. Use ponto e vírgula como um separador. Se nenhum for especificado, todos os parâmetros de consulta serão usados.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|allow-private-response-caching|Quando definido como `true`, permite armazenar em cache as solicitações que contêm um cabeçalho Authorization.|Não|false|  
|downstream-caching-type|Este atributo deve ser definido como um dos valores a seguir.<br /><br /> -   none: o cache downstream não é permitido.<br />-   private: o cache downstream privado é permitido.<br />-   public: o cache downstream privado e compartilhado é permitido.|Não|nenhum|  
|must-revalidate|Quando o cache downstream está habilitado, este atributo ativa ou desativa a diretiva de controle de cache `must-revalidate` em respostas do gateway.|Não|verdadeiro|  
|vary-by-developer|Definido como `true` para respostas em cache por chave de desenvolvedor.|Não|false|  
|vary-by-developer-groups|Definido como `true` para respostas em cache por função de usuário.|Não|false|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** entrada  
  
-   **Escopos de política:** API, operação, produto  
  
##  <a name="StoreToCache"></a> Armazenar em cache  
 A política `cache-store` armazena as respostas em cache de acordo com a configuração de cache especificada. Esta política deve ser aplicada em casos nos quais o conteúdo da resposta permanece estático por um período de tempo. O cache das respostas reduz os requisitos de largura de banda e processamento exigidos do servidor Web de back-end e reduz a latência percebida pelos consumidores da API.  
  
> [!NOTE]
>  Esta política deve ter uma política [Obter do cache](api-management-caching-policies.md#GetFromCache) correspondente.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<cache-store duration="seconds" />  
```  
  
### <a name="examples"></a>Exemplos  
  
#### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
          <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">  
                <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->  
        </cache-lookup>  
    </inbound>  
    <outbound>  
        <base />  
            <cache-store duration="3600" />  
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Exemplo usando expressões de política  
 Este exemplo mostra como configurar a duração do cache de resposta do Gerenciamento de API que corresponde ao cache de resposta do serviço de back-end, conforme especificado pela diretiva `Cache-Control` do serviço de backup. Para ver uma demonstração da configuração e do uso dessa política, veja [Abordagem da Nuvem Episódio 177: Mais Recursos de Gerenciamento de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avance para 25:25.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 Para saber mais, veja [Expressões de política](api-management-policy-expressions.md) e [Variável de contexto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|cache-store|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|duration|Vida útil das entradas armazenadas em cache, especificada em segundos.|Sim|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** saída  
  
-   **Escopos de política:** API, operação, produto  
  
##  <a name="GetFromCacheByKey"></a> Obter valor do cache  
 Use a política `cache-lookup-value` para executar a consulta em cache por chave e retornar um valor armazenado em cache. A chave pode ter um valor de cadeia de caracteres arbitrário e geralmente é fornecida usando uma expressão de política.  
  
> [!NOTE]
>  É necessário ter uma política correspondente de [Armazenar valor em cache](#StoreToCacheByKey).  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<cache-lookup-value key="cache key value"   
    default-value="value to use if cache lookup resulted in a miss"   
    variable-name="name of a variable looked up value is assigned to" />  
```  
  
### <a name="example"></a>Exemplo  
 Para saber mais e obter exemplos dessa política, veja [Cache personalizado no Gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-lookup-value  
    key="@("userprofile-" + context.Variables["enduserid"])"    
    variable-name="userprofile" />  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|cache-lookup-value|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|default-value|Um valor que será atribuído à variável se a pesquisa de chave em cache resultou em um erro. Se esse atributo não for especificado, `null` é atribuído.|Não|`null`|  
|chave|Valor de chave de cache a usar na pesquisa.|Sim|N/D|  
|variable-name|Nome da [variável de contexto](api-management-policy-expressions.md#ContextVariables) a atribuir para o valor pesquisado, se a pesquisa for bem-sucedida. Se a pesquisa resulta em um erro, a variável será atribuída o valor do atributo `default-value` ou `null`, se o atributo `default-value` for omitido.|Sim|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos de política:** global, API, operação, produto  
  
##  <a name="StoreToCacheByKey"></a> Armazenar valor em cache  
 `cache-store-value` armazena em cache por chave. A chave pode ter um valor de cadeia de caracteres arbitrário e geralmente é fornecida usando uma expressão de política.  
  
> [!NOTE]
>  Esta política deve ter uma política [Obter valor do cache](#GetFromCacheByKey) correspondente.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<cache-store-value key="cache key value" value="value to cache" duration="seconds" />  
```  
  
### <a name="example"></a>Exemplo  
 Para saber mais e obter exemplos dessa política, veja [Cache personalizado no Gerenciamento de API do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-store-value  
    key="@("userprofile-" + context.Variables["enduserid"])"  
    value="@((string)context.Variables["userprofile"])" duration="100000" />  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|cache-store-value|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|duration|Valor será armazenado em cache para o valor de duração fornecido, especificado em segundos.|Sim|N/D|  
|chave|A chave em cache em que o valor será armazenado.|Sim|N/D|  
|valor|O valor a ser armazenado em cache.|Sim|N/D|  
  
### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos de política:** global, API, operação, produto  
  
###  <a name="RemoveCacheByKey"></a> Remover valor do cache  
 `cache-remove-value` exclui um item em cache identificado por sua chave. A chave pode ter um valor de cadeia de caracteres arbitrário e geralmente é fornecida usando uma expressão de política.  
  
#### <a name="policy-statement"></a>Declaração de política  
  
```xml  
  
<cache-remove-value key="cache key value"/>  
  
```  
  
#### <a name="example"></a>Exemplo  
  
```xml  
  
<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>  
  
```  
  
#### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Obrigatório|  
|----------|-----------------|--------------|  
|cache-remove-value|Elemento raiz.|Sim|  
  
#### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Obrigatório|Padrão|  
|----------|-----------------|--------------|-------------|  
|chave|A chave do valor anteriormente armazenado em cache a ser removido do cache.|Sim|N/D|  
  
#### <a name="usage"></a>Uso  
 Essa política pode ser usada nas [seções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e nos [escopos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) da política a seguir.  
  
-   **Seções de política:** entrada, saída, back-end, em caso de erro  
  
-   **Escopos de política:** global, API, operação, produto  
  

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como trabalhar com políticas, veja [Políticas em Gerenciamento de API](api-management-howto-policies.md).  