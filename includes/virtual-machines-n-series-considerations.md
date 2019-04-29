---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1de7221f100077e07a2211bdb94e0198b35cb77c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776298"
---
## <a name="deployment-considerations"></a>Considerações de implantação

* Para ver a disponibilidade das VMs da Série N, veja [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

* As VMs da Série N só podem ser implantadas no modelo de implantação do Resource Manager.

* As VMs da série N diferem no tipo de Armazenamento do Microsoft Azure que dão suporte aos discos. As VMs NC e NV dão suporte somente a discos de VM que executam backup em HDD (Armazenamento em Disco Standard). As NCv2, NCv3, ND, NDv2, e’ NVv2são compatíveis apenas com discos de VM que executam backup em SSD (Armazenamento em Disco Premium).

* Se você quiser implantar mais do que algumas VMs da Série N, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

* Talvez seja necessário aumentar a cota de núcleos (por região) na sua assinatura do Azure, e aumentar a cota separada para núcleos NC, NCv2, NCv3, ND, NDv2, NV, ou NVv2. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../articles/azure-supportability/how-to-create-azure-support-request.md) gratuitamente. Os limites padrão podem variar dependendo de sua categoria de assinatura.




