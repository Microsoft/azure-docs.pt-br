---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 419d8d0cd20c13bffdd192060d81975ee94b47fe
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106125981"
---
> [!NOTE]
> Para recursos que não são corrigidos, abra um tíquete de suporte para solicitar um aumento nas cotas. Não crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

### <a name="account-limits"></a>Limites de conta

| Recurso | Limite padrão |
| --- | --- |
| Contas de Serviços de Mídia em uma única assinatura | 100 (fixo) |

### <a name="asset-limits"></a>Limites de ativo

| Recurso | Limite padrão |
| --- | --- |
| Ativos por conta dos Serviços de Mídia | 1\.000.000|

### <a name="storage-media-limits"></a>Limites de armazenamento (mídia)

| Recurso | Limite padrão |
| --- | --- |
| Tamanho do arquivo| Em alguns cenários, há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. <sup>(1)</sup> |
| Contas de armazenamento | 100<sup>(2)</sup> (fixo) |

<sup>1</sup> O tamanho máximo com suporte para um único blob atualmente é de até 5 TB no Azure Blob Storage. Os limites adicionais aplicam-se nos Serviços de Mídia com base nos tamanhos de VM usados pelo serviço. O limite de tamanho aplica-se aos arquivos que você carrega e também aos arquivos gerados como resultado do processamento dos Serviços de Mídia (codificação ou análise). Se o arquivo de origem for maior que 260 GB, o trabalho provavelmente falhará.

A tabela a seguir mostra os limites nas unidades reservada para mídia S1, S2 e S3. Se o arquivo de origem for maior do que os limites definidos na tabela, o trabalho de codificação falhará. Se você codificar fontes de resolução 4K de longa duração, deverá usar unidades reservadas para mídia S3 para alcançar o desempenho necessário. Se você tiver um conteúdo em 4K maior do que o limite de 260 GB nas unidades reservadas para mídia S3, abra um tíquete de suporte.

|Tipo de unidade reservada para mídia|Tamanho máximo de entrada (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

### <a name="jobs-encoding--analyzing-limits"></a>Limites de trabalhos (codificação e análise)

| Recurso | Limite padrão |
| --- | --- |
| Trabalhos por conta dos Serviços de Mídia | 500.000<sup>(3)</sup> (fixo)|
| Entradas de trabalho por trabalho | 50  (fixo)|
| Saídas de trabalho por trabalho | 20 (fixo) |
| Transformações por conta dos Serviços de Mídia | 100  (fixo)|
| Transformar saídas em uma transformação | 20 (fixo) |
| Arquivos por entrada de trabalho|10 (fixo)|

<sup>3</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. 

Qualquer registro de Trabalho em sua conta com mais de 90 dias será excluído automaticamente, mesmo que o número total de registros esteja abaixo da cota máxima. 

### <a name="live-streaming-limits"></a>Limites da transmissão ao vivo

| Recurso | Limite padrão |
| --- | --- |
| Eventos ao vivo <sup>(4)</sup> por conta dos Serviços de Mídia |5|
| Saídas ao vivo por evento ao vivo |3 <sup>(5)</sup> |
| Duração máxima da saída ao vivo | [Tamanho da janela DVR](../articles/media-services/latest/live-event-cloud-dvr-time-how-to.md) |

<sup>4</sup> Para obter informações detalhadas sobre as limitações de Eventos ao Vivo, confira [Comparação e limitações dos tipos de eventos ao vivo](../articles/media-services/latest/live-event-types-comparison-reference.md).

<sup>5</sup> As Saídas ao Vivo começam na criação e terminam quando são excluídas.

### <a name="packaging--delivery-limits"></a>Limites de entrega e empacotamento

| Recurso | Limite padrão |
| --- | --- |
| Pontos de extremidade de streaming (interrompidos ou em execução) por conta dos Serviços de Mídia| 2 |
| Filtros de Manifesto Dinâmico|100|
| Políticas de Streaming | 100 <sup>(6)</sup> |
| Localizadores de streaming exclusivos associados a um Ativo ao mesmo tempo | 100<sup>(7)</sup> (fixo) |

<sup>6</sup> Ao usar uma [Política de Streaming](/rest/api/media/streamingpolicies) personalizada, você deve criar um conjunto limitado de políticas para a conta de Serviço de Mídia e reutilizá-las para os Localizadores de Streaming sempre que as mesmas opções e protocolos de criptografia forem necessários. Você não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

<sup>7</sup> Os Localizadores de Streaming não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM).

### <a name="protection-limits"></a>Limites de proteção

| Recurso | Limite padrão |
| --- | --- |
| Opções por Política de Chave de Conteúdo | 30 |
| Licenças por mês para cada um dos tipos de DRM no serviço de entrega de chave dos Serviços de Mídia por conta|1\.000.000|

### <a name="support-ticket"></a>Tíquete de suporte

Para obter recursos que não são fixos, você pode solicitar que as cotas sejam geradas, abrindo um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Inclua informações detalhadas na solicitação sobre as alterações de cota desejadas, cenários de casos de uso e regiões necessárias. <br/>**Não** crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.