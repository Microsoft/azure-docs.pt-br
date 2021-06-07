---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: f4bdd5332b3c10cf375c7c04ede25137328714a2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106072927"
---
>[!NOTE]
>Para recursos que não são corrigidos, abra um tíquete de suporte para solicitar um aumento nas cotas. Não crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

| Recurso | Limite | 
| --- | --- | 
| Contas de Serviços de Mídia do Azure em uma assinatura única | 25 (fixo) |
| Unidades reservadas para mídia por conta dos Serviços de Mídia |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Trabalhos por conta dos Serviços de Mídia | 50.000<sup>2</sup> |
| Tarefas encadeadas por trabalho | 30 (fixo) |
| Ativos por conta dos Serviços de Mídia | 1\.000.000|
| Ativos por tarefa | 50 |
| Ativos por trabalho | 100 |
| Localizadores exclusivos associados a um ativo simultaneamente | 5<sup>4</sup> |
| Canais ao vivo por conta dos Serviços de Mídia |5|
| Programas no estado interrompido por canal  |50|
| Programa em estado de execução por canal  |3|
| Pontos de extremidade de streaming interrompidos ou em execução por conta dos Serviços de Mídia|2|
| Unidades de streaming por ponto de extremidade de streaming |10 |
| Contas de armazenamento | 100<sup>5</sup> (fixo) |
| Políticas | 1,000,000<sup>6</sup> |
| Tamanho do arquivo| Em alguns cenários, há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia.<sup>7</sup> |

<sup>1</sup>Se você alterar o tipo, por exemplo, de S2 para S1, os limites de unidade reservada máximos serão redefinidos.

<sup>2</sup>Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. Você pode excluir trabalhos antigos usando **IJob.Delete** ou a solicitação HTTP **DELETE**.

A partir de 1º de abril de 2017, qualquer registro de trabalho em sua conta com mais de 90 dias é excluído automaticamente, junto com os registros das tarefas associadas. A exclusão automática ocorre mesmo que o número total de registros esteja abaixo da cota máxima. Para arquivar as informações de trabalho e tarefa, você pode usar o código descrito em [Gerenciar ativos com o SDK do .NET dos Serviços de Mídia](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>Ao fazer uma solicitação para listar as entidades de trabalho, serão retornados no máximo mil trabalhos por solicitação. Para manter o controle de todos os trabalhos enviados, use as consultas superior ou ignorar conforme descrito em [Opções de consulta do sistema OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>Os localizadores não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de DRM (gerenciamento de direitos digitais). Para obter mais informações, confira [Proteger seu conteúdo com os Serviços de Mídia do Azure](../articles/media-services/latest/drm-content-protection-concept.md).

<sup>5</sup>As contas de armazenamento precisam ser da mesma assinatura do Azure.

<sup>6</sup>Há um limite de um milhão de políticas para diferentes políticas dos Serviços de Mídia. Um exemplo é para a política do localizador ou ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Se você sempre pode usar as mesmas permissões de acesso e de dias, use a mesma ID de política. Para obter informações e um exemplo, confira [Gerenciar ativos com o SDK do .NET dos Serviços de Mídia](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>O tamanho máximo com suporte para um único blob atualmente é de até 5 TB no Armazenamento de Blobs do Azure. Os limites adicionais aplicam-se nos Serviços de Mídia com base nos tamanhos de VM usados pelo serviço. O limite de tamanho aplica-se aos arquivos que você carrega e também aos arquivos gerados como resultado do processamento dos Serviços de Mídia (codificação ou análise). Se o arquivo de origem for maior que 260 GB, o trabalho provavelmente falhará. 

A tabela a seguir mostra os limites nas unidades reservada para mídia S1, S2 e S3. Se o arquivo de origem for maior do que os limites definidos na tabela, o trabalho de codificação falhará. Se você codificar fontes de resolução 4K de longa duração, deverá usar unidades reservadas para mídia S3 para alcançar o desempenho necessário. Se você tiver um conteúdo em 4K maior do que o limite de 260 GB nas unidades reservadas para mídia S3, abra um tíquete de suporte.

|Tipo de unidade reservada para mídia    |Tamanho máximo de entrada (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
