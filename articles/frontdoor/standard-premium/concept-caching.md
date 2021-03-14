---
title: 'Porta frontal do Azure: Caching'
description: Este artigo ajuda você a entender o comportamento do Azure front door Standard/Premium com as regras de roteamento que habilitaram o Caching.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 73b2e8e59774e12ddb9aa684382510d1f2c151b8
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098529"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Caching com o Azure front door Standard/Premium (visualização)

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

Neste artigo, você aprenderá como as rotas Standard/Premium (versão prévia) e o conjunto de regras da porta de front-end se comporta quando o Caching está habilitado. A porta frontal do Azure é uma CDN (rede de distribuição de conteúdo) moderna com aceleração de site dinâmica e balanceamento de carga.

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="delivery-of-large-files"></a>Entrega de arquivos grandes

O padrão de porta frontal/Premium (visualização) fornece arquivos grandes sem limite de tamanho de arquivo. O Front Door usa uma técnica chamada agrupamento de objeto. Quando um arquivo grande é solicitado, a porta frontal recupera partes menores do arquivo da origem. Depois de receber uma solicitação de arquivo completa ou de intervalo de bytes, o ambiente de porta frontal solicita o arquivo das origens em partes de 8 MB.

Depois que a parte chega ao ambiente de porta frontal, ela é armazenada em cache e imediatamente fornecida ao usuário. Em seguida, o Front Door efetua a pré-busca da próxima parte em paralelo. Essa pré-busca garante que o conteúdo permaneça uma parte à frente do usuário, o que reduz a latência. Esse processo continua até que todo o arquivo seja baixado (se solicitado) ou o cliente feche a conexão.

Para obter mais informações sobre a solicitação de intervalo de bytes, leia [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A porta frontal armazena em cache todas as partes à medida que elas são recebidas para que o arquivo inteiro não precise ser armazenado em cache no cache da porta frontal. Solicitações que podem ser feitas para os intervalos de arquivo ou de bytes são servidas do cache. Se as partes não estiverem todas armazenadas em cache, a pré-busca será usada para solicitar partes do back-end. Essa otimização depende da capacidade da origem de dar suporte a solicitações de intervalo de bytes. Se a origem não oferecer suporte a solicitações de intervalo de bytes, essa otimização não será eficaz.

## <a name="file-compression"></a>Compactação de arquivo

Consulte melhorar o desempenho compactando arquivos na porta frontal do Azure.

## <a name="query-string-behavior"></a>Comportamento da cadeia de caracteres de consulta

Com o Front Door, é possível controlar como os arquivos são armazenados em cache para uma solicitação da Web que contenha uma cadeia de caracteres de consulta. Em uma solicitação da Web com uma cadeia de caracteres de consulta, a cadeia de caracteres de consulta é aquela parte da solicitação que ocorre após um ponto de interrogação (?). Uma cadeia de caracteres de consulta pode conter um ou mais pares de chave-valor, no qual o nome do campo e seu valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se houver mais de um par chave-valor em uma cadeia de caracteres de consulta de uma solicitação, sua ordem não importa.

* **Ignorar cadeias de caracteres de consulta**: nesse modo, a porta frontal passa as cadeias de caracteres de consulta do solicitante para a origem na primeira solicitação e armazena o ativo em cache. Todas as solicitações inválidas para o ativo que são atendidas do ambiente de porta de front-end ignoram as cadeias de caracteres de consulta até que o ativo em cache expire.

* **Armazenar em cada cache URL exclusiva**: nesse modo, cada solicitação com um URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta da origem para uma solicitação de `www.example.ashx?q=test1` é armazenada em cache no ambiente da porta de front-end e retornada para caches inconsistentes com a mesma cadeia de caracteres de consulta. Uma solicitação `www.example.ashx?q=test2` é armazenada em cache como um ativo separado com sua própria configuração de vida útil.
* Você também pode usar o conjunto de regras para especificar o comportamento da **cadeia de caracteres de consulta de chave de cache** , para incluir ou excluir parâmetros especificados quando a chave de cache é gerada. Por exemplo, a chave de cache padrão é:/foo/Image/asset.html e a solicitação de exemplo é `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` . Há uma regra de conjunto de regras para excluir a cadeia de caracteres de consulta ' userid '. Em seguida, a cadeia de caracteres de consulta cache-Key seria `/foo/image/asset.html?language=EN&sessionid=200` .

## <a name="cache-purge"></a>Limpeza do cache

Consulte limpeza de cache.

## <a name="cache-expiration"></a>Expiração do cache
A ordem de cabeçalhos a seguir é usada para determinar por quanto tempo um item será armazenado em nosso cache:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: Max-age =\<seconds>
3. Expira \<http-date>

Cache-Control cabeçalhos de resposta que indicam que a resposta não será armazenada em cache, como Cache-Control: privado, Cache-Control: no-cache e Cache-Control: no-Store são respeitados.  Se nenhum Cache-Control estiver presente, o comportamento padrão é que a porta frontal armazenará em cache o recurso por um período de tempo X. Em que X é escolhido aleatoriamente entre 1 e 3 dias.

## <a name="request-headers"></a>Cabeçalhos de solicitação

Os cabeçalhos de solicitação a seguir não serão encaminhados para uma origem ao usar o Caching.
* Content-Length
* Transfer-Encoding

## <a name="cache-duration"></a>Duração do cache

A duração do cache pode ser configurada no conjunto de regras. A duração do cache definida por meio de regras definidas é uma substituição de cache verdadeira. Isso significa que ele usará o valor de substituição, independentemente do cabeçalho de resposta da origem.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [condições de correspondência do conjunto de regras](concept-rule-set-match-conditions.md)
* Saiba mais sobre as [ações do conjunto de regras](concept-rule-set-actions.md)