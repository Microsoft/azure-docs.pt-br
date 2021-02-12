---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214737"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Configurar o provedor de impressão digital do sensor

Vamos começar criando e configurando um provedor de impressão digital do sensor. O provedor de impressão digital do sensor será responsável pela leitura dos sensores específicos da plataforma em seu dispositivo e pela conversão das leituras em uma representação comum consumida pela sessão da âncora espacial de nuvem.

> [!IMPORTANT]
> [Verifique aqui](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability) se os sensores que você está habilitando estão disponíveis em sua plataforma.