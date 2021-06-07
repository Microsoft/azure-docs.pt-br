---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: include
ms.date: 01/12/2021
ms.author: wellee
ms.custom: include file
ms.openlocfilehash: 67a996dbe1eb7090ea5f9ee9f0880698f4ba74a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919053"
---
Esta seção descreve as diretrizes e os requisitos para alocar espaços de endereço de cliente em que a unidade de escala do gateway de VPN ponto a site do Hub de WAN Virtual escolhida é maior ou igual a 40.

### <a name="background"></a>Tela de fundo

Gateways de VPN ponto a site no hub de WAN Virtual são implantados com várias instâncias. Cada instância de um gateway de VPN ponto a site pode dar suporte a até 10.000 conexões de usuário ponto a site simultâneas. Como resultado, para unidades de escala maiores que 40, a WAN Virtual precisa implantar capacidade extra, o que exige um número mínimo de pools de endereços alocados para unidades de escala diferentes.

Por exemplo, se uma unidade de escala de cem for escolhida, cinco instâncias serão implantadas para o gateway de VPN ponto a site no Hub Virtual. Essa implantação pode dar suporte a 50.000 conexões simultâneas e a **pelo menos** 5 pools de endereços distintos.

**Unidades de escala disponíveis**

| Unidade de Escala | Número máximo de clientes com suporte | Número mínimo de pools de endereços |
|--- |--- |--- |
| 40 | 20000 | 2 |
| 60 | 30000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100000 | 10 |

### <a name="specifying-address-pools"></a>Como especificar pools de endereços

Abaixo estão algumas diretrizes para escolha de pools de endereços. Observe que as atribuições de pool de endereços de VPN ponto a site são feitas automaticamente pela WAN Virtual.

1. Uma instância de gateway permite no máximo 10.000 conexões simultâneas. Assim, cada pool de endereços deve conter pelo menos dez mil endereços IP RFC1918 exclusivos.
1. Vários intervalos de pool de endereços são automaticamente combinados e atribuídos a apenas **uma** instância de gateway. Esse processo é feito de modo round robin para qualquer instância de gateway que tenha menos de 10.000 endereços IP. Por exemplo, um pool com 5.000 endereços pode ser combinado automaticamente pela WAN Virtual com outro pool que tenha 8.000 endereços e seja atribuído a apenas uma instância de gateway.
1. Um pool de endereços individual é atribuído a apenas uma instância de gateway pela WAN Virtual.
1. Os pools de endereços precisam ser distintos. Não pode haver nenhuma sobreposição entre pools de endereços.

> [!NOTE] 
> Se um pool de endereços estiver associado a uma instância de gateway que estiver passando por manutenção, o pool de endereços não poderá ser atribuído novamente a outra instância.

### <a name="example"></a>Exemplo 

O exemplo a seguir descreve uma situação em que 60 unidades de escala dão suporte a até trinta mil conexões, mas os pools de endereços alocados resultam em menos de trinta mil conexões simultâneas.

O número total de conexões simultâneas com suporte por essa configuração é de 28.192. A primeira instância de gateway dá suporte a 10.000 endereços, a segunda instância a 8.192 conexões e a terceira instância também dá suporte a 10.000 endereços.

| Número do pool de endereços | Pool de endereços | Conexões com suporte |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | 10.14.0.0/15 | 10000|

**Recomendação nº 1: Verifique se o pool de endereços nº 2 tem pelo menos dez mil endereços IP distintos. (exemplo: 10.13.0.0/15)**

**Recomendação nº 2: Adicione mais um pool de endereços. (exemplo: Pool de endereços nº 4 10.15.0.0/21 com 2.048 endereços). Os pools de endereços 2 e 4 serão automaticamente combinados e permitirão que essa instância de gateway dê suporte a 10.000 conexões simultâneas.**
