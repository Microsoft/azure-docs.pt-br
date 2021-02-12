---
title: Metas de desempenho e escalabilidade do Arquivos do Azure
description: Saiba mais sobre as metas de desempenho e escalabilidade para Arquivos do Azure, incluindo a capacidade, taxa de solicitação e limites de largura de banda de entrada e saída.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e10f45af89e19f6fe62ff729f96d870e008c96ec
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611093"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Metas de desempenho e escalabilidade do Arquivos do Azure

O [Arquivos do Azure](storage-files-introduction.md) oferece compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do protocolo SMB padrão no setor. Este artigo discute as metas de escalabilidade e desempenho dos arquivos do Azure e do Azure File Sync.

As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser afetadas por outras variáveis em sua implantação. Por exemplo, a taxa de transferência para um arquivo pode também ser limitada pela sua largura de banda de rede disponível, não apenas os servidores que hospedam o serviço de Arquivos do Azure. É altamente recomendável testar seu padrão de uso para determinar se a escalabilidade e o desempenho do Arquivos do Azure atende às suas necessidades. É nosso compromisso aumentar esses limites ao longo do tempo. Não hesite em fazer comentários, na seção de comentários abaixo ou no [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files), sobre os limites que você gostaria que aumentássemos.

## <a name="azure-storage-account-scale-targets"></a>Metas de escalabilidade da conta de armazenamento do Azure

O recurso pai de um compartilhamento de arquivo do Azure é uma conta de armazenamento do Azure. Uma conta de armazenamento representa um pool de armazenamento do Azure que pode ser usado por vários serviços de armazenamento, incluindo Arquivos do Azure, para armazenar dados. Outros serviços que armazenam dados em contas de armazenamento são o armazenamento de Blobs do Azure, armazenamento de Filas do Azure e armazenamento de Tabelas do Azure. As metas a seguir se aplicam a todos os serviços de armazenamento que armazenam dados em uma conta de armazenamento:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> A utilização da conta de armazenamento de uso geral de outros serviços de armazenamento afeta os compartilhamentos de arquivos do Azure em sua conta de armazenamento. Por exemplo, se você atingir a capacidade máxima da conta de armazenamento com o armazenamento de Blobs do Azure, você não poderá criar novos arquivos em seu compartilhamento de arquivo do Azure, mesmo se o compartilhamento de arquivo do Azure estiver abaixo do tamanho máximo do compartilhamento.

## <a name="azure-files-scale-targets"></a>Destinos de escala de Arquivos do Azure

Há três categorias de limitações a serem consideradas para os arquivos do Azure: contas de armazenamento, compartilhamentos e arquivos.

Por exemplo: com compartilhamentos de arquivos premium, um único compartilhamento pode atingir 100.000 IOPS e um único arquivo pode ser dimensionado para até 5.000 IOPS. Portanto, se você tiver três arquivos em um único compartilhamento, o máximo de IOPS que você pode obter desse compartilhamento é 15.000.

### <a name="standard-storage-account-limits"></a>Limites de conta de armazenamento Standard

Consulte a seção [destinos de escala da conta de armazenamento do Azure](#azure-storage-account-scale-targets) para esses limites.

### <a name="premium-filestorage-account-limits"></a>Limites da conta de armazenamento Premium

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Os limites da conta de armazenamento são aplicados a todos os compartilhamentos. O dimensionamento até o máximo para contas de armazenamento de filebackup só será atingível se houver apenas um compartilhamento por conta de armazenamento de File.

### <a name="file-share-and-file-scale-targets"></a>Destinos de escala de arquivo e compartilhamento de arquivos

> [!NOTE]
> Compartilhamentos de arquivos padrão maiores que 5 TiB têm determinadas limitações. Para obter uma lista de limitações e instruções para habilitar tamanhos maiores de compartilhamento de arquivos, consulte a seção [habilitar compartilhamentos de arquivos maiores em compartilhamentos](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) de arquivos padrão do guia de planejamento.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Destinos de escala de Sincronização de Arquivos do Azure

A sincronização de Arquivos do Azure foi projetado com o objetivo de uso ilimitado, mas o uso ilimitado nem sempre é possível. A tabela a seguir indica os limites do teste da Microsoft e também indica quais destinos são limites rígidos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Métricas de desempenho de sincronização de arquivos do Azure

Como o agente do Azure File Sync é executado em um computador Windows Server que se conecta aos compartilhamentos de arquivos do Azure, o desempenho de sincronização efetivo depende de vários fatores em sua infraestrutura: Windows Server e a configuração de disco subjacente, largura de banda de rede entre o servidor e o Azure armazenamento, tamanho do arquivo, tamanho total do conjunto de dados e atividade no conjunto de dados. Desde que a sincronização de arquivos do Azure funciona no nível de arquivo, as características de desempenho de uma solução de sincronização de arquivos do Azure melhor é medido em número de objetos (arquivos e diretórios) processado por segundo.

Para sincronização de arquivos do Azure, o desempenho for crítico em dois estágios:

1. **O provisionamento inicial única**: para otimizar o desempenho no provisionamento inicial, consulte [integração com sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) para os detalhes de implantação ideal.
2. **Sincronização em andamento**: depois que os dados inicialmente são propagados em compartilhamentos de arquivos do Azure, a sincronização de arquivos do Azure mantém vários pontos de extremidade em sincronia.

Para ajudá-lo a planejar a implantação para cada um dos estágios, abaixo são os resultados observados durante o teste interno em um sistema com uma configuração

| Configuração do sistema | Detalhes |
|-|-|
| CPU | 64 núcleos virtuais com o cache de MiB L3 64 |
| Memória | 128 GiB |
| Disco | Cache de discos SAS com RAID 10 com bateria de reserva |
| Rede | Rede de 1 Gbps |
| Carga de trabalho | Servidor de arquivos de uso geral|

| Provisionamento inicial de uso único  | Detalhes |
|-|-|
| Número de objetos | 25 milhões de objetos |
| Tamanho do conjunto de dados| ~ 4,7 TiB |
| Tamanho médio de arquivo | ~ 200 KiB (maior arquivo: 100 GiB) |
| Enumeração de alteração de nuvem inicial | 20 objetos por segundo  |
| Carregue a taxa de transferência | 20 objetos por segundo por grupo de sincronização |
| Taxa de transferência de download de namespace | 400 objetos por segundo |

### <a name="initial-one-time-provisioning"></a>Provisionamento inicial de uso único

**Enumeração de alteração de nuvem inicial**: quando um novo grupo de sincronização é criado, a enumeração de alteração de nuvem inicial é a primeira etapa que será executada. Nesse processo, o sistema irá enumerar todos os itens no compartilhamento de arquivos do Azure. Durante esse processo, não haverá nenhuma atividade de sincronização, ou seja, nenhum item será baixado do ponto de extremidade de nuvem para o ponto de extremidade do servidor e nenhum item será carregado do ponto de extremidade do servidor para o ponto de extremidade da nuvem. A atividade de sincronização será retomada após a conclusão da enumeração de alteração de nuvem inicial.
A taxa de desempenho é de 20 objetos por segundo. Os clientes podem estimar o tempo necessário para concluir a enumeração inicial de alteração na nuvem determinando o número de itens no compartilhamento de nuvem e usando a fórmula a seguir para obter o tempo em dias. 

   **Tempo (em dias) para a enumeração de nuvem inicial = (número de objetos no ponto de extremidade de nuvem)/(20 * 60 * 60 * 24)**

**Taxa de transferência de download de namespace** Quando um novo ponto de extremidade do servidor é adicionado a um grupo de sincronização existente, o agente de Sincronização de Arquivos do Azure não baixa nenhum conteúdo do arquivo do ponto de extremidade da nuvem. Sincronizar primeiro namespace completo e, em seguida, os gatilhos em segundo plano Lembre-se de fazer o download dos arquivos, em sua totalidade ou, se camadas na nuvem está habilitado para a política de camadas de nuvem definido no ponto de extremidade do servidor.


| Sincronização contínua  | Detalhes  |
|-|--|
| Número de objetos sincronizados| 125.000 objetos (aproximadamente 1% rotatividade) |
| Tamanho do conjunto de dados| 50 GiB |
| Tamanho médio de arquivo | ~500 KiB |
| Carregue a taxa de transferência | 20 objetos por segundo por grupo de sincronização |
| Taxa de transferência do Download completo* | 60 objetos por segundo |

*Se nuvem camadas estiver habilitada, provavelmente a observar um desempenho melhor com apenas o arquivo de dados são baixados. Sincronização de arquivos do Azure fazer o download somente os dados de arquivos armazenados em cache quando forem alteradas em qualquer um dos pontos de extremidade. Para todos os arquivos em camadas ou recentemente criados, o agente não baixar os dados de arquivo e em vez disso, apenas o namespace para todos os pontos de extremidade do servidor é sincronizado. O agente também oferece suporte parciais downloads de arquivos em camadas como eles são acessados pelo usuário. 

> [!Note]  
> Os números acima não são uma indicação do desempenho que você terá. O desempenho real dependerá vários fatores conforme descrito no início desta seção.

Como um guia geral para sua implantação, você deve manter alguns pontos em mente:

- A taxa de transferência do objeto é dimensionado aproximadamente proporcionalmente ao número de grupos de sincronização no servidor. Dividir dados em vários grupos de sincronização em um servidor resulta em melhor taxa de transferência, que também é limitada pelo servidor e rede.
- A taxa de transferência do objeto é inversamente proporcional à MiB por segundo taxa de transferência. Para os arquivos menores, você terá maior taxa de transferência em termos de número de objetos processados por segundo, mas inferior MiB por segundo taxa de transferência. Por outro lado, para arquivos maiores, você terá menos objetos processados por segundo, mas superior MiB por segundo taxa de transferência. A MiB por segundo taxa de transferência é limitada pelos destinos de escala de arquivos do Azure.

## <a name="see-also"></a>Confira também

- [Como planejar uma implantação de Arquivos do Azure](storage-files-planning.md)
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
