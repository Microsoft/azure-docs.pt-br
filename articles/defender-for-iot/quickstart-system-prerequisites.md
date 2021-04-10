---
title: Pré-requisitos do sistema
description: Obtenha os pré-requisitos do sistema necessários para executar o Azure Defender para IoT.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 78d5948554ae531c4b2f77d67bb916d5290db943
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780932"
---
# <a name="system-prerequisites"></a>Pré-requisitos do sistema
Este artigo lista os pré-requisitos do sistema para executar o Azure Defender para IoT.

## <a name="minimum-requirements"></a>Requisitos mínimos

- Comutadores de rede que dão suporte ao monitoramento de tráfego por meio da porta SPAN.
- Dispositivos de hardware para sensores NTA.
- A função Colaborador da assinatura do Azure. Ela é necessária apenas durante a integração para definir os dispositivos confirmados e a conexão com o Azure Sentinel.
- Função **Colaborador** do Hub IoT do Azure (camada Gratuita ou Standard), para gerenciamento conectado à nuvem. Verifique se o recurso **Azure Defender para IoT** está habilitado.
- Para obter suporte do microagente do Defender para IoT no nível do dispositivo, os agentes do Defender para IoT dão suporte a uma lista crescente de dispositivos e plataformas. Confira a [lista de plataformas com suporte](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Regiões de serviço com suporte

O Defender para IoT roteia todo o tráfego de todas as regiões europeias para o datacenter regional do Oeste da Europa. Ele roteia o tráfego de todas as regiões restantes para o datacenter regional de EUA Central.

Para obter mais informações, confira [Regiões do hub IoT com suporte](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="see-also"></a>Consulte também

- [Identificar os dispositivos necessários](how-to-identify-required-appliances.md)
- [Sobre a configuração de rede do Azure Defender para IoT](how-to-set-up-your-network.md)
