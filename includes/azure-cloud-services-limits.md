---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85838854"
---
| Recurso | Limite |
| --- | --- |
| [Funções Web ou de trabalho por implantação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Pontos de extremidade de entrada da instância](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) por implantação |25 |
| [Pontos de extremidade de entrada](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) por implantação |25 |
| [Pontos de extremidade internos](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) por implantação |25 |
| [Certificados de serviços hospedados](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implantação |199 |

<sup>1</sup>Cada Serviço de Nuvem do Azure com funções de trabalho ou Web poderá ter duas implantações: uma para a produção e outra para o preparo. Esse limite se refere ao número de funções distintas, ou seja, configurações. Esse limite não se refere ao número de instâncias por função, ou seja, escalas.

