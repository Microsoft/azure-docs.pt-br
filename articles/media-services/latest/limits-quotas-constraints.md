---
title: Cotas e limites nos serviços de mídia do Azure
description: Este tópico descreve cotas e limites no Serviços de Mídia do Microsoft Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: d6ca7a444f2a3d4babe220548edb10bd37784be7
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678116"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Cotas e limites dos serviços de mídia do Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo lista alguns dos limites de Serviços de Mídia do Microsoft Azure mais comuns, que também são chamados de cotas.

> [!NOTE]
> Para recursos que não são corrigidos, abra um tíquete de suporte para solicitar um aumento nas cotas. Não crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

## <a name="account-limits"></a>Limites de conta

| Recurso | Limite padrão |
| --- | --- |
| [Contas de serviços de mídia](media-services-account-concept.md) em uma única assinatura | 100 (fixo) |

## <a name="asset-limits"></a>Limites de ativo

| Recurso | Limite padrão |
| --- | --- |
| [Ativos](assets-concept.md) por conta de serviços de mídia | 1\.000.000|

## <a name="storage-limits"></a>Limites de armazenamento

| Recurso | Limite padrão | 
| --- | --- | 
| Tamanho do arquivo| Em alguns cenários, há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. <sup>(1)</sup> |
| [Contas de armazenamento](storage-account-concept.md) | 100<sup>(2)</sup> (fixo) |

<sup>1</sup> O tamanho máximo com suporte para um único blob atualmente é de até 5 TB no Azure Blob Storage. Os limites adicionais aplicam-se nos Serviços de Mídia com base nos tamanhos de VM usados pelo serviço. O limite de tamanho aplica-se aos arquivos que você carrega e também aos arquivos gerados como resultado do processamento dos Serviços de Mídia (codificação ou análise). Se o arquivo de origem for maior que 260 GB, o trabalho provavelmente falhará. 

A tabela a seguir mostra os limites nas unidades reservada para mídia S1, S2 e S3. Se o arquivo de origem for maior do que os limites definidos na tabela, o trabalho de codificação falhará. Se você codificar fontes de resolução 4K de longa duração, deverá usar unidades reservadas para mídia S3 para alcançar o desempenho necessário. Se você tiver um conteúdo em 4K maior do que o limite de 260 GB nas unidades reservadas para mídia S3, abra um tíquete de suporte.

|Tipo de unidade reservada para mídia|Tamanho máximo de entrada (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

## <a name="jobs-encoding--analyzing-limits"></a>Limites de trabalhos (codificação e análise)

| Recurso | Limite padrão | 
| --- | --- | 
| [Trabalhos](transforms-jobs-concept.md) por conta de serviços de mídia | 500.000<sup>(3)</sup> (fixo)|
| Entradas de trabalho por trabalho | 50  (fixo)|
| Saídas de trabalho por trabalho | 20 (fixo) |
| [Transformações](transforms-jobs-concept.md) por conta de serviços de mídia | 100  (fixo)|
| Transformar saídas em uma transformação | 20 (fixo) |
| Arquivos por entrada de trabalho|10 (fixo)|

<sup>3</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. 

Qualquer registro de Trabalho em sua conta com mais de 90 dias será excluído automaticamente, mesmo que o número total de registros esteja abaixo da cota máxima. 

## <a name="live-streaming-limits"></a>Limites da transmissão ao vivo

| Recurso | Limite padrão | 
| --- | --- | 
| [Eventos ao vivo](live-events-outputs-concept.md) <sup>(4)</sup> por conta de serviços de mídia |5|
| Saídas ao vivo por evento ao vivo |3 <sup>(5)</sup> |
| Duração máxima da saída ao vivo | [Tamanho da janela DVR](live-event-cloud-dvr.md) |

<sup>4</sup> para obter informações detalhadas sobre limites de eventos ao vivo, consulte [comparação e limites de tipos de eventos ao vivo](live-event-types-comparison.md).

<sup>5</sup> As Saídas ao Vivo começam na criação e terminam quando são excluídas.

## <a name="packaging--delivery-limits"></a>Limites de entrega e empacotamento

| Recurso | Limite padrão |
| --- | --- |
| [Pontos de extremidade de streaming](streaming-endpoint-concept.md) (interrompidos ou em execução) por conta de serviços de mídia | 2 |
| Unidades de streaming Premium | 10 |
| [Filtros de Manifesto Dinâmico](filters-dynamic-manifest-overview.md)|100|
| [Políticas de Streaming](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| [Localizadores de streaming](streaming-locators-concept.md) exclusivos associados a um ativo ao mesmo tempo | 100<sup>(7)</sup> (fixo) |

<sup>6</sup> Ao usar uma [Política de Streaming](/rest/api/media/streamingpolicies) personalizada, você deve criar um conjunto limitado de políticas para a conta de Serviço de Mídia e reutilizá-las para os Localizadores de Streaming sempre que as mesmas opções e protocolos de criptografia forem necessários. Você não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

<sup>7</sup> Os Localizadores de Streaming não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM).

## <a name="protection-limits"></a>Limites de proteção

| Recurso | Limite padrão |
| --- | --- |
| Opções por [política de chave de conteúdo](content-key-policy-concept.md) |30 |
| Licenças por mês para cada um dos tipos de DRM no serviço de entrega de chave dos Serviços de Mídia por conta|1\.000.000|

## <a name="support-ticket"></a>Tíquete de suporte

Para obter recursos que não são fixos, você pode solicitar que as cotas sejam geradas, abrindo um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Inclua informações detalhadas na solicitação sobre as alterações de cota desejadas, cenários de casos de uso e regiões necessárias. <br/>**Não** crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](media-services-overview.md)
