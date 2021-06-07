---
title: Ações no mecanismo de regras padrão para a CDN do Azure | Microsoft Docs
description: Documentação de referência para ações no mecanismo de regras padrão para a rede de distribuição de conteúdo do Azure (CDN do Azure).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 051737a9f5e0d4092cda26a3f7ce3df1d7f535ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87760117"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Ações no mecanismo de regras padrão para a CDN do Azure

No [mecanismo de regras padrão](cdn-standard-rules-engine.md) para a rede de distribuição de conteúdo do Azure (CDN do Azure), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das ações que você pode usar no mecanismo de regras padrão para a CDN do Azure.

A segunda parte de uma regra é uma ação. Uma ação define o comportamento que é aplicado ao tipo de solicitação que uma condição de correspondência ou conjunto de condições de correspondência identifica.

## <a name="actions"></a>Ações

As seguintes ações estão disponíveis para uso no mecanismo de regras padrão para a CDN do Azure. 

### <a name="cache-expiration"></a>Expiração do cache

Use essa ação para substituir o valor de vida útil (TTL) do ponto de extremidade para solicitações que as regras correspondem às condições especificar.

#### <a name="required-fields"></a>Campos obrigatórios

Comportamento do cache |  Descrição              
---------------|----------------
Ignorar cache | Quando essa opção é selecionada e a regra corresponde, o conteúdo não é armazenado em cache.
Substituições | Quando essa opção é selecionada e a regra corresponde, o valor de TTL retornado de sua origem é substituído pelo valor especificado na ação. Esse comportamento só será aplicado se a resposta for armazenável em cache. Para o cabeçalho de resposta de controle de cache com valores "no-cache", "Private", "no-Store", a ação não será aplicável.
Definir se ausente | Quando essa opção for selecionada e a regra corresponder, se nenhum valor de TTL for retornado de sua origem, a regra definirá o TTL para o valor especificado na ação. Esse comportamento só será aplicado se a resposta for armazenável em cache. Para o cabeçalho de resposta de controle de cache com valores "no-cache", "Private", "no-Store", a ação não será aplicável.

#### <a name="additional-fields"></a>Campos adicionais

Dias | Horas | minutos | Segundos
-----|-------|---------|--------
int | int | int | int 

### <a name="cache-key-query-string"></a>Cadeia de consulta de chave de cache

Use esta ação para modificar a chave de cache com base em cadeias de caracteres de consulta.

#### <a name="required-fields"></a>Campos obrigatórios

Comportamento | Descrição
---------|------------
Incluir | Quando essa opção é selecionada e a regra corresponde, as cadeias de caracteres de consulta especificadas nos parâmetros são incluídas quando a chave de cache é gerada. 
Armazenar em cache todas as URLs exclusivas | Quando essa opção é selecionada e a regra corresponde, cada URL exclusiva tem sua própria chave de cache. 
Excluir | Quando essa opção é selecionada e a regra corresponde, as cadeias de caracteres de consulta especificadas nos parâmetros são excluídas quando a chave de cache é gerada.
Ignorar as cadeias de consulta | Quando essa opção é selecionada e a regra corresponde, as cadeias de caracteres de consulta não são consideradas quando a chave de cache é gerada. 

### <a name="modify-request-header"></a>Modificar o cabeçalho de solicitação

Use esta ação para modificar os cabeçalhos que estão presentes nas solicitações enviadas para sua origem.

#### <a name="required-fields"></a>Campos obrigatórios

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando essa opção é selecionada e a regra corresponde, o cabeçalho especificado em **Nome do cabeçalho** é adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor será anexado ao valor existente. | String
Overwrite | Quando essa opção é selecionada e a regra corresponde, o cabeçalho especificado em **Nome do cabeçalho** é adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substituirá o valor existente. | String
Excluir | Quando essa opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é excluído da solicitação. | String

### <a name="modify-response-header"></a>Modificar o cabeçalho de resposta

Use essa ação para modificar os cabeçalhos que estão presentes nas respostas retornadas aos clientes.

#### <a name="required-fields"></a>Campos obrigatórios

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando essa opção é selecionada e a regra corresponde, o cabeçalho especificado em **Nome do cabeçalho** é adicionado à resposta usando o **Valor** especificado. Se o cabeçalho já estiver presente, **Valor** será anexado ao valor existente. | String
Overwrite | Quando essa opção é selecionada e a regra corresponde, o cabeçalho especificado em **Nome do cabeçalho** é adicionado à resposta usando o **Valor** especificado. Se o cabeçalho já estiver presente, **Valor** substituirá o valor existente. | String
Excluir | Quando essa opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é excluído da resposta. | String

### <a name="url-redirect"></a>Redirecionamento de URL

Use esta ação para redirecionar clientes para uma nova URL. 

#### <a name="required-fields"></a>Campos obrigatórios

Campo | Descrição 
------|------------
Type | Selecione o tipo de resposta para retornar ao solicitante: Encontrado (302), Movido (301), Redirecionamento temporário (307) e Redirecionamento permanente (308).
Protocolo | Solicitação de correspondência, HTTP, HTTPS.
Nome do host | Selecione o nome do host para o qual você deseja que a solicitação seja redirecionada. Deixe em branco para preservar o host de entrada.
Caminho | Defina o caminho a ser usado no redirecionamento. Deixe em branco para preservar o caminho de entrada.  
Cadeia de consulta | Defina a cadeia de consulta usada no redirecionamento. Deixe em branco para preservar a cadeia de consulta de entrada. 
Fragmento | Defina o fragmento a ser usado no redirecionamento. Deixe em branco para preservar o fragmento de entrada. 

É altamente recomendável que você use uma URL absoluta. O uso de uma URL relativa pode redirecionar as URLs da CDN do Azure para um caminho inválido. 

### <a name="url-rewrite"></a>Reconfiguração de URL

Use essa ação para reescrever o caminho de uma solicitação que é roteada para sua origem.

#### <a name="required-fields"></a>Campos obrigatórios

Campo | Descrição 
------|------------
Padrão de origem | Defina o padrão de origem no caminho da URL a ser substituído. Atualmente, o padrão de origem usa uma correspondência baseada em prefixo. Para corresponder a todos os caminhos de URL, use uma barra ( **/** ) como o valor de padrão de origem.
Destino | Defina o caminho de destino a ser usado na regravação. O caminho de destino substitui o padrão de origem.
Preservar caminho sem correspondência | Se definido como **Sim**, o caminho restante após o padrão de origem será acrescentado ao novo caminho de destino. 

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras Standard](cdn-standard-rules-engine-reference.md)
- [Condições de correspondência do mecanismo de regras Standard](cdn-standard-rules-engine-match-conditions.md)
- [Impor HTTPS usando o mecanismo de regras Standard](cdn-standard-rules-engine.md)
