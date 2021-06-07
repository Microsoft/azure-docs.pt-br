---
title: Ações do mecanismo de regras do Azure Front Door
description: Este artigo fornece uma lista das várias ações que você pode executar com o mecanismo de regras do Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 7b5358ec6bf0f41c860501c70ad175d7cf4bfe97
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062838"
---
# <a name="azure-front-door-rules-engine-actions"></a>Ações do mecanismo de regras do Azure Front Door

No [Mecanismo de Regras do AFD](front-door-rules-engine.md), uma regra consiste em zero ou mais ações e condições de correspondência. Este artigo fornece descrições detalhadas das ações que você pode usar no mecanismo de regras do AFD.

Uma ação define o comportamento que é aplicado ao tipo de solicitação que uma condição de correspondência ou conjunto de condições de correspondência identifica. No Mecanismo de Regras do AFD, uma regra pode conter até cinco ações. Somente uma delas poderá ser uma ação de substituição de configuração de rota (encaminhar ou redirecionar).

As ações a seguir estão disponíveis para uso no mecanismo de regras do Azure Front Door.  

## <a name="modify-request-header"></a>Modificar o cabeçalho de solicitação

Use esta ação para modificar os cabeçalhos que estão presentes nas solicitações enviadas para sua origem.

### <a name="required-fields"></a>Campos obrigatórios

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando essa opção for selecionada e houver uma correspondência de regra, o cabeçalho especificado em **Nome do cabeçalho** será adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor será anexado ao valor existente. | String
Overwrite | Quando essa opção for selecionada e houver uma correspondência de regra, o cabeçalho especificado em **Nome do cabeçalho** será adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substituirá o valor existente. | String
Excluir | Quando essa opção for selecionada com regras de correspondência e o cabeçalho especificado na regra estiver presente, o cabeçalho será excluído da solicitação. | String

## <a name="modify-response-header"></a>Modificar o cabeçalho de resposta

Use essa ação para modificar os cabeçalhos que estão presentes nas respostas retornadas aos clientes.

### <a name="required-fields"></a>Campos obrigatórios

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando essa opção for selecionada e houver uma correspondência de regra, o cabeçalho especificado em **Nome do cabeçalho** será adicionado à resposta usando o **Valor** especificado. Se o cabeçalho já estiver presente, **Valor** será anexado ao valor existente. | String
Overwrite | Quando essa opção é selecionada e a regra corresponde, o cabeçalho especificado em **Nome do cabeçalho** é adicionado à resposta usando o **Valor** especificado. Se o cabeçalho já estiver presente, **Valor** substituirá o valor existente. | String
Excluir | Quando essa opção for selecionada e houver correspondência entre a regra e o cabeçalho especificado, o cabeçalho será excluído da resposta. | String

## <a name="route-configuration-overrides"></a>Substituições de configuração de rota 

### <a name="route-type-redirect"></a>Tipo de Rota: Redirecionar

Use esta ação para redirecionar clientes para uma nova URL. 

#### <a name="required-fields"></a>Campos obrigatórios

Campo | Descrição 
------|------------
Tipo de redirecionamento | Selecione o tipo de resposta para retornar ao solicitante: Encontrado (302), Movido (301), Redirecionamento temporário (307) e Redirecionamento permanente (308).
Protocolo de redirecionamento | Solicitação de correspondência, HTTP, HTTPS.
Host de destino | Selecione o nome do host para o qual você deseja que a solicitação seja redirecionada. Deixe em branco para preservar o host de entrada.
Caminho de destino | Defina o caminho a ser usado no redirecionamento. Deixe em branco para preservar o caminho de entrada.  
Cadeia de consulta | Defina a cadeia de consulta usada no redirecionamento. Deixe em branco para preservar a cadeia de consulta de entrada. 
Fragmento de destino | Defina o fragmento a ser usado no redirecionamento. Deixe em branco para preservar o fragmento de entrada. 


### <a name="route-type-forward"></a>Tipo de Rota: Avançar

Use esta ação para encaminhar clientes para uma nova URL. Essa ação também contém subações para regravações de URL e cache. 

Campo | Descrição 
------|------------
Pool de back-end | Selecione um pool de back-end para substituir as solicitações e atendê-las. Isso também mostrará todos os pools de back-end pré-configurados atualmente em seu perfil do Front Door. 
Protocolo de encaminhamento | Solicitação de correspondência, HTTP, HTTPS.
Reconfiguração de URL | Use essa ação para reescrever o caminho de uma solicitação que é roteada para sua origem. Caso esteja habilitada, confira a seguir quais são os campos adicionais necessários
Cache | Habilitado, Desabilitado. Confira a seguir quais são os campos adicionais necessários, caso esse campo esteja habilitado. 

#### <a name="url-rewrite"></a>Reconfiguração de URL

Use essa configuração para configurar um **Caminho de Encaminhamento Personalizado** opcional para usar ao construir a solicitação para encaminhar para o back-end.

Campo | Descrição 
------|------------
Caminho de encaminhamento personalizado | Defina o caminho para o qual encaminhar as solicitações. 

#### <a name="caching"></a>Cache

Use estas configurações para controlar como os arquivos serão armazenados em cache para obter solicitações que contêm cadeias de caracteres de consulta. Além de saber se o conteúdo deve ser armazenado em cache com base em todos os parâmetros ou nos parâmetros selecionados. Será possível usar configurações adicionais para substituir o valor de TTL (vida útil) para controlar por quanto tempo o conteúdo permanecerá no cache. Para forçar o armazenamento em cache como uma ação, defina o campo de cache como "Habilitado". Ao forçar o armazenamento em cache, as seguintes opções aparecerão: 

Comportamento do cache |  Descrição              
---------------|----------------
Ignorar as cadeias de caracteres de consulta | Depois que o ativo for armazenado em cache, todas as solicitações subsequentes ignorarão as cadeias de caracteres de consulta até que o ativo em cache expire.
Armazenar em cache todas as URLs exclusivas | Cada solicitação com um URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com um cache próprio.
Ignorar as cadeias de caracteres de consulta especificadas | As cadeias de consulta da URL de solicitação listadas na configuração "parâmetros de consulta" são ignoradas para cache.
Incluir as cadeias de caracteres de consulta especificadas | As cadeias de consulta da URL de solicitação listadas na configuração "parâmetros de consulta" são usadas para cache.

Campos adicionais |  Descrição 
------------------|---------------
Compactação dinâmica | O Front Door pode compactar dinamicamente conteúdo na borda, resultando em uma resposta menor e mais rápida.
Parâmetros de consulta | Uma lista separada por vírgula de parâmetros permitidos (ou não permitidos) a serem usados como base para o armazenamento em cache.
Duração do cache | Duração do término do cache em dias, horas, minutos, segundos. Todos os valores devem ser Int. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como configurar seu primeiro [Mecanismo de Regras](front-door-tutorial-rules-engine.md). 
- Saiba mais sobre as [condições de correspondência do Mecanismo de regras](front-door-rules-engine-match-conditions.md)
- Saiba mais sobre o [Mecanismo de regras do Azure Front Door](front-door-rules-engine.md)
