---
title: Metas de desempenho e escalabilidade do Arquivos do Azure
description: Saiba mais sobre as metas de desempenho e escalabilidade para Arquivos do Azure, incluindo a capacidade, taxa de solicitação e limites de largura de banda de entrada e saída.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6ef255d78d3dd3ff6fcc5eba7aad522018185299
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518888"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Metas de desempenho e escalabilidade do Arquivos do Azure
Os [arquivos do Azure](storage-files-introduction.md) oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem que são acessíveis por meio dos protocolos de sistema de arquivos SMB e NFS. Este artigo discute as metas de escalabilidade e desempenho dos arquivos do Azure e do Azure File Sync.

As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser afetadas por outras variáveis em sua implantação. Por exemplo, a taxa de transferência de um arquivo também pode ser limitada por sua largura de banda de rede disponível, não apenas pelos servidores que hospedam seus compartilhamentos de arquivos do Azure. É altamente recomendável testar seu padrão de uso para determinar se a escalabilidade e o desempenho do Arquivos do Azure atende às suas necessidades. É nosso compromisso aumentar esses limites ao longo do tempo. 

## <a name="azure-files-scale-targets"></a>Destinos de escala de Arquivos do Azure
Os compartilhamentos de arquivo do Azure são implantados em contas de armazenamento, que são objetos de nível superior que representam um pool de armazenamento compartilhado. Esse pool de armazenamento pode ser usado para implantar vários compartilhamentos de arquivos. Portanto, há três categorias a serem consideradas: contas de armazenamento, compartilhamentos de arquivos do Azure e arquivos.

### <a name="storage-account-scale-targets"></a>Destinos de escala da conta de armazenamento
O Azure dá suporte a vários tipos de contas de armazenamento para diferentes cenários de armazenamento que os clientes podem ter, mas há dois tipos principais de contas de armazenamento para arquivos do Azure. O tipo de conta de armazenamento que você precisa criar depende se você deseja criar um compartilhamento de arquivos padrão ou um compartilhamento de arquivos Premium: 

- **Contas de armazenamento GPv2 (uso geral versão 2)** : as contas de armazenamento GPv2 permitem que você implante compartilhamentos de arquivo do Azure em hardware padrão/baseado em HD (disco rígido). Além de armazenar compartilhamentos de arquivo do Azure, as contas de armazenamento GPv2 podem armazenar outros recursos de armazenamento, como contêineres de blob, filas ou tabelas. Os compartilhamentos de arquivos podem ser implantados nas camadas otimizadas para transação (padrão), quente ou fria.

- **Contas de armazenamento FileStorage**: as contas de armazenamento FileStorage permitem que você implante compartilhamentos de arquivo do Azure em hardware premium/baseado em SSD (disco de estado sólido). As contas FileStorage só podem ser usadas para armazenar compartilhamentos de arquivo do Azure; nenhum outro recurso de armazenamento (contêineres de blob, filas, tabelas etc.) pode ser implantado em uma conta FileStorage.

| Atributo | Contas de armazenamento GPv2 (Standard) | Contas de armazenamento de armazenamento de File(Premium) |
|-|-|-|
| Número de contas de armazenamento por regiõ por assinatura | 250 | 250 |
| Capacidade máxima da conta de armazenamento | 5 PiB<sup>1</sup> | 100 TiB (provisionado) |
| Número máximo de compartilhamentos de arquivos | Ilimitado | Ilimitado, o tamanho total provisionado de todos os compartilhamentos deve ser menor que o máximo que a capacidade da conta de armazenamento máxima |
| Taxa máxima de solicitações simultâneas | 20.000 IOPS<sup>1</sup> | 100.000 IOPS |
| Entrada máxima | <ul><li>EUA/Europa: 10 GBP/s<sup>1</sup></li><li>Outras regiões (LRS/ZRS): 10 libras/s<sup>1</sup></li><li>Outras regiões (GRS): 5 libras/s<sup>1</sup></li></ul> | 4.136 MiB/s |
| Saída máxima | 50 GBP/s<sup>1</sup> | 6.204 MiB/s |
| Número máximo de regras de rede virtual | 200 | 200 |
| Número máximo de regras de endereço IP | 200 | 200 |
| Operações de leitura de gerenciamento | 800 a cada 5 minutos | 800 a cada 5 minutos |
| Operações de gravação de gerenciamento | 10 por segundo/1200 por hora | 10 por segundo/1200 por hora |
| Operações da lista de gerenciamento | 100 a cada 5 minutos | 100 a cada 5 minutos |

<sup>1</sup> as contas de armazenamento de finalidade geral 2 dão suporte a limites de capacidade mais altos e limites mais altos para entrada por solicitação. Para solicitar um aumento nos limites de conta, contate o [Suporte do Azure](https://azure.microsoft.com/support/faq/).

### <a name="azure-file-share-scale-targets"></a>Destinos de escala de compartilhamento de arquivos do Azure
| Atributo | Compartilhamentos de arquivos padrão<sup>1</sup> | Compartilhamentos de arquivos Premium |
|-|-|-|
| Tamanho mínimo de um compartilhamento de arquivo | Nenhum mínimo | 100 GiB (provisionado) |
| Aumentar/diminuir unidade de tamanho provisionado | N/D | 1 GiB |
| Tamanho máximo de um compartilhamento de arquivo | <ul><li>100 TiB, com o recurso de compartilhamento de arquivos grandes habilitado<sup>2</sup></li><li>5 TiB, padrão</li></ul> | 100 TiB |
| Número máximo de arquivos em um compartilhamento de arquivo | Sem limite | Sem limite |
| Taxa de solicitação máxima (IOPS máxima) | <ul><li>10.000, com o recurso de compartilhamento de arquivos grandes habilitado<sup>2</sup></li><li>1.000 ou 100 solicitações por 100 ms, padrão</li></ul> | <ul><li>IOPS de linha de base: 400 + 1 IOPS por GiB, até 100.000</li><li>Intermitência de IOPS: Max (4000, 3x IOPS por GiB), até 100.000</li></ul> |
| Entrada máxima para um compartilhamento de arquivo | <ul><li>Até 300 MiB/s, com o recurso de compartilhamento de arquivos grandes habilitado<sup>2</sup></li><li>Até 60 MiB/s, padrão</li></ul> | 40 MiB/s + 0, 4 * GiB provisionados |
| Saída máxima para um compartilhamento de arquivo | <ul><li>Até 300 MiB/s, com o recurso de compartilhamento de arquivos grandes habilitado<sup>2</sup></li><li>Até 60 MiB/s, padrão</li></ul> | 60 MiB/s + 0, 6 * GiB provisionados |
| Número máximo de instantâneos de compartilhamento | 200 instantâneos | 200 instantâneos |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 2\.048 caracteres | 2\.048 caracteres |
| Componente de nome de caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |
| Limite de vínculo físico (somente NFS) | N/D | 178 |
| Número máximo de canais do SMB Multichannel | N/D | 4 |
| Número máximo de políticas de acesso armazenadas por compartilhamento de arquivo | 5 | 5 |

<sup>1</sup> os limites para compartilhamentos de arquivos padrão se aplicam a todas as três camadas disponíveis para compartilhamentos de arquivos padrão: transação otimizada, quente e fria.

<sup>2</sup> o padrão em compartilhamentos de arquivos padrão é 5 Tib, consulte [habilitar e criar grandes compartilhamentos de arquivos](./storage-files-how-to-create-large-file-share.md) para obter detalhes sobre como aumentar os compartilhamentos de arquivos padrão escalando verticalmente até 100 Tib.

### <a name="file-scale-targets"></a>Destinos de escala de arquivo
| Atributo | Arquivos em compartilhamentos de arquivos padrão  | Arquivos em compartilhamentos de arquivos Premium  |
|-|-|-|
| Tamanho máximo do arquivo | 4 TiB | 4 TiB |
| Taxa máxima de solicitações simultâneas | IOPS DE 1.000 | Até 8.000<sup>1</sup> |
| Entrada máxima para um arquivo | 60 MiB/s | 200 MiB/s (até 1 GiB/s com visualização multicanal de SMB)<sup>2</sup>|
| Egresso máxima de um arquivo | 60 MiB/s | 300 MiB/s (até 1 GiB/s com visualização multicanal de SMB)<sup>2</sup> |
| Máximo de identificadores simultâneos | identificadores 2.000 | identificadores 2.000  |

<sup>1 aplica-se ao Ios de leitura e gravação (normalmente, tamanhos de e/s menores menores ou iguais a 64 KiB). Operações de metadados, além de leituras e gravações, podem ser menores.</sup> 
 <sup>2 sujeito aos limites de rede da máquina, largura de banda disponível, tamanhos de e/s, profundidade da fila e outros fatores. Para obter detalhes, consulte [desempenho de Multichannel do SMB](./storage-files-smb-multichannel-performance.md).</sup>

## <a name="azure-file-sync-scale-targets"></a>Destinos de escala de Sincronização de Arquivos do Azure
A tabela a seguir indica os limites do teste da Microsoft e também indica quais destinos são limites rígidos:

| Recurso | Destino | Limite rígido |
|----------|--------------|------------|
| Serviços de Sincronização de Armazenamento por região | 100 Serviços de Sincronização de Armazenamento | Sim |
| Grupos de sincronização por Serviço de Sincronização de Armazenamento | 200 grupos de sincronização | Sim |
| Servidores registrados por Serviço de Sincronização de Armazenamento | 99 servidores | Sim |
| Pontos de extremidade na nuvem por grupo de sincronização | 1 ponto de extremidade de nuvem | Sim |
| Pontos de extremidade no servidor por grupo de sincronização | 100 pontos de extremidade de servidor | Sim |
| Pontos de extremidade de servidor por servidor | 30 pontos de extremidade de servidor | Sim |
| Objetos do sistema de arquivos (diretórios e arquivos) por grupo de sincronização | 100 milhões de objetos | Não |
| Número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório | 5 milhões de objetos | Sim |
| Tamanho máximo do descritor de segurança (diretórios e arquivos) do objeto | 64 KiB | Sim |
| Tamanho do arquivo | 100 GiB | Não |
| Tamanho mínimo do arquivo para que um arquivo seja colocado em camadas | V9 e mais recente: com base no tamanho do cluster do sistema de arquivos (tamanho duplo do cluster do sistema de arquivos). Por exemplo, se o tamanho do cluster do sistema de arquivos for 4 KiB, o tamanho mínimo do arquivo será de 8 KiB.<br> V8 e uma versão mais antiga: 64 KiB  | Sim |

> [!Note]  
> Um ponto de extremidade de Sincronização de Arquivos do Azure poderá aumentar o tamanho de um compartilhamento de arquivo do Azure. Caso o limite de tamanho do compartilhamento de arquivo do Azure seja atingido, a sincronização não será capaz de funcionar.

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

## <a name="see-also"></a>Consulte também
- [Como planejar uma implantação de Arquivos do Azure](storage-files-planning.md)
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)