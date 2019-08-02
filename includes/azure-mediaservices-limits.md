---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: de3fd8dc0d45ea10e64af8e2258682a9e98639dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172303"
---
>[!NOTE]
>Para obter recursos que não são fixos, abra um tíquete de suporte para solicitar um aumento nas cotas. Não crie contas de serviços de mídia do Azure adicionais em uma tentativa de obter limites mais altos.

| Resource | Limite padrão | 
| --- | --- | 
| Contas de serviços de mídia do Azure em uma única assinatura | 25 (fixo) |
| Unidades por conta dos serviços de mídia reservadas de mídia |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Trabalhos por conta dos Serviços de Mídia | 50.000<sup>2</sup> |
| Tarefas encadeadas por trabalho | 30 (fixo) |
| Ativos por conta dos serviços de mídia | 1\.000.000|
| Ativos por tarefa | 50 |
| Ativos por trabalho | 100 |
| Localizadores exclusivos associados a um ativo simultaneamente | 5<sup>4</sup> |
| Canais ao vivo por conta dos serviços de mídia |5|
| Programas no estado interrompido por canal |50|
| Programa em estado de execução por canal |3|
| Streaming de pontos de extremidade que são interrompidos ou em execução por conta dos serviços de mídia|2|
| Unidades de streaming por ponto de extremidade de streaming |10 |
| Contas de armazenamento | 1,000<sup>5</sup> (fixo) |
| Políticas | 1,000,000<sup>6</sup> |
| Tamanho do arquivo| Em alguns cenários, há um limite no tamanho máximo do arquivo com suporte para processamento nos serviços de mídia. <sup>7</sup> |

<sup>1</sup>se você alterar o tipo, por exemplo, da S2 para S1, os limites máximo de unidade reservada são redefinidos.

<sup>2</sup>esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. Você pode excluir trabalhos antigos usando **Ijob** ou o **excluir** solicitação HTTP.

A partir de 1 de abril de 2017, qualquer registro de trabalho em sua conta com mais de 90 dias é excluído automaticamente, junto com seus registros de tarefas associada. A exclusão automática ocorrerá mesmo que o número total de registros esteja abaixo da cota máxima. Para arquivar as informações de trabalho e tarefa, use o código descrito [gerenciar ativos com o SDK do .NET de serviços de mídia](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>quando você faz uma solicitação para listar entidades de trabalho, um máximo de 1.000 trabalhos é retornado por solicitação. Para manter o controle de todos os trabalhos enviados, usar a parte superior ou ignorar consultas conforme descrito em [opções de consulta OData sistema](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>localizadores não foram projetados para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções DRM (gerenciamento) de direitos digitais. Para obter mais informações, consulte [proteger o conteúdo com os serviços de mídia do Azure](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup>as contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>6</sup>há um limite de 1.000.000 políticas para diferentes políticas dos serviços de mídia. Um exemplo é para a política de localizador ou ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Se você sempre use os mesmos dias e permissões de acesso, use a mesma ID de política. Para obter informações e um exemplo, consulte [gerenciar ativos com o SDK do .NET de serviços de mídia](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>o tamanho máximo com suporte para um único blob atualmente é até 5 TB de armazenamento de BLOBs do Azure. Limites adicionais aplicam-se os serviços de mídia com base em tamanhos de VM que são usados pelo serviço. O limite de tamanho aplica-se aos arquivos que você carrega e também os arquivos que obterem gerados como resultado do processamento (codificação ou analisando) os serviços de mídia. Se o arquivo de origem for maior que 260 GB, o trabalho provavelmente falhará. 

A tabela a seguir mostra os limites sobre o reservadas de mídia unidades S1, S2 e S3. Se seu arquivo de origem for maior do que os limites definidos na tabela, o trabalho de codificação falhará. Se você codificar a fontes de resolução de 4K de longa duração, você precisa usar unidades reservadas de mídia S3 para alcançar o desempenho necessário. Se você tiver o conteúdo de 4K maior que o limite de 260 GB das unidades reservadas de mídia de S3, entre em contato conosco em amshelp@microsoft.com para possíveis mitigações dar suporte ao seu cenário.

|Tipo de unidade reservada de mídia   |Tamanho máximo de entrada (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|
