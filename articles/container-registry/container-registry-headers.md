---
title: "Repositórios de Registro de contêiner do Azure | Microsoft Docs"
description: "Como usar os repositórios de Registro de contêiner do Azure para imagens do Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.openlocfilehash: 2090d4c951e2261529bf1b7b361510d5822060a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-registry-repositories"></a>Repositórios de Registro de contêiner do Azure

Os Registros de Contêiner do Azure são compatíveis com uma variedade de serviços e orquestradores. Para tornar mais fácil de controlar os serviços de origem e os agentes dos quais o ACR é usado, começamos usando o campo de cabeçalho do Docker no arquivo Docker.config.



## <a name="viewing-repositories-in-the-portal"></a>Exibindo repositórios no Portal

Os cabeçalhos do ACR seguem o formato:
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* Nuvem: Azure, Azure Stack ou outras nuvens do Azure específicas do país ou do governo. Embora no momento não haja suporte para o Azure Stack e nuvens do governo, este parâmetro habilita suporte futuro.
* Serviço: nome do serviço.
* Optionalservicename: parâmetro opcional para serviços com subserviços ou para especificar uma SKU (ex: aplicativos Web correspondem a Azure/app-service/web-apps).

Orquestradores e serviços de parceiro são incentivados a usar valores de cabeçalho específicos para ajudar em nossa telemetria. Os usuários também podem modificar o valor passado para o cabeçalho, se desejarem.

Os valores que desejamos que os parceiros do ACR usem para preencher o campo "X-Meta-Origem-Cliente" estão abaixo:

| Nome do Serviço              | Cabeçalho                                |
| ------------------------- | ------------------------------------- |
| Serviço de Contêiner do Azure   | azure/compute/azure-container-service |
| Serviço de Aplicativo – Aplicativos Web    | azure/app-service/web-apps            |
| Serviço de Aplicativo – Aplicativos Lógicos  | azure/app-service/logic-apps          |
| Batch                     | azure/compute/batch                   |
| Console de nuvem             | azure/cloud-console                   |
| Funções                 | azure/compute/functions               |
| Internet das Coisas – Hub  | hub/de iot do Azure                         |
| HDInsight                 | hdinsight/de dados do Azure                  |
| Jenkins                   | Azure/jenkins                         |
| Machine Learning          | azure/data/machine-learning           |
| Service Fabric            | Azure/computação/serviço-malha          |
| VSTS                      | azure/vsts                            |


## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre os registros e os serviços e orquestradores com suporte](container-registry-intro.md)
