---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96010734"
---
|**Geração do<br>gateway de<br>VPN** |**SKU**   | **S2S/VNet para VNet<br>Túneis** | **P2S<br> Conexões SSTP** | **P2S<br> Conexões IKEv2/OpenVPN** | **Parâmetro de comparação<br>de taxa de transferência total** | **BGP** | **Com redundância de zona** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Geração1**|**Basic**   | Máx. 10    | Máx. 128  | Sem suporte  | 100 Mbps  | Sem suporte| Não |
|**Geração1**|**VpnGw1**  | Máx. 30*   | Máx. 128  | Máx. 250       | 650 Mbps  | Com suporte | Não |
|**Geração1**|**VpnGw2**  | Máx. 30*   | Máx. 128  | Máx. 500       | 1 Gbps    | Com suporte | Não |
|**Geração1**|**VpnGw3**  | Máx. 30*   | Máx. 128  | Máx. 1000      | 1,25 Gbps | Com suporte | Não |
|**Geração1**|**VpnGw1AZ**| Máx. 30*   | Máx. 128  | Máx. 250       | 650 Mbps  | Com suporte | Sim |
|**Geração1**|**VpnGw2AZ**| Máx. 30*   | Máx. 128  | Máx. 500       | 1 Gbps    | Com suporte | Sim |
|**Geração1**|**VpnGw3AZ**| Máx. 30*   | Máx. 128  | Máx. 1000      | 1,25 Gbps | Com suporte | Sim |
|        |            |            |           |                |           |           |     |
|**Geração2**|**VpnGw2**  | Máx. 30*   | Máx. 128  | Máx. 500       | 1,25 Gbps | Com suporte | Não |
|**Geração2**|**VpnGw3**  | Máx. 30*   | Máx. 128  | Máx. 1000      | 2,5 Gbps  | Com suporte | Não |
|**Geração2**|**VpnGw4**  | Máx. 30*   | Máx. 128  | Máx. 5.000      | 5 Gbps    | Com suporte | Não |
|**Geração2**|**VpnGw5**  | Máx. 30*   | Máx. 128  | Máx. 10000      | 10 Gbps   | Com suporte | Não |
|**Geração2**|**VpnGw2AZ**| Máx. 30*   | Máx. 128  | Máx. 500       | 1,25 Gbps | Com suporte | Sim |
|**Geração2**|**VpnGw3AZ**| Máx. 30*   | Máx. 128  | Máx. 1000      | 2,5 Gbps  | Com suporte | Sim |
|**Geração2**|**VpnGw4AZ**| Máx. 30*   | Máx. 128  | Máx. 5.000      | 5 Gbps    | Com suporte | Sim |
|**Geração2**|**VpnGw5AZ**| Máx. 30*   | Máx. 128  | Máx. 10000      | 10 Gbps   | Com suporte | Sim |

(*) Use [WAN Virtual](../articles/virtual-wan/virtual-wan-about.md) se precisar de mais de 30 túneis de VPN S2S.

* O redimensionamento de SKUs VpnGw é permitido dentro da mesma geração, com exceção do redimensionamento de SKU Basic. O SKU Basic é um SKU herdado e tem limitações de recursos. Para avançar da versão Basic para outro SKU VpnGw, você deve excluir o gateway de VPN do SKU Basic e criar um novo gateway com a combinação de tamanho de SKU e Geração desejada.

* Esses limites de conexão são separados. Por exemplo, você pode ter 128 conexões SSTP e 250 conexões IKEv2 em uma SKU VpnGw1.

* Encontre informações sobre preços na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* As informações de SLA (contrato de nível de serviço) podem ser encontradas na página [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Em um único túnel, uma taxa de transferência máxima de 1 Gbps pode ser alcançada. O parâmetro de comparação da taxa de transferência total na tabela acima se baseia nas medidas de vários túneis agregados por meio de um único gateway. O Benchmark Agregado de Taxa de Transferência para um Gateway de VPN é uma combinação de S2S + P2S. **Se você tiver muitas conexões P2S, isso poderá afetar negativamente uma conexão S2S devido a limitações de taxa de transferência.** O Parâmetro de Comparação de Taxa de Transferência Agregada não é uma taxa de transferência garantida devido às condições de tráfego de Internet e seus comportamentos de aplicativo.

Para ajudar nossos clientes a entender o desempenho relativo dos SKUs que usam algoritmos diferentes, usamos ferramentas iPerf e CTSTraffic disponíveis publicamente para medir o desempenho. A tabela abaixo lista os resultados dos testes de desempenho para os SKUs VpnGw de Geração 1. Como você pode ver, o melhor desempenho foi obtido quando usamos o algoritmo GCMAES256 para Criptografia e Integridade de IPsec. Obtivemos o desempenho médio quando usamos AES256 para Criptografia de IPsec e SHA256 para Integridade. Quando usamos DES3 para Criptografia de IPsec e SHA256 para Integridade, o desempenho foi menor.

|**Generation**|**SKU**   | **Algoritmos<br>usados** | **Taxa de transferência<br>observada** | **Pacotes por segundo<br>observados** |
|---           |---       | ---                 | ---            | ---                    |
|**Geração1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50.000<br>50.000|
|**Geração1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55.000|
|**Geração1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90.000<br>60.000|
|**Geração1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50.000<br>50.000|
|**Geração1**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90.000<br>80.000<br>55.000|
|**Geração1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90.000<br>60.000|
