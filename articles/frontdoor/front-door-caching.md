---
title: Cache de porta frontal do Azure | Microsoft Docs
description: Este artigo ajuda você a entender o comportamento da porta frontal com as regras de roteamento que habilitaram o Caching.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: d001a7a24d44c46a19bde08051e21d3ae3c5acb8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99538044"
---
# <a name="caching-with-azure-front-door"></a>Caching com a porta frontal do Azure
O documento a seguir especifica comportamentos para a porta frontal com regras de roteamento que habilitaram o Caching. A porta frontal é uma CDN (rede de distribuição de conteúdo) moderna com aceleração de site dinâmica e balanceamento de carga; ela também dá suporte a comportamentos de cache, assim como qualquer outra CDN.

## <a name="delivery-of-large-files"></a>Entrega de arquivos grandes
A porta frontal do Azure fornece arquivos grandes sem limite de tamanho de arquivo. O Front Door usa uma técnica chamada agrupamento de objeto. Quando um arquivo grande é solicitado, o Front Door recupera partes menores do arquivo a partir do back-end. Depois de receber uma solicitação de arquivo completa ou de intervalo de bytes, o ambiente de porta frontal solicita o arquivo do back-end em partes de 8 MB.

Depois que a parte chega ao ambiente de porta frontal, ela é armazenada em cache e imediatamente fornecida ao usuário. Em seguida, o Front Door efetua a pré-busca da próxima parte em paralelo. Essa pré-busca garante que o conteúdo permaneça uma parte à frente do usuário, o que reduz a latência. Esse processo continua até que todo o arquivo seja baixado (se solicitado) ou o cliente feche a conexão.

Para obter mais informações sobre a solicitação de intervalo de bytes, leia [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A porta frontal armazena em cache todas as partes à medida que elas são recebidas para que o arquivo inteiro não precise ser armazenado em cache no cache da porta frontal. Solicitações que podem ser feitas para os intervalos de arquivo ou de bytes são servidas do cache. Se as partes não estiverem todas armazenadas em cache, a pré-busca será usada para solicitar partes do back-end. Essa otimização depende da capacidade do back-end de dar suporte a solicitações de intervalo de bytes. Se o back-end não der suporte a solicitações de intervalo de bytes, essa otimização não será eficaz.

## <a name="file-compression"></a>Compactação de arquivo
A porta frontal pode compactar dinamicamente o conteúdo na borda, resultando em um tempo de resposta menor e mais rápido para seus clientes. Para que um arquivo seja qualificado para compactação, o cache deve ser habilitado e o arquivo deve ser de um tipo MIME para ser qualificado para compactação. Atualmente, a porta frontal não permite que essa lista seja alterada. A lista atual é:
- "application/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- “application/json”
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

Além disso, o arquivo também deve ter entre 1 KB e 8 MB de tamanho, inclusive.

Esses perfis dão suporte às seguintes codificações de compactação:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Se uma solicitação com suporte para gzip e compactação Brotli, a compactação Brotli terá precedência.</br>
Quando uma solicitação de um ativo especifica a compactação e a solicitação resulta em um erro de cache, a porta frontal faz a compactação do ativo diretamente no servidor POP. Depois disso, o arquivo compactado será servido do cache. O item resultante é retornado com uma codificação de transferência: em partes.

## <a name="query-string-behavior"></a>Comportamento da cadeia de caracteres de consulta
Com o Front Door, é possível controlar como os arquivos são armazenados em cache para uma solicitação da Web que contenha uma cadeia de caracteres de consulta. Em uma solicitação da Web com uma cadeia de caracteres de consulta, a cadeia de caracteres de consulta é aquela parte da solicitação que ocorre após um ponto de interrogação (?). Uma cadeia de caracteres de consulta pode conter um ou mais pares de chave-valor, no qual o nome do campo e seu valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se houver mais de um par chave-valor em uma cadeia de caracteres de consulta de uma solicitação, sua ordem não importa.
- **Ignorar cadeias de caracteres de consulta**: nesse modo, a porta frontal passa as cadeias de caracteres de consulta do solicitante para o back-end na primeira solicitação e armazena o ativo em cache. Todas as solicitações inválidas para o ativo que são atendidas do ambiente de porta de front-end ignoram as cadeias de caracteres de consulta até que o ativo em cache expire.

- **Armazenar em cada cache URL exclusiva**: nesse modo, cada solicitação com um URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta do back-end para uma solicitação de `www.example.ashx?q=test1` é armazenada em cache no ambiente de porta frontal e retornada para caches inconsistentes com a mesma cadeia de caracteres de consulta. Uma solicitação `www.example.ashx?q=test2` é armazenada em cache como um ativo separado com sua própria configuração de vida útil.

## <a name="cache-purge"></a>Limpeza do cache

A porta frontal armazena ativos em cache até o tempo de vida (TTL) do ativo expirar. Sempre que um cliente solicita um ativo com TTL expirado, o ambiente de porta de front-end recupera uma nova cópia atualizada do ativo para atender à solicitação e, em seguida, armazena o cache atualizado.

A prática recomendada para garantir que os usuários sempre obtenham a cópia mais recente de seus ativos é verter os ativos para cada atualização e publicá-los como novas URLs. O Front Door recuperará imediatamente os novos ativos para as próximas solicitações do cliente. Às vezes, convém limpar o conteúdo em cache de todos os nós de borda e forçá-los a recuperar novos ativos atualizados. O motivo pode ser devido a atualizações em seu aplicativo Web ou para atualizar ativos rapidamente que contenham informações incorretas.

Selecione os ativos que você deseja limpar dos nós de borda. Para limpar todos os ativos, selecione **limpar tudo**. Caso contrário, em **caminho**, insira o caminho de cada ativo que você deseja limpar.

Esses formatos têm suporte nas listas de caminhos a serem limpos:

- **Limpeza de caminho único**: Limpe ativos individuais especificando o caminho completo do ativo (sem o protocolo e o domínio), com a extensão de arquivo, por exemplo,/Pictures/strasbourg.png;
- **Limpeza de caractere curinga**: o asterisco (\*) pode ser usado como um caractere curinga. Limpe todas as pastas, subpastas e arquivos em um ponto de extremidade com/ \* no caminho ou limpe todas as subpastas e arquivos em uma pasta específica, especificando a pasta seguida por/ \* , por exemplo,/Pictures/ \* .
- **Limpeza do domínio raiz**: limpe a raiz do ponto de extremidade com "/" no caminho.

> [!NOTE]
> A **limpeza de domínios curinga**: a especificação de caminhos armazenados em cache para limpeza, conforme discutido nesta seção, não se aplica a domínios curinga que estejam associados à porta frontal. No momento, não há suporte para a limpeza direta de domínios curinga. Você pode limpar caminhos de subdomínios específicos especificando o subdomínio específico e o caminho de limpeza. Por exemplo, se minha porta frontal tiver `*.contoso.com` , posso limpar os ativos do meu subdomínio `foo.contoso.com` digitando `foo.contoso.com/path/*` . Atualmente, a especificação de nomes de host no caminho de conteúdo de limpeza é imited a subdomínios de domínios curinga, se aplicável.
>

As limpezas de cache do Front Door diferenciam maiúsculas de minúsculas. Além disso, eles são independentes de cadeia de caracteres de consulta, o que significa que a limpeza de uma URL limpará todas as variações de cadeia de caracteres de consulta. 

## <a name="cache-expiration"></a>Expiração do cache
A ordem de cabeçalhos a seguir é usada para determinar por quanto tempo um item será armazenado em nosso cache:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: Max-age =\<seconds>
3. Expira \<http-date>

Cache-Control cabeçalhos de resposta que indicam que a resposta não será armazenada em cache, como Cache-Control: privado, Cache-Control: no-cache e Cache-Control: no-Store são respeitados.  Se nenhum Cache-Control estiver presente, o comportamento padrão é que a porta frontal armazenará em cache o recurso por X período de tempo em que X é separado aleatoriamente entre 1 e 3 dias.

## <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos de solicitação a seguir não serão encaminhados para um back-end ao usar o Caching.
- Content-Length
- Transfer-Encoding

## <a name="cache-duration"></a>Duração do cache

A duração do cache pode ser configurada no designer de porta frontal e no mecanismo de regras. A duração do cache definida no designer de porta frontal é a duração mínima do cache. Essa substituição não funcionará se o cabeçalho de controle de cache da origem tiver uma TTL maior do que o valor de substituição. 

A duração do cache definida por meio do mecanismo de regras é uma substituição de cache verdadeira, o que significa que ele usará o valor de substituição, independentemente do cabeçalho de resposta da origem.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
