---
title: Configurar ações de conjunto de regras padrão/Premium da porta do Azure
description: Este artigo fornece uma lista das várias ações que você pode fazer com o conjunto de regras de porta frontal do Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c9995df0f292c5e528156a3280df5484db017fca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098434"
---
# <a name="azure-front-door-standardpremium-rule-set-actions"></a>Ações de conjunto de regras padrão/Premium da porta do Azure

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

Um [conjunto de regras](concept-rule-set.md) de porta frontal do Azure consiste em regras com uma combinação de ações e condições de correspondência. Este artigo fornece uma descrição detalhada das ações que podem ser usadas em um conjunto de regras. A ação define o comportamento que é aplicado a um tipo de solicitação que identifica as condições de correspondência. Em um conjunto de regras de porta frontal do Azure, uma regra pode conter até cinco ações. A variável de servidor tem suporte em todas as ações.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As ações a seguir estão disponíveis para uso no conjunto de regras de porta frontal do Azure.  

## <a name="cache-expiration"></a>Expiração do cache

Use essa ação para substituir o valor de vida útil (TTL) do ponto de extremidade para solicitações que as regras correspondem às condições especificar.

### <a name="required-fields"></a>Campos obrigatórios

A descrição a seguir se aplica ao selecionar esses comportamentos de cache e a regra corresponde a:

Comportamento do cache |  Descrição              
---------------|----------------
Ignorar cache | O conteúdo não está armazenado em cache.
Substituições | O valor de TTL retornado de sua origem é substituído pelo valor especificado na ação. Esse comportamento só será aplicado se a resposta for armazenável em cache. Para o cabeçalho de resposta de controle de cache com valores "no-cache", "Private", "no-Store", a ação não será aplicável.
Definir se ausente | Se nenhum valor de TTL for retornado de sua origem, a regra definirá o TTL para o valor especificado na ação. Esse comportamento só será aplicado se a resposta for armazenável em cache. Para o cabeçalho de resposta de controle de cache com valores "no-cache", "Private", "no-Store", a ação não será aplicável.

### <a name="additional-fields"></a>Campos adicionais

Dias | Horas | minutos | Segundos
-----|-------|---------|--------
int | int | int | int 

## <a name="cache-key-query-string"></a>Cadeia de consulta de chave de cache

Use esta ação para modificar a chave de cache com base em cadeias de caracteres de consulta.

### <a name="required-fields"></a>Campos obrigatórios

A descrição a seguir se aplica ao selecionar esses comportamentos e a regra corresponde a:

Comportamento | Descrição
---------|------------
Incluir | As cadeias de caracteres de consulta especificadas nos parâmetros são incluídas quando a chave de cache é gerada. 
Armazenar em cache todas as URLs exclusivas | Cada URL exclusiva tem sua própria chave de cache. 
Excluir | As cadeias de caracteres de consulta especificadas nos parâmetros são excluídas quando a chave de cache é gerada.
Ignorar as cadeias de consulta | As cadeias de caracteres de consulta não são consideradas quando a chave de cache é gerada. 

## <a name="modify-request-header"></a>Modificar o cabeçalho de solicitação

Use esta ação para modificar os cabeçalhos que estão presentes nas solicitações enviadas para sua origem.

### <a name="required-fields"></a>Campos obrigatórios

A descrição a seguir se aplica ao selecionar essas ações e a regra corresponde a:

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | O cabeçalho especificado no **nome do cabeçalho** é adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor será anexado ao valor existente. | String
Overwrite | O cabeçalho especificado no **nome do cabeçalho** é adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substituirá o valor existente. | String
Excluir | Se o cabeçalho especificado na regra estiver presente, o cabeçalho será excluído da solicitação. | String

## <a name="modify-response-header"></a>Modificar o cabeçalho de resposta

Use essa ação para modificar os cabeçalhos que estão presentes nas respostas retornadas aos clientes.

### <a name="required-fields"></a>Campos obrigatórios

A descrição a seguir se aplica ao selecionar essas ações e a regra corresponde a:

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | O cabeçalho especificado no **nome do cabeçalho** é adicionado à resposta usando o **valor** especificado. Se o cabeçalho já estiver presente, **Valor** será anexado ao valor existente. | String
Overwrite | O cabeçalho especificado no **nome do cabeçalho** é adicionado à resposta usando o **valor** especificado. Se o cabeçalho já estiver presente, **Valor** substituirá o valor existente. | String
Excluir | Se o cabeçalho especificado na regra estiver presente, o cabeçalho será excluído da resposta. | String

## <a name="url-redirect"></a>Redirecionamento de URL

Use esta ação para redirecionar clientes para uma nova URL. 

### <a name="required-fields"></a>Campos obrigatórios

Campo | Descrição 
------|------------
Tipo de redirecionamento | Selecione o tipo de resposta para retornar ao solicitante: Encontrado (302), Movido (301), Redirecionamento temporário (307) e Redirecionamento permanente (308).
Protocolo de redirecionamento | Solicitação de correspondência, HTTP, HTTPS.
Host de destino | Selecione o nome do host para o qual você deseja que a solicitação seja redirecionada. Deixe em branco para preservar o host de entrada.
Caminho de destino | Defina o caminho a ser usado no redirecionamento. Deixe em branco para preservar o caminho de entrada.  
Cadeia de consulta | Defina a cadeia de consulta usada no redirecionamento. Deixe em branco para preservar a cadeia de consulta de entrada. 
Fragmento de destino | Defina o fragmento a ser usado no redirecionamento. Deixe em branco para preservar o fragmento de entrada. 

## <a name="url-rewrite"></a>Reconfiguração de URL

Use essa ação para reescrever o caminho de uma solicitação que é roteada para sua origem.

### <a name="required-fields"></a>Campos obrigatórios

Campo | Descrição 
------|------------
Padrão de origem | Defina o padrão de origem no caminho da URL a ser substituído. Atualmente, o padrão de origem usa uma correspondência baseada em prefixo. Para corresponder a todos os caminhos de URL, use uma barra ( **/** ) como o valor de padrão de origem.
Destino | Defina o caminho de destino a ser usado na regravação. O caminho de destino substitui o padrão de origem.
Preservar caminho sem correspondência | Se definido como **Sim**, o caminho restante após o padrão de origem será acrescentado ao novo caminho de destino. 

## <a name="server-variable"></a>Variável de servidor

### <a name="supported-variables"></a>Variáveis com suporte

| Nome da variável | Descrição                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | O endereço IP da conexão direta com a borda da porta frontal do Azure. Se o cliente usou um proxy HTTP ou um balanceador de carga para enviar a solicitação, o valor de SocketIp será o endereço IP do balanceador de carga ou proxy. |
| client_ip                  | O endereço IP do cliente que fez a solicitação original. Se houver um cabeçalho X-Forwardd-for na solicitação, o IP do cliente será escolhido do mesmo. |
| client_port                | A porta IP do cliente que fez a solicitação. |
| hostname                      | O nome do host na solicitação do cliente. |
| geo_country                     | Indica o país/região de origem do solicitante por meio de seu código de país/região. |
| http_method                | O método usado para fazer a solicitação de URL. Por exemplo, GET ou POST. |
| http_version               | O protocolo de solicitação. Geralmente, HTTP/1.0, HTTP/1.1 ou HTTP/2.0. |
| query_string               | A lista de pares de variáveis/valores que segue o "?" na URL solicitada. Exemplo: na solicitação *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* , QUERY_STRING valor será *ID = 123&título = Fabrikam* |
| request_scheme             | O esquema de solicitação: http ou HTTPS. |
| request_uri                | O URI de solicitação original completo (com argumentos). Exemplo: na solicitação *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* , REQUEST_URI valor será */article.aspx? id = 123&title = Fabrikam* |
| server_port                | A porta do servidor que aceitou uma solicitação. |
| ssl_protocol    | O protocolo de uma conexão TLS estabelecida. |
| url_path                   | Identifica o recurso específico no host que o cliente Web deseja acessar. Essa é a parte do URI de solicitação sem os argumentos. Exemplo: na solicitação *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* , uri_path valor será */article.aspx* |

### <a name="server-variable-format"></a>Formato de variável de servidor    

**Formato:** {variável: offset}, {variável: deslocamento: comprimento}, {variável}

### <a name="supported-server-variable-actions"></a>Ações de variável de servidor com suporte

* Cabeçalho da solicitação
* Cabeçalho de resposta
* Cadeia de consulta de chave de cache
* Reconfiguração de URL
* Redirecionamento de URL

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [conjunto de regras stanard/Premium do Azure front door](concept-rule-set.md).
* Saiba mais sobre [condições de correspondência de conjunto de regras](concept-rule-set-match-conditions.md).
