---
title: Escolha Sincronização de Arquivos do Azure políticas de camadas de nuvem | Microsoft Docs
description: Detalhes sobre o que deve ter em mente ao escolher Sincronização de Arquivos do Azure políticas de camadas de nuvem.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/24/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2ed1b8162c49ccc26cb98dd02897a9c40f809d14
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204293"
---
# <a name="choose-cloud-tiering-policies"></a>Escolher políticas de camadas de nuvem

Este artigo fornece diretrizes para usuários que estão selecionando e ajustando suas políticas de camadas de nuvem. Antes de ler este artigo, verifique se você entendeu como a camada de nuvem funciona. Para conceitos básicos de camadas de nuvem, consulte [entender sincronização de arquivos do Azure camadas de nuvem](storage-sync-cloud-tiering-overview.md). Para obter uma explicação detalhada das políticas de camadas de nuvem com exemplos, consulte [sincronização de arquivos do Azure políticas de camadas de nuvem](storage-sync-cloud-tiering-policy.md).

## <a name="limitations"></a>Limitações
- Não há suporte para a disposição em camadas na nuvem no volume do sistema do Windows.

- Você ainda poderá habilitar a disposição em camadas da nuvem se tiver uma cota FSRM no nível do volume. Depois que uma cota FSRM é definida, as APIs de consulta de espaço livre que são chamadas relatam automaticamente o espaço livre no volume de acordo com a configuração de cota. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Tamanho mínimo de arquivo para um arquivo para a camada

Para o Agent versões 9 e mais recentes, o tamanho mínimo do arquivo para um arquivo para camada é baseado no tamanho do cluster do sistema de arquivos. O tamanho mínimo de arquivo qualificado para a camada de nuvem é calculado por 2x o tamanho do cluster e, no mínimo, 8 KB. A tabela a seguir ilustra os tamanhos mínimos de arquivo que podem ser em camadas, com base no tamanho do cluster de volume:

|Tamanho do cluster de volume (bytes) |Arquivos desse tamanho ou maiores podem ser em camadas  |
|----------------------------|---------|
|4 KB ou menor (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536)    | 128 KB  |

Os tamanhos de cluster de até 64 KB têm suporte no momento, mas, para tamanhos maiores, a camada de nuvem não funciona.

Todos os sistemas de arquivos usados pelo Windows, organizam o disco rígido com base no tamanho do cluster (também conhecido como tamanho da unidade de alocação). O tamanho do cluster representa a menor quantidade de espaço em disco que pode ser usada para manter um arquivo. Quando os tamanhos de arquivo não chegam a um múltiplo par do tamanho do cluster, o espaço adicional deve ser usado para manter o arquivo até o próximo múltiplo do tamanho do cluster.

Sincronização de Arquivos do Azure tem suporte em volumes NTFS com o Windows Server 2012 R2 e mais recente. A tabela a seguir descreve os tamanhos de cluster padrão quando você cria um novo volume NTFS com o Windows Server 2019.

|Tamanho do volume    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB – 16 TB   | 4 KB                |
|16TB – 32 TB   | 8 KB                |
|32 TB – 64 TB   | 16 KB               |
|64 TB – 128 TB  | 32 KB               |
|128TB – 256 TB | 64 KB (máximo anterior) |
|256 TB – 512 TB| 128 KB              |
|512 TB – 1 PB  | 256 KB              |
|1 PB – 2 PB    | 512 KB              |
|2 TB – 4 PB    | 1024 KB             |
|4 TB – 8 TB    | 2048 KB (tamanho máximo)  |
|> 8 TB         | sem suporte       |

É possível que, durante a criação do volume, você tenha formatado manualmente o volume com um tamanho de cluster diferente. Se o seu volume se originar de uma versão mais antiga do Windows, os tamanhos de cluster padrão também poderão ser diferentes. [Este artigo tem mais detalhes sobre os tamanhos de cluster padrão.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Mesmo que você escolha um tamanho de cluster menor que 4 KB, um limite de 8 KB como o menor tamanho de arquivo que pode ser em camadas, ainda se aplica. (Mesmo que o tamanho do cluster tecnicamente 2x corresponda a menos de 8 KB.)

O motivo para o mínimo absoluto é encontrado na maneira como o NTFS armazena arquivos extremamente pequenos-1 KB a 4 KB de arquivos de tamanho. Dependendo de outros parâmetros do volume, é possível que arquivos pequenos não sejam armazenados em um cluster no disco. Talvez seja mais eficiente armazenar esses arquivos diretamente na tabela de arquivos mestre do volume ou no "registro de MFT". O ponto de nova análise em camadas de nuvem sempre é armazenado em disco e ocupa exatamente um cluster. A hierarquização desses arquivos pequenos poderia acabar sem economia de espaço. Os casos extremos poderiam até acabar usando mais espaço com a camada de nuvem habilitada. Para proteger contra isso, o menor tamanho de um arquivo que a camada de nuvem irá criará, é de 8 KB em um tamanho de cluster de 4 KB ou menor. 

## <a name="selecting-your-initial-policies"></a>Selecionando as políticas iniciais

Geralmente, quando você habilita a camada de nuvem em um ponto de extremidade do servidor, você deve criar uma unidade virtual local para cada ponto de extremidade do servidor individual. Isolar o ponto de extremidade do servidor torna mais fácil entender como a camada de nuvem funciona e ajustar suas políticas de acordo. No entanto, Sincronização de Arquivos do Azure funciona mesmo que você tenha vários pontos de extremidade do servidor na mesma unidade, para obter detalhes, consulte a seção [vários pontos de extremidade do servidor no volume local](storage-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) . Também recomendamos que, ao habilitar a camada de nuvem pela primeira vez, você mantenha a política de data desabilitada e a política de espaço livre em volume em cerca de 10% a 20%. Para a maioria dos volumes de servidor de arquivos, o espaço livre do volume de 20% geralmente é a melhor opção.

Para simplificar e ter uma compreensão clara de como os itens serão em camadas, recomendamos que você ajuste principalmente a política de espaço livre do volume e mantenha sua política de data desabilitada, a menos que seja necessário. Recomendamos isso porque a maioria dos clientes acha valioso para preencher o cache local com o máximo possível de arquivos ativos e colocar em camadas o restante para a nuvem. No entanto, a política de data pode ser benéfica se você quiser liberar proativamente o espaço em disco local e souber que os arquivos nesse ponto de extremidade do servidor acessados após o número de dias especificado na sua política de data não precisam ser mantidos localmente. Definir a política de data libera a capacidade de disco local valiosa para outros pontos de extremidade no mesmo volume para armazenar em cache mais de seus arquivos.

Depois de definir suas políticas, monitore a saída e ajuste as duas políticas adequadamente. É recomendável examinar especificamente o **tamanho de recall de camadas de nuvem** e o tamanho de **recall em camadas de nuvem por** métricas de aplicativo em Azure monitor. Para saber como monitorar a saída, consulte [monitorar camadas de nuvem](storage-sync-monitor-cloud-tiering.md).

## <a name="adjusting-your-policies"></a>Ajustando suas políticas

Se a quantidade de arquivos recuperados constantemente do Azure for maior do que o desejado, você poderá ter mais arquivos ativos do que você tem espaço para salvá-los no volume do servidor local. Aumente o tamanho do volume local, se possível, e/ou diminua a porcentagem de política de espaço livre do volume em pequenos incrementos. A diminuição da porcentagem de espaço livre do volume também pode ter consequências negativas. A rotatividade mais alta em seu conjunto de informações requer mais espaço livre – para novos arquivos e recall de arquivos "frios". A disposição em camadas é acionada com um atraso de até uma hora e, em seguida, precisa de tempo de processamento, motivo pelo qual você sempre deve ter bastante espaço livre em seu volume.

Manter mais dados locais significa custos de egresso menores, pois menos arquivos serão rechamados do Azure, mas também exigirão uma quantidade maior de armazenamento local que é obtida por seu próprio custo. 

Ao ajustar a política de espaço livre do volume, a quantidade de dados que você deve manter local é determinada pelos seguintes fatores: a largura de banda, o padrão de acesso do conjunto e o orçamento. Com uma conexão de baixa largura de banda, talvez você queira mais dados locais, para garantir um atraso mínimo para os usuários. Caso contrário, você pode baseá-lo na taxa de rotatividade durante um determinado período. Por exemplo, se você souber que 10% de seu conjunto de acordo de 1 TB é alterado ou acessado ativamente por mês, convém manter o local de 100 GB para que você não esteja rechamando arquivos com frequência. Se o volume for de 2 TB, você desejaria manter o local de 5% (ou 100 GB), o que significa que a porcentagem restante é de 95% de espaço livre no volume. No entanto, você deve adicionar um buffer para períodos de variação mais alta – em outras palavras, começando com uma porcentagem maior de espaço livre de volume e, em seguida, ajustando-o se necessário posteriormente.

## <a name="standard-operating-procedures"></a>Procedimentos operacionais padrão

- Ao migrar pela primeira vez para os arquivos do Azure via Sincronização de Arquivos do Azure, a camada de nuvem depende do carregamento inicial
- A camada de nuvem verifica a conformidade com as políticas de espaço livre e de data do volume a cada 60 minutos
- Usar a opção/LFSM no Robocopy ao migrar arquivos permitirá que os arquivos sejam sincronizados e a camada de nuvem para criar espaço durante o carregamento inicial 
- Se a camada ocorrer antes de uma calor ser formada, os arquivos serão em camadas pelo carimbo de data/hora da última modificação

## <a name="next-steps"></a>Próximas etapas
* [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
