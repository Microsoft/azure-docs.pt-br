---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: c98d88d66e89df3efe186212d8c004ec2a65158d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106450240"
---
| Recurso | Limite | Observação |
| --- | --- | --- |
| Gateway de Aplicativo do Azure |1\.000 por assinatura | |
| Configurações de IP de front-end |2 |1 pública e 1 privada |
| Portas de front-end |100<sup>1</sup> | |
| Pools de endereços de back-end |100<sup>1</sup> | |
| Servidores de back-end por pool |1.200 | |
| Ouvintes HTTP |200<sup>1</sup> |Limitado a 100 ouvintes ativos que estão roteando tráfego. Ouvintes ativos = número total de ouvintes - ouvintes não ativos.<br>Se uma configuração padrão dentro de uma regra de roteamento for definida para rotear o tráfego (por exemplo, ela tem um ouvinte, um pool de back-end e configurações de HTTP), isso também contará como um ouvinte.|
| Regras de balanceamento de carga de HTTP |100<sup>1</sup> | |
| Configurações de HTTP de back-end |100<sup>1</sup> | |
| Instâncias por gateway |SKU V1 – 32<br>SKU V2 – 125 | |
| Certificados SSL |100<sup>1</sup> |1 por ouvinte HTTP |
| Tamanho máximo do certificado SSL |SKU V1 – 10 KB<br>SKU V2 – 16 KB| |
| Certificados de autenticação |100 | |
| Certificados raiz confiáveis |100 | |
| Tempo limite mínimo da solicitação |1 segundo | |
| Tempo limite máximo da solicitação |24 horas | |
| Número de sites |100<sup>1</sup> |1 por ouvinte HTTP |
| Mapas de URL por ouvinte |1 | |
| Regras com base em caminho máximo por mapa de URL|100||
| Configurações de redirecionamento |100<sup>1</sup>| |
| Número de conjuntos de regras de regravação |400| |
| Número de configurações de Cabeçalho ou URL por conjunto de regras de regravação|40| |
| Número de condições por conjunto de regras de regravação|40| |
| Conexões WebSocket Simultâneas |Gateways médios 20 mil<sup>2</sup><br> Gateways grandes 50 mil<sup>2</sup>| |
| Tamanho máximo da URL|32 KB| |
| Tamanho máximo do cabeçalho para HTTP/2 |4 KB| |
| Tamanho de carregamento de arquivo máximo, Padrão |2 GB | |
| Tamanho de carregamento de arquivo máximo WAF |Gateways de WAF médios V1, 100 MB<br>Gateways de WAF grandes V1, 500 MB<br>WAF V2, 750 MB| |
| Limite de tamanho de corpo de WAF, sem arquivos|128 KB||
| WAF máximo de regras personalizadas|100||
| Número máximo de exclusões de WAF por Gateway de Aplicativo|40||

<sup>1</sup> No caso de SKUs habilitados para WAF, limite o número de recursos a 40.

<sup>2</sup> O limite é por instância do Gateway de Aplicativo, não por recurso do Gateway de Aplicativo.
