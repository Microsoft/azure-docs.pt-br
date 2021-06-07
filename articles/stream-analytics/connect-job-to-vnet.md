---
title: Conectar Stream Analytics trabalhos a recursos em uma VNET (rede virtual) do Azure
description: Este artigo descreve como conectar um trabalho de Azure Stream Analytics com recursos que estão em uma VNET.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 4701cb4122b4196b08b2a427b34d49c7784b91a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878232"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Conectar Stream Analytics trabalhos a recursos em uma VNet (rede virtual) do Azure

Seus trabalhos de Stream Analytics fazem conexões de saída com os recursos de entrada e saída do Azure para processar dados em tempo real e produzir resultados. Esses recursos de entrada e saída (por exemplo, hubs de eventos do Azure e banco de dados SQL do Azure) podem estar atrás de um firewall do Azure ou em uma VNet (rede virtual) do Azure. Stream Analytics serviço opera de redes que não podem ser incluídas diretamente em suas regras de rede.

No entanto, há duas maneiras de conectar com segurança seus trabalhos de Stream Analytics aos seus recursos de entrada e saída nesses cenários.
* Usando pontos de extremidade privados em clusters Stream Analytics.
* Usando o modo de autenticação de identidade gerenciada juntamente com a configuração de rede ' permitir serviços confiáveis '.

Seu trabalho de Stream Analytics não aceita nenhuma conexão de entrada.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Pontos de extremidade privados em clusters Stream Analytics.
[Clusters de Stream Analytics](./cluster-overview.md) é um cluster de computação dedicado de locatário único no qual você pode executar seus trabalhos de Stream Analytics. Você pode criar pontos de extremidade privados gerenciados no cluster Stream Analytics, o que permite que todos os trabalhos em execução no cluster façam uma conexão de saída segura com os recursos de entrada e saída.

A criação de pontos de extremidade privados no cluster de Stream Analytics é uma [operação de duas etapas](./private-endpoints.md). Essa opção é mais adequada para cargas de trabalho de streaming médio a grande, pois o tamanho mínimo de um cluster de Stream Analytics é o SUs 36 (embora o 36 SUs possa ser compartilhado por diferentes trabalhos em várias assinaturas ou ambientes, como desenvolvimento, teste e produção).

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>Autenticação de identidade gerenciada com a configuração ' permitir serviços confiáveis '
Alguns serviços do Azure fornecem uma configuração de rede de **serviços confiáveis da Microsoft** , que, quando habilitada, permite que seus trabalhos de Stream Analytics se conectem com segurança ao recurso usando autenticação forte. Essa opção permite que você conecte seus trabalhos aos recursos de entrada e saída sem exigir um Stream Analytics cluster e pontos de extremidade privados. Configurar seu trabalho para usar essa técnica é uma operação de duas etapas:
* Use o modo de autenticação de identidade gerenciada ao configurar entrada ou saída em seu trabalho de Stream Analytics.
* Conceda a seus trabalhos específicos Stream Analytics acesso explícito aos seus recursos de destino atribuindo uma função do Azure à identidade gerenciada atribuída pelo sistema do trabalho. 

A habilitação de **permitir serviços confiáveis da Microsoft** não concede acesso amplo a nenhum trabalho. Isso lhe dá controle total de quais trabalhos de Stream Analytics específicos podem acessar seus recursos com segurança. 

Seus trabalhos podem se conectar aos seguintes serviços do Azure usando essa técnica:
1. [Armazenamento de BLOBs ou Azure data Lake Storage Gen2](./blob-output-managed-identity.md) -pode ser a conta de armazenamento do seu trabalho, transmitir entrada ou saída.
2. [Hubs de eventos do Azure](./event-hubs-managed-identity.md) – pode ser a entrada ou saída de streaming de seu trabalho.

Se os trabalhos precisarem se conectar a outros tipos de entrada ou de saída, você poderá gravar de Stream Analytics para a saída dos hubs de eventos primeiro e, em seguida, para qualquer destino de sua escolha usando Azure Functions. Se você quiser gravar diretamente de Stream Analytics em outros tipos de saída protegidos em uma VNet ou em um firewall, a única opção será usar pontos de extremidade privados em clusters Stream Analytics.

## <a name="next-steps"></a>Próximas etapas

* [Criar e remover pontos de extremidade privados em clusters Stream Analytics](./private-endpoints.md)
* [Conectar-se a hubs de eventos em uma VNet usando a autenticação de identidade gerenciada](./event-hubs-managed-identity.md)
* [Conectar-se ao armazenamento de BLOBs e ADLS Gen2 em uma VNet usando a autenticação de identidade gerenciada](./blob-output-managed-identity.md)