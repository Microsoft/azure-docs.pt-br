---
title: 'Guia de início rápido: Pré-requisitos do sistema'
description: Neste guia de início rápido, obtenha os pré-requisitos do sistema necessários para executar o Azure Defender para IoT.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 2aae849b6ee772c2aa29c680f3b107af3ed600b0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382939"
---
# <a name="quickstart-system-prerequisites"></a>Guia de início rápido: Pré-requisitos do sistema

Este artigo lista os pré-requisitos do sistema para executar o Azure Defender para IoT.

## <a name="prerequisites"></a>Pré-requisitos

- Nenhum

## <a name="minimum-requirements"></a>Requisitos mínimos

- Comutadores de rede que dão suporte ao monitoramento de tráfego por meio da porta SPAN.
- Dispositivos de hardware para sensores NTA.
- A função Colaborador da assinatura do Azure. Ela é necessária apenas durante a integração para definir os dispositivos confirmados e a conexão com o Azure Sentinel.
- Função **Colaborador** do Hub IoT do Azure (camada Gratuita ou Standard), para gerenciamento conectado à nuvem. Verifique se o recurso **Azure Defender para IoT** está habilitado.
- Para obter suporte do microagente do Defender para IoT no nível do dispositivo, os agentes do Defender para IoT dão suporte a uma lista crescente de dispositivos e plataformas. Confira a [lista de plataformas com suporte](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Regiões de serviço com suporte

O Defender para IoT roteia todo o tráfego de todas as regiões europeias para o datacenter regional do Oeste da Europa. Ele roteia o tráfego de todas as regiões restantes para o datacenter regional de EUA Central.

Para obter mais informações, confira [Regiões do hub IoT com suporte](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Identificar os dispositivos necessários](how-to-identify-required-appliances.md)
