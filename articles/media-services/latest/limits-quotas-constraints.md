---
title: Cotas e limitações dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Este tópico descreve as cotas e as limitações nos Serviços de Mídia do Azure v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/16/2019
ms.author: juliako
ms.openlocfilehash: 709ed293dbb0550dc1bb43bf1e1e1cc50906cc31
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293435"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Cotas e limitações dos Serviços de Mídia do Azure v3

Este artigo descreve cotas e limitações nos Serviços de Mídia do Azure v3.

| Resource | Limite padrão | 
| --- | --- | 
| Ativos por conta de Serviços de Mídia do Azure | 1\.000.000|
| Filtros de Manifesto Dinâmico|100|
| JobInputs por trabalho | 50  (fixo)|
| JobOutputs por trabalho | 20 (fixo) |
| TransformOutputs em uma transformação | 20 (fixo) |
| Arquivos por JobInput|10 (fixo)|
| Tamanho do arquivo| Em alguns cenários, há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. <sup>(1)</sup> |
| Trabalhos por conta dos Serviços de Mídia | 500.000 <sup>(2)</sup> (fixo)|
| Listar Transformações|Paginar a resposta, com 1000 Transformações por página|
| Listar Trabalhos|Paginar a resposta, com 500 Trabalhos por página|
| Eventos ao vivo por conta dos Serviços de Mídia |5|
| Contas de Serviços de Mídia em uma única assinatura | 25 (fixo) |
| Ao vivo saídas por evento ao vivo |3 <sup>(3)</sup> |
| Duração Máx Live saída | 25 horas |
| Contas de armazenamento | 100<sup>(4)</sup> (fixo) |
| Pontos de extremidade de streaming (interrompidos ou em execução) por conta dos Serviços de Mídia|2 (fixas)|
| Políticas de Streaming | 100 <sup>(5)</sup> |
| Transformações por conta dos Serviços de Mídia | 100  (fixo)|
| Localizadores de streaming exclusivos associados a um Ativo ao mesmo tempo | 100<sup>(6)</sup> (fixas) |
| Opções de acordo com a política de chave de conteúdo |30 | 
| Serviço de entrega por conta da chave de licenças por mês para cada um dos tipos DRM nos serviços de mídia|1\.000.000|

<sup>1</sup> O tamanho máximo com suporte para um único blob atualmente é de até 5 TB no Azure Blob Storage. Limites adicionais aplicam-se os serviços de mídia com base em tamanhos de VM que são usados pelo serviço. O limite de tamanho aplica-se aos arquivos que você carrega e também os arquivos que obterem gerados como resultado do processamento (codificação ou analisando) os serviços de mídia. Se o arquivo de origem for maior que 260 GB, o trabalho provavelmente falhará. 

A tabela a seguir mostra os limites sobre o reservadas de mídia unidades S1, S2 e S3. Se seu arquivo de origem for maior do que os limites definidos na tabela, o trabalho de codificação falhará. Se você codificar a fontes de resolução de 4K de longa duração, você precisa usar unidades reservadas de mídia S3 para alcançar o desempenho necessário. Se você tiver o conteúdo de 4K maior que o limite de 260 GB das unidades reservadas de mídia de S3, entre em contato conosco em amshelp@microsoft.com para possíveis mitigações dar suporte ao seu cenário.

|Tipo de unidade reservada de mídia   |Tamanho máximo de entrada (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. 

Qualquer registro de Trabalho em sua conta com mais de 90 dias será excluído automaticamente, mesmo que o número total de registros esteja abaixo da cota máxima. 

<sup>3</sup> live saídas iniciar na criação e parar quando excluído.

<sup>4</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>5</sup> ao usar um personalizado [Streaming política](https://docs.microsoft.com/rest/api/media/streamingpolicies), você deve criar um conjunto limitado de tais políticas para sua conta de serviço de mídia e reutilizá-los para sua StreamingLocators, sempre que a mesma criptografia opções e protocolos são necessários. Você não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

<sup>6</sup> localizadores de streaming não são projetados para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM).

## <a name="support-ticket"></a>Tíquete de suporte

Para obter recursos que não são fixos, você pode solicitar que as cotas sejam geradas, abrindo um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Inclua informações detalhadas na solicitação sobre as alterações de cota desejadas, cenários de casos de uso e regiões necessárias. <br/>**Não** crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](media-services-overview.md)
