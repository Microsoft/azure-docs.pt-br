---
title: Residência de dados para o observador de rede do Azure | Microsoft Docs
description: Este artigo o ajudará a entender a residência de dados para o serviço observador de rede do Azure.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b5aa8167031c3b871c6a6a4d84159c3c284bf241
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018421"
---
# <a name="data-residency-for-azure-network-watcher"></a>Residência de dados para o observador de rede do Azure
Com exceção do serviço monitor de conexão (versão prévia), o observador de rede do Azure não armazena dados do cliente.


## <a name="connection-monitor-preview-data-residency"></a>Residência de dados do monitor de conexão (visualização)
O serviço monitor de conexão (versão prévia) armazena dados do cliente. Esses dados são armazenados automaticamente pelo observador de rede em uma única região. Portanto, o monitor de conexão (versão prévia) atende automaticamente aos requisitos de residência de dados em região, incluindo os requisitos especificados na [central de confiabilidade](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Residência de dadosResidência de dados
No Azure, o recurso que habilita o armazenamento de dados do cliente em uma única região está disponível no momento apenas na região do Sudeste Asiático (Cingapura) da região Pacífico Asiático geográfica e do Sul do Brasil (estado de São Paulo) da geografia do Brasil. Para todas as outras regiões, os dados do cliente são armazenados na Área geográfica. Para obter mais informações, confira a [Central de Confiabilidade](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Próximas etapas

* Leia uma visão geral do [observador de rede](./network-watcher-monitoring-overview.md).