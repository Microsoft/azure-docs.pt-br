---
title: Referência do mecanismo de regras padrão para a CDN do Azure | Microsoft Docs
description: Documentação de referência para condições de correspondência e ações no mecanismo de regras padrão para a rede de distribuição de conteúdo do Azure (CDN do Azure).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 1a0f4456f38939632026645500dd48acbf7dbc88
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242201"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Referência do mecanismo de regras Standard para a CDN do Azure

No [mecanismo de regras padrão](cdn-standard-rules-engine.md) para a rede de distribuição de conteúdo do Azure (CDN do Azure), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência e recursos que estão disponíveis no mecanismo de regras padrão para a CDN do Azure.

O mecanismo de regras foi projetado para ser a autoridade final sobre como tipos específicos de solicitações são processados pela CDN do Azure padrão.

**Usos comuns para as regras** :

- Substituir ou definir uma política de cache personalizada.
- Solicitações de redirecionamento.
- Modificar cabeçalhos de solicitação e resposta HTTP.

## <a name="terminology"></a>Terminologia

Para definir uma regra no mecanismo de regras, defina [condições de correspondência](cdn-standard-rules-engine-match-conditions.md) e [ações](cdn-standard-rules-engine-actions.md):

 ![Estrutura de regras da CDN do Azure](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Cada regra pode ter até dez condições de correspondência e cinco ações. Cada ponto de extremidade da CDN do Azure pode ter até 25 regras. 

O incluído nesse limite é uma *regra global* padrão. A regra global não tem condições de correspondência; as ações definidas em uma regra global sempre são disparadas.

   > [!IMPORTANT]
   > A ordem na qual são listadas as várias regras afeta como elas são processadas. As ações especificadas em uma regra podem ser substituídas por uma regra seguinte.

## <a name="limits-and-pricing"></a>Limites e preços 

Cada ponto de extremidade da CDN do Azure pode ter até 25 regras. Cada regra pode ter até dez condições de correspondência e cinco ações. O preço do mecanismo de regras segue as dimensões abaixo: 
- Regras: $1 por regra por mês 
- Solicitações processadas: $0.60 por milhão de solicitações
- As primeiras 5 regras permanecerão livres

## <a name="syntax"></a>Syntax

A forma como os caracteres especiais são tratados em uma regra varia de acordo com a forma como as condições de correspondência e as ações diferentes manipulam valores de texto. Uma condição de correspondência ou ação pode interpretar o texto de uma das seguintes maneiras:

- [Valores literais](#literal-values)
- [Valores de caractere curinga](#wildcard-values)


### <a name="literal-values"></a>Valores literais

O texto que é interpretado como um valor literal trata todos os caracteres especiais *, exceto o símbolo%* , como parte do valor que deve ser correspondido em uma regra. Por exemplo, uma condição de correspondência literal definida como `'*'` é satisfeita somente quando o valor exato `'*'` é encontrado.

Um sinal de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20` ).

### <a name="wildcard-values"></a>Valores de caractere curinga

Atualmente, damos suporte ao caractere curinga na **condição de correspondência UrlPath** no mecanismo de regras padrão. O \* caractere é um caractere curinga que representa um ou mais caracteres. 

## <a name="next-steps"></a>Próximas etapas

- [Condições de correspondência do mecanismo de regras Standard](cdn-standard-rules-engine-match-conditions.md)
- [Ações no mecanismo de regras Standard](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS usando o mecanismo de regras Standard](cdn-standard-rules-engine.md)
- [Visão geral da CDN do Azure](cdn-overview.md)
