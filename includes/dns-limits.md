---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/05/2020
ms.author: rohink
ms.openlocfilehash: 7011b92485c56187021c9043ba84bc85e448a98f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94329519"
---
**Zonas DNS públicas**

| Recurso | Limite |
| --- | --- |
| Zonas DNS públicas por assinatura |250 <sup>1</sup> |
| Conjuntos de registros por zona DNS pública |10.000 <sup>1</sup> |
| Registros por conjunto de registros na zona DNS pública |20 |
| Número de registros de Alias de um recurso do Azure |20|

<sup>1</sup>Se precisar aumentar esses limites, entre em contato com o Suporte do Azure.

**Zonas DNS privadas**

| Recurso | Limite |
| --- | --- |
| Zonas DNS privadas por assinatura |1000|
| Conjuntos de registros por zona DNS privada |25000|
| Registros por conjunto de registros para zonas DNS privadas |20|
| Links de Redes Virtuais por zona DNS privada |1000|
| Links de Redes Virtuais por zonas DNS privadas com um registro automático habilitado |100|
| Número de zonas DNS privadas às quais uma rede virtual poderá ser vinculada usando um registro automático habilitado |1|
| Número de zonas DNS privadas às quais uma rede virtual poderá ser vinculada |1000|
| Número de consultas DNS que uma máquina virtual poderá enviar ao resolvedor DNS do Azure por segundo |1000 <sup>1</sup> |
| Número máximo de consultas DNS enfileiradas (resposta pendente) por máquina virtual |200 <sup>1</sup> |

<sup>1</sup>Esses limites serão aplicados a cada máquina virtual individual, não no nível de rede virtual. As consultas DNS que excederem esses limites serão descartadas.
