---
title: Cabeçalhos HTTP Verizon X-EC-Debug para o Mecanismo de regras de CDN do Azure |Microsoft Docs
description: O cabeçalho da solicitação X-EC-Debug depuração de cache fornece informações adicionais sobre a política de cache que é aplicada para o ativo solicitado. Esses cabeçalhos são específicos para Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: 4ba42850ee28e2e212d9bc2b7b64be103218757c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736965"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Cabeçalhos HTTP Verizon X-EC-Debug para o Mecanismo de regras de CDN do Azure
O cabeçalho da solicitação de depuração de cache `X-EC-Debug` fornece informações adicionais sobre a política de cache que é aplicada para o ativo solicitado. Esses cabeçalhos são específicos para **Premium do Azure CDN dos produtos Verizon**.

## <a name="usage"></a>Uso
A resposta enviada de servidores POP para um usuário inclui o `X-EC-Debug` cabeçalho somente quando as seguintes condições forem atendidas:

- O [Recurso de Cabeçalhos de Resposta de Cache de Depuração](cdn-rules-engine-reference-features.md#debug-cache-response-headers) foi habilitado no mecanismo de regras para a solicitação especificada.
- A solicitação especificada define o conjunto de cabeçalhos de resposta do cache de depuração que serão incluídos na resposta.

## <a name="requesting-debug-cache-information"></a>Solicitação de informações do cache de depuração
Use as seguintes diretivas na solicitação especificada para definir as informações do cache de depuração que serão incluídas na resposta:

Cabeçalho da solicitação | DESCRIÇÃO |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Código de status do cache](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [Código de status do cache](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Cacheable](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [Cache-key](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [Estado do cache](#cache-state-response-header)

### <a name="syntax"></a>Sintaxe

Cabeçalhos de resposta de cache de depuração podem ser solicitados incluindo-se o seguinte cabeçalho e as diretivas especificadas na solicitação:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Exemplo de cabeçalhos X-EC-Debug

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informações de código de status do cache
O cabeçalho de resposta X-EC-Debug pode identificar um servidor e como ele manipulou a resposta por meio das diretivas a seguir:

Cabeçalho | DESCRIÇÃO
-------|------------
X-EC-Debug: x-ec-cache | Esse cabeçalho é informado sempre que o conteúdo é encaminhado por meio da CDN. Ele identifica o servidor POP que atendeu à solicitação.
X-EC-Debug: x-ec-cache-remote | Esse cabeçalho é relatado somente quando o conteúdo solicitado foi armazenado em cache em um servidor de proteção de origem ou de um servidor de gateway ADN.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O cabeçalho X-EC-Debug relata informações de código de status de cache no seguinte formato:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Os termos usados na sintaxe são definidos da seguinte maneira:
- StatusCode: Indica como o conteúdo solicitado foi tratado pela CDN, que é representada por um código de status do cache.
    
    O código de status TCP_DENIED pode ser relatado em vez de NENHUM quando uma solicitação não autorizada é negada devido a autenticação baseada em Token. No entanto, o código de status NONE continuará a ser usado ao exibir relatórios de Status do Cache ou dados brutos de log.

- Plataforma: Indica a plataforma na qual o conteúdo foi solicitado. Os códigos a seguir são válidos para este campo:

    Código  | Plataforma
    ------| --------
    ECAcc | HTTP grande
    ECS   | HTTP Pequeno
    ECD   | Rede de entrega de aplicativo (ADN)

- POP: Indica o [POP](cdn-pop-abbreviations.md) que tratou a solicitação. 

### <a name="sample-response-headers"></a>Exemplo de cabeçalhos de resposta

Os cabeçalhos de exemplo a seguir fornecem informações de código de status de cache para uma solicitação:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Cabeçalhos de resposta armazenáveis em cache
O `X-EC-Debug: x-ec-check-cacheable` cabeçalho de resposta que indica se o conteúdo solicitado pode ter sido armazenado em cache.

Esse cabeçalho de resposta não indica se o cache foi feito. Em vez disso, indica se a solicitação foi qualificada para o cache.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O `X-EC-Debug` cabeçalho de reposta relatando se uma solicitação pode ter sido armazenada em cache está no formato a seguir:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Os termos usados na sintaxe são definidos da seguinte maneira:

Value  | DESCRIÇÃO
-------| --------
SIM    | Indica que o conteúdo solicitado estava elegível para cache.
NÃO     | Indica que o conteúdo solicitado estava inelegível para cache. Esse status pode ser devido a um dos seguintes motivos: <br /> Configuração específica do cliente: Uma configuração específica à sua conta pode impedir que os servidores pop armazenem um ativo de cache. Por exemplo, mecanismo de regras pode impedir um ativo que está sendo armazenado em cache, permitindo que o recurso Ignorar Cache qualifique as solicitações.<br /> -Cabeçalhos de resposta de cache: Cabeçalhos de Cache-Control e Expires do ativo solicitado podem impedir que os servidores POP em cache.
DESCONHECIDO | Indica que os servidores não foram puderam avaliar se o recurso solicitado foi armazenável em cache. Normalmente, esse status ocorre quando a solicitação for negada devido a autenticação baseada em token.

### <a name="sample-response-header"></a>Exemplo de cabeçalho de resposta

O exemplo de cabeçalho de resposta a seguir indica se o conteúdo solicitado pode ter sido armazenado em cache:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cabeçalho de reposta Cache-Key
O `X-EC-Debug: x-ec-cache-key` cabeçalho de resposta indica o Cache-Key físico associado ao conteúdo solicitado. Um cache-key é o caminho relativo que identifica um ativo para fins de cache. Em outras palavras, os servidores verificarão se há uma versão em cache de um ativo de acordo com seu caminho, conforme definido pelo cache-key.

Este cache-key começa com uma barra invertida dupla (/ /) seguido pelo protocolo usado para solicitar o conteúdo (HTTP ou HTTPS). Esse protocolo é seguido pelo caminho relativo para o ativo solicitado, o que inicia com o ponto de acesso ao conteúdo (por exemplo, _/000001/_).

Por padrão, as plataformas HTTP são configuradas para usar *cache padrão*, o que significa que as cadeias de caracteres de consulta são ignoradas pelo mecanismo de armazenamento em cache. Esse tipo de configuração impede que o cache-key inclua dados de cadeia de caracteres de consulta.

Se uma cadeia de caracteres de consulta é registrada no cache-key, ele foi convertido em seu equivalente de hash e, em seguida, inserido entre o nome do ativo solicitado e sua extensão de arquivo (por exemplo, ativo&lt;o valor de hash&gt;. HTML).

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta relata informações de cache-key físicas no seguinte formato:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Exemplo de cabeçalho de resposta

O cabeçalho de resposta indica o cache-key físico associado ao conteúdo solicitado:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Cabeçalho de resposta de Idade
O `X-EC-Debug: x-ec-cache-state` cabeçalho de resposta indica o estado do cache do conteúdo solicitado no momento em que foi solicitado.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta relata informações de estado de cache no seguinte formato:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Os termos usados na sintaxe são definidos da seguinte maneira:

- MASeconds: Indica a idade máxima (em segundos), conforme definido por cabeçalhos de controle de Cache do conteúdo solicitado.

- MATimePeriod: Converte o valor de idade máxima (ou seja, MASeconds) aproximado equivalente a uma unidade maior (por exemplo, em dias). 

- UnixTime: Indica o carimbo de hora do cache do conteúdo solicitado no horário do Unix (também conhecido como hora POSIX ou época do Unix). O carimbo de hora de cache indica a data/hora inicial do qual será calculado um TTL do ativo. 

    Se o servidor de origem não utiliza um servidor de cache HTTP de terceiros ou se o servidor não retorna o cabeçalho de resposta de Idade, em seguida, o carimbo de data/hora sempre será a data/hora quando o ativo foi recuperado ou revalidado. Caso contrário, os servidores POP usará o campo de idade para calcular a TTL do ativo da seguinte maneira: Recuperação/RevalidateDateTime - idade.

- DDD, dd MMM AAAA HH: mm ss GMT: Indica o carimbo de hora do cache do conteúdo solicitado. Para mais informações, consulte o termo UnixTime acima.

- CASeconds: Indica o número de segundos decorridos desde o carimbo de hora do cache.

- RTSeconds: Indica o número de segundos restantes para o qual o conteúdo em cache será considerado atualizado. Esse valor é calculado da seguinte maneira: RTSeconds = max-age - idade do cache.

- RTTimePeriod: Converte o valor de TTL remanescente (ou seja, Maseconds) aproximado equivalente a uma unidade maior (por exemplo, em dias).

- ExpiresSeconds: Indica o número de segundos restantes antes que a data/hora especificada no `Expires` cabeçalho de resposta. Se o `Expires` cabeçalho de resposta não foi incluído na resposta, em seguida, o valor do termo é *nenhum*.

### <a name="sample-response-header"></a>Exemplo de cabeçalho de resposta

O cabeçalho de resposta a seguir indica o estado do cache do conteúdo solicitado no momento em que foi solicitado:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

