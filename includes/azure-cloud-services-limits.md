---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172296"
---
| Resource | Limite padrão | Limite máximo |
| --- | --- | --- |
| [Funções de trabalho ou Web por implantação](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Pontos de extremidade de entrada de instância](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) por implantação |25 |25 |
| [Pontos de extremidade de entrada](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) por implantação |25 |25 |
| [Pontos de extremidade internos](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) por implantação |25 |25 |
| [Certificados de serviços hospedados](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) por implantação |199 |199 |

<sup>1</sup>cada serviço de nuvem do Azure com funções web ou de trabalho pode ter duas implantações, um para produção e uma de preparo. Esse limite se refere ao número de funções distintas, ou seja, a configuração. Esse limite não se refere ao número de instâncias por função, ou seja, dimensionamento.

