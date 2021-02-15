---
title: incluir arquivo
description: incluir arquivo
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 361fb18574e90fc46e45bff8914c51ee1afa2f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89411745"
---
| Recurso | Limite |
| --- | --- |
| Recursos do Azure Front Door por assinatura | 100 |
| Hosts de front-end, que incluem domínios personalizados por recurso | 500 |
| Regras de roteamento por recurso | 500 |
| Pools de back-end por recurso | 50 |
| Back-ends por pool de back-end | 100 |
| Padrões de caminho para corresponder a uma regra de roteamento | 25 |
| URLs em uma chamada de limpeza de cache | 100 |
| Regras de firewall do aplicativo Web personalizadas por política | 100 |
| Política de firewall do aplicativo Web por assinatura | 100 |
| Condições de correspondência do firewall do aplicativo Web por regra personalizada | 10 |
| Intervalos de endereços IP de firewall do aplicativo Web por condição de correspondência | 600 |
| Valores de correspondência da cadeia de caracteres do firewall do aplicativo Web por condição de correspondência | 10 |
| Comprimento do valor de correspondência da cadeia de caracteres do firewall do aplicativo Web | 256 |
| Comprimento do nome do parâmetro de corpo de POST do firewall do aplicativo Web | 256 |
| Comprimento do nome do cabeçalho HTTP do firewall do aplicativo Web | 256 |
| Comprimento do nome do cookie do firewall do aplicativo Web | 256 |
| Tamanho do corpo da solicitação HTTP do firewall do aplicativo Web inspecionado | 128 KB |
| Comprimento do corpo da resposta personalizada do firewall do aplicativo Web | 2 KB |

### <a name="timeout-values"></a>Valores de tempo limite
#### <a name="client-to-front-door"></a>Cliente para o Front Door
* O Front Door tem um tempo limite de conexão TCP ocioso de 61 segundos.

#### <a name="front-door-to-application-back-end"></a>Front Door para o back-end do aplicativo
* Se a resposta for uma resposta em partes, 200 será retornado se ou quando a primeira parte for recebida.
* Depois que a solicitação HTTP for encaminhada para o back-end, o Front Door aguardará 30 segundos para o primeiro pacote do back-end. Em seguida, ele retorna um erro 503 para o cliente. Esse valor é configurável por meio do campo sendRecvTimeoutSeconds na API.
    * Para cenários de cache, esse tempo limite não é configurável e, portanto, se uma solicitação for armazenada em cache e o primeiro pacote do Front Door ou do back-end demorar mais de 30 segundos, um erro 504 será retornado ao cliente. 
* Após o recebimento do primeiro pacote do back-end, o Front Door aguarda 30 segundos em um tempo limite ocioso. Em seguida, ele retorna um erro 503 para o cliente. Esse valor de tempo limite não é configurável.
* O tempo limite da sessão do Front Door para o TCP de back-end é de 90 segundos.

### <a name="upload-and-download-data-limit"></a>Carregar e baixar o limite de dados

|  | Com CTE (codificação de transferência em partes) | Sem agrupamento de HTTP |
| ---- | ------- | ------- |
| **Download** | Não há nenhum limite para o tamanho do download. | Não há nenhum limite para o tamanho do download. |
| **Upload** |    Não há limite, desde que cada upload de CTE seja inferior a 2 GB. | O tamanho não pode ser maior do que 2 GB. |

### <a name="other-limits"></a>Outros limites
* Tamanho máximo da URL (8.192 bytes) – Especifica o comprimento máximo da URL bruta (esquema + nome do host + porta + caminho + cadeia de consulta da URL)
* Tamanho máximo da Cadeia de Caracteres de Consulta (4.096 bytes) – Especifica o comprimento máximo da cadeia de caracteres de consulta, em bytes.
* O tamanho máximo do cabeçalho de resposta HTTP da URL de investigação de integridade (4.096 bytes) – Especifica o comprimento máximo de todos os cabeçalhos de resposta das investigações de integridade. 
