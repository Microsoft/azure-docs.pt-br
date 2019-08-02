---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: deca0034996f6c8ddcac71cd4f191c1a0659b655
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333364"
---
| Resource | Limite máximo/padrão |
| --- | --- |
| Recursos de serviço de porta da frente do Azure por assinatura | 100 |
| Hosts de front-end, que inclui os domínios personalizados por recurso | 100 |
| Regras de roteamento por recurso | 100 |
| Pools de back-end por recurso | 50 |
| Back-ends por pool de back-end | 100 |
| Padrões de caminho para corresponder a uma regra de roteamento | 25 |
| Regras de firewall do aplicativo Web personalizadas por política | 10 |
| Política do firewall do aplicativo Web por recurso | 100 |
| Condições de correspondência de firewall de aplicativo Web por regra personalizada | 10 |
| Condição de correspondência de intervalos de endereços IP do Web application firewall por | 600 |
| Valores de correspondência de cadeia de caracteres do Web application firewall por condição de correspondência | 10 |
| Comprimento do valor do Web application firewall cadeia de caracteres correspondência | 256 |
| Firewall do aplicativo Web comprimento do nome de parâmetro de corpo de POSTAGEM | 256 |
| Firewall do aplicativo Web comprimento do nome de cabeçalho HTTP | 256 |
| Comprimento do nome de cookie do Web application firewall | 256 |
| Tamanho de corpo da solicitação no Web application firewall HTTP inspecionado | 128 KB |
| Comprimento do corpo de resposta personalizada do Web application firewall | 2 KB |

### <a name="timeout-values"></a>Valores de tempo limite
#### <a name="client-to-front-door"></a>Cliente para o Front Door
- O Front Door tem um tempo limite de conexão TCP ocioso de 61 segundos.

#### <a name="front-door-to-application-back-end"></a>Porta da frente para back-end do aplicativo
- Se a resposta é uma resposta em partes, uma resposta 200 é retornado se ou quando a primeira parte é recebida.
- Depois que a solicitação HTTP é encaminhada para o back-end, porta da frente aguarda 30 segundos para o primeiro pacote de back-end. Em seguida, ele retorna um erro 503 ao cliente.
- Depois que o primeiro pacote é recebido do back-end, porta da frente aguarda 30 segundos em um tempo limite de ociosidade. Em seguida, ele retorna um erro 503 ao cliente.
- Porta da frente para o tempo limite da sessão TCP back-end é de 30 minutos.

### <a name="upload-and-download-data-limit"></a>Carregar e baixar o limite de dados

|  | Com codificação (CTE) de transferência em partes | Sem agrupamento de HTTP |
| ---- | ------- | ------- |
| **Baixar** | Não há nenhum limite no tamanho do download. | Não há nenhum limite no tamanho do download. |
| **Upload** |  Não há nenhum limite desde que cada carregamento CTE for menor que 2 GB. | O tamanho não pode ser maior que 2 GB. |

### <a name="other-limits"></a>Outros limites
- Tamanho máximo da URL - 8.192 bytes - especifica o comprimento máximo da URL bruta (esquema + nome de host + porta + caminho + cadeia de caracteres da URL de consulta) - tamanho da cadeia de caracteres de consulta máximo - 4.096 bytes - especifica o comprimento máximo da cadeia de caracteres de consulta, em bytes.