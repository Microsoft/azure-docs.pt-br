---
title: Conexão do serviço de emparelhamento do Azure
description: Saiba mais sobre Microsoft Azure conexão de serviço de emparelhamento
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0540fa9c7a29456aaf343adce83509b2d0e390e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91398913"
---
# <a name="peering-service-connection"></a>Conexão de serviço de emparelhamento

Uma conexão normalmente se refere a um conjunto de informações lógicas, identificando um serviço de emparelhamento. Ele é definido especificando os seguintes atributos:

- Nome Lógico
- Parceiro de conectividade
- Local físico do cliente
- Prefixos de IP

O cliente pode estabelecer uma única conexão ou várias conexões de acordo com o requisito. Uma conexão também é usada como uma unidade de coleção de telemetria. Por exemplo, para aceitar alertas de telemetria, o cliente deve definir a conexão que será monitorada.

> [!Note]
> Quando você se inscreve no serviço de emparelhamento, analisamos sua telemetria do Windows e Microsoft 365 para fornecer medidas de latência para os prefixos selecionados.
>Para obter mais informações sobre telemetria de conexão, consulte a [telemetria de conexão de serviço de emparelhamento](connection-telemetry.md).
>

>## <a name="how-to-register-a-connection"></a>Como registrar uma conexão?

**Cenário** – digamos que uma filial esteja distribuída entre diferentes localizações geográficas, conforme mostrado na figura abaixo. Aqui, o cliente precisa fornecer um nome lógico, o nome do provedor de serviços (SP), o local físico do cliente e os prefixos IP que são (pertencentes ao cliente ou alocados pelo provedor de serviços) associados a uma única conexão. Esse processo deve ser repetido para registrar o serviço de emparelhamento para conexões separadas com redundância geográfica.

![Conexões geográficas com redundância](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> Nível de estado-filtragem é considerado para o local físico do cliente quando a conexão é localizada geograficamente no Estados Unidos.
>

## <a name="next-steps"></a>Próximas etapas

Para aprender o processo passo a passo sobre como registrar a conexão de serviço de emparelhamento, consulte [registrar serviço de emparelhamento usando o portal do Azure](azure-portal.md).

Para saber mais sobre telemetria de conexão do Serviço de Emparelhamento, confira [Telemetria de conexão do Serviço de Emparelhamento](connection-telemetry.md).

Para medir a telemetria, confira [Medir telemetria de conexão](measure-connection-telemetry.md).
