---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d3d4679703f6d98cb2062144cfde7d11fe44130c
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386805"
---
## <a name="application-performance-indicators"></a>Indicadores de desempenho de aplicativo

Avaliamos o desempenho de um aplicativo como bom ou ruim usando indicadores de desempenho como estes: rapidez com que um aplicativo está processando uma solicitação de usuário, volume de dados que um aplicativo está processando por solicitação, quantidade de solicitações que um aplicativo está processando em um intervalo específico, quanto tempo um usuário precisa aguardar para obter uma resposta depois de enviar uma solicitação. Os termos técnicos para esses indicadores de desempenho são IOPS, Taxa de Transferência ou Largura de Banda e Latência.

Nesta seção, discutiremos os indicadores comuns de desempenho no contexto do Armazenamento Premium. Na seção a seguir, Coletando os requisitos de aplicativo, você aprenderá como avaliar esses indicadores de desempenho para seu aplicativo. Mais adiante, em Otimizando o desempenho do aplicativo, você saberá mais sobre os fatores que afetam esses indicadores de desempenho e as recomendações para otimizá-los.

## <a name="iops"></a>IOPS

O IOPS ou Operações de Entrada/Saída por Segundo é o número de solicitações que seu aplicativo está enviando para os discos de armazenamento em um segundo. Uma operação de entrada/saída pode ser de leitura ou gravação, sequencial ou aleatória. Os aplicativos OLTP (Processamento de Transações Online), como um site varejista online, precisam processar muitas solicitações simultâneas de usuários instantaneamente. As solicitações do usuário são transações de banco de dados com inserções e atualizações intensas, que o aplicativo deve processar rapidamente. Desse modo, os aplicativos OLTP exigem IOPS bastante alta. Tais aplicativos tratam milhões de solicitações de E/S aleatórias e pequenas. Se você tiver um aplicativo desse tipo, será preciso projetar a infraestrutura do aplicativo para otimização de IOPS. Na seção posterior, *Otimizando o desempenho do aplicativo*, vamos abordar em detalhes os fatores que devem ser considerados para obter IOPS alta.

Quando você anexa um disco do armazenamento premium à sua VM de alta escala, o Azure provisiona um número garantido de IOPS de acordo com a especificação do disco. Por exemplo, um disco P50 provisiona 7500 IOPS. Cada tamanho de VM de alta escala também tem um limite específico de IOPS que ela pode manter. Por exemplo, uma VM GS5 Padrão tem um limite de 80.000 IOPS.

## <a name="throughput"></a>Taxa de transferência

A taxa de transferência ou largura de banda é o volume de dados que o aplicativo está enviando aos discos de armazenamento em um intervalo especificado. Se o aplicativo estiver executando operações de entrada/saída com tamanhos grandes de unidade de E/S, ele exigirá uma taxa de transferência alta. Os aplicativos de data warehouse tendem a emitir operações de alta verificação que acessam grandes partes de dados por vez e geralmente executam operações em massa. Em outras palavras, esses aplicativos exigem uma taxa de transferência mais alta. Caso você tenha um aplicativo desse tipo, será preciso projetar sua infraestrutura para otimização da taxa de transferência. Na próxima seção, abordaremos em detalhes os fatores que você deve ajustar para conseguir isso.

Quando você anexa um disco do armazenamento Premium a uma VM de alta escala, o Azure provisiona a taxa de transferência de acordo com a especificação do disco. Por exemplo, um disco P50 provisiona taxas de transferência de disco de 250 MB por segundo. Cada tamanho de VM de alta escala também tem um limite específico de taxa de transferência que ela pode manter. Por exemplo, a VM GS5 padrão tem uma Taxa de Transferência máxima de 2.000 MB por segundo.

Há uma relação entre a taxa de transferência e a IOPS, como mostrado na fórmula abaixo.

![Relação de IOPS e taxa de transferência](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

Portanto, é importante determinar os valores ideais de taxa de transferência e de IOPS que o aplicativo exige. Ao tentar otimizar um deles, o outro também é afetado. Em uma seção mais adiante, *Otimizando o desempenho do aplicativo*, abordaremos em mais detalhes como otimizar a IOPS e Taxa de Transferência.

## <a name="latency"></a>Latência

A Latência é o tempo que leva para um aplicativo receber uma única solicitação, enviá-la aos discos de armazenamento e enviar a resposta ao cliente. Essa é uma medida essencial do desempenho de um aplicativo, além de IOPS e da Taxa de Transferência. A Latência de um disco do Armazenamento Premium é o tempo que se leva para recuperar informações de uma solicitação e comunicá-la de volta ao aplicativo. O Armazenamento Premium fornece baixas latências consistentes. Discos Premium são projetados para fornecer latências de dígito único em milissegundos para a maioria das operações de E/S. Ao habilitar o cache do host ReadOnly nos discos do Armazenamento Premium, você obtém latência de leitura mais baixa. Abordaremos o Cache de Disco em mais detalhes na seção *Otimizando o desempenho do aplicativo*.

Quando você otimizar o aplicativo para obter IOPS e taxa de transferência mais altas, a latência do aplicativo também será afetada. Após ajustar o desempenho do aplicativo, sempre avalie a latência dele pare evitar comportamento inesperado de alta latência.

As operações de plano de controle a seguir em Managed Disks podem envolver a movimentação do disco de um local de armazenamento para outro. Isso é orquestrado por meio da cópia em segundo plano dos dados que podem levar várias horas para serem concluídos, normalmente menos de 24 horas, dependendo da quantidade de dados nos discos. Durante esse tempo, seu aplicativo pode ficar mais alto do que a latência de leitura usual, já que algumas leituras podem ser redirecionadas para o local original e podem levar mais tempo para serem concluídas. Não há nenhum impacto na latência de gravação durante esse período.

- Atualize o tipo de armazenamento.
- Desanexar e anexar um disco de uma VM para outra.
- Crie um disco gerenciado com base em um VHD.
- Crie um disco gerenciado com base em um instantâneo.
- Converter discos não gerenciados em discos gerenciados.

# <a name="performance-application-checklist-for-disks"></a>Lista de verificação de aplicativo de desempenho para discos

A primeira etapa na criação de aplicativos de alto desempenho executados no Armazenamento Premium do Azure é entender os requisitos de desempenho do aplicativo. Depois de entender os requisitos de desempenho, será possível otimizar o aplicativo para obter o desempenho ideal.

Na seção anterior, explicamos os indicadores comuns de desempenho: IOPS, taxa de transferência e latência. Você deve identificar quais desses indicadores de desempenho são essenciais para o aplicativo proporcionar a experiência desejada ao usuário. Por exemplo, a IOPS alta é mais importante para aplicativos OLTP que processam milhões de transações em um segundo. Já a Taxa de Transferência alta é essencial para aplicativos de data warehouse que processam grandes volumes de dados em um segundo. A Latência extremamente baixa é essencial para aplicativos em tempo real, como sites de streaming de vídeo ao vivo.

Em seguida, avalie os requisitos de desempenho máximo do aplicativo durante todo o seu ciclo de vida. Use a lista de verificação de exemplo como ponto de partida. Registre os requisitos de desempenho máximo durante os períodos de carga de trabalho normais, de pico e fora do horário de expediente. Ao identificar os requisitos para todos os níveis de cargas de trabalho, você poderá determinar o requisito de desempenho geral do aplicativo. Por exemplo, a carga de trabalho normal de um site de comércio eletrônico serão as transações feitas durante a maioria dos dias em um ano. A carga de trabalho de pico do site será composta das transações atendidas no fim do ano ou em datas especiais. A carga de trabalho de pico geralmente ocorre por um tempo limitado, mas pode exigir que o aplicativo seja dimensionado para duas ou três vezes de sua operação normal. Descubra os requisitos de percentil 50, percentil 90 e percentil 99. Isso ajuda a filtrar as exceções nos requisitos de desempenho e você pode concentrar seus esforços na otimização dos valores certos.

## <a name="application-performance-requirements-checklist"></a>Lista de verificação dos requisitos de desempenho do aplicativo

| **Requisitos de desempenho** | **Percentil 50** | **Percentil 90** | **Percentil 99** |
| --- | --- | --- | --- |
| Máx. Transações por segundo | | | |
| % de operações de Leitura | | | |
| % de operações de Gravação | | | |
| % de operações Aleatórias | | | |
| % de operações Sequenciais | | | |
| Tamanho da solicitação de E/S | | | |
| Taxa de transferência média | | | |
| Máx. Taxa de transferência | | | |
| Mín. Latência | | | |
| Latência Média | | | |
| Máx. CPU | | | |
| CPU Média | | | |
| Máx. Memória | | | |
| Memória Média | | | |
| Profundidade da Fila | | | |

> [!NOTE]
> você deve considerar o dimensionamento desses números com base no futuro crescimento esperado do aplicativo. É uma boa ideia planejar o crescimento antecipadamente, pois pode ser mais difícil alterar a infraestrutura para melhorar o desempenho posteriormente.

Se você já tiver um aplicativo e deseja passar para o Armazenamento Premium, antes de qualquer coisa, crie a lista de verificação acima para o aplicativo. Em seguida, crie um protótipo do aplicativo no Armazenamento Premium e projete o aplicativo com base nas diretrizes descritas em *Otimizando o desempenho do aplicativo* em uma seção posterior deste documento. O próximo artigo descreve as ferramentas podem ser usadas para entender as medições de desempenho.

### <a name="counters-to-measure-application-performance-requirements"></a>Contadores para avaliar requisitos de desempenho do aplicativo

A melhor maneira de avaliar os requisitos de desempenho do aplicativo é usando as ferramentas de monitoramento de desempenho fornecidas pelo sistema operacional do servidor. Você pode usar o PerfMon para Windows e iostat para Linux. Essas ferramentas capturam contadores correspondentes para cada medida explicada na seção acima. Você deve capturar os valores desses contadores quando seu aplicativo estiver executando suas cargas de trabalho normais, de pico e fora do horário comercial.

Os contadores do PerfMon estão disponíveis para processador, memória e cada disco lógico e físico do seu servidor. Quando você usa discos do Armazenamento Premium com uma VM, os contadores de disco físico são para cada disco do Armazenamento Premium e os contadores de disco lógico são para cada volume criado nos discos do Armazenamento Premium. É preciso capturar os valores dos discos que hospedam a carga de trabalho do aplicativo. Se houver um mapeamento de um para um entre os discos lógicos e físicos, você poderá consultar os contadores de disco físico, caso contrário, consulte os contadores de disco lógico. No Linux, o comando iostat gera um relatório de utilização de CPU e disco. O relatório de utilização de disco fornece estatísticas por dispositivo físico ou partição. Se você tiver um servidor de banco de dados com seu log e seus logs em discos separados, colete esses dados para ambos os discos. A tabela abaixo descreve os contadores para discos, processadores e memória:

| Contador | Descrição | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS ou Transações por segundo** |Número de solicitações de E/S emitidas para o disco de armazenamento por segundo. |Leituras de Disco/s <br> Gravações de Disco/s |tps <br> r/s <br> w/s |
| **Leituras e gravações de discos** |% de operações de Leitura e Gravação executadas no disco. |% de Tempo de Leitura de Disco <br> % de Tempo de Gravação de Disco |r/s <br> w/s |
| **Taxa de transferência** |Volume de dados lidos ou gravados no disco por segundo. |Bytes/s Lidos em Disco <br> Bytes Gravados no Disco/s |kB_read/s <br> kB_wrtn/s |
| **Latência** |Tempo total para concluir uma solicitação de E/S no disco. |Média por Segundo do Disco/Leitura <br> Média por segundo do disco/Gravação |await <br> svctm |
| **Tamanho de E/S** |O tamanho das solicitações de E/S emitidas para os discos de armazenamento. |Média de Bytes do Disco/Leitura <br> Média de Bytes do Disco/Gravação |avgrq-sz |
| **Profundidade da Fila** |Número de solicitações de E/S pendentes aguardando serem lidas ou gravadas no disco de armazenamento. |Comprimento da Fila do Disco Atual |avgqu-sz |
| **IOPS Máxima** |Quantidade de memória exigida para execução perfeita do aplicativo |% de Bytes Confirmados em Uso |Usar o vmstat |
| **IOPS Máxima** |Quantidade de CPU exigida para a execução perfeita do aplicativo |% do Tempo do Processador |%util |

Saiba mais sobre o [iostat](https://linux.die.net/man/1/iostat) e [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).



## <a name="optimize-application-performance"></a>Otimizar o desempenho do aplicativo

Os principais fatores que influenciam o desempenho de um aplicativo em execução no armazenamento Premium são a natureza das solicitações de e/s, tamanho da VM, tamanho do disco, número de discos, cache de disco, multithread e profundidade da fila. Você pode controlar alguns desses fatores com botões fornecidos pelo sistema. A maioria dos aplicativos talvez não apresente uma opção para alterar o tamanho de E/S e a profundidade da fila diretamente. Por exemplo, se você estiver usando o SQL Server, não será possível escolher a profundidade de fila e o tamanho de E/S. O SQL Server escolhe os valores ideais do tamanho de E/S e da profundidade da fila para obter o melhor desempenho. É importante compreender os efeitos de ambos os tipos de fator no desempenho do aplicativo para que você possa provisionar recursos apropriados que atendam às necessidades de desempenho.

Ao longo desta seção, consulte a lista de verificação de requisitos de aplicativo que você criou para identificar quanto você precisa para otimizar o desempenho do aplicativo. Com base nisso, você poderá determinar quais fatores dessa seção será preciso ajustar. Para ver os efeitos de cada fator no desempenho do aplicativo, execute os parâmetros de comparação na configuração do aplicativo. Consulte a seção de parâmetros de comparação no final deste artigo para obter as etapas para executar ferramentas comuns de benchmark em VMs Windows e Linux.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Otimizar o IOPS, a taxa de transferência e a latência em um relance

A tabela a seguir resume os fatores de desempenho e as etapas necessárias para otimizar o IOPS, a taxa de transferência e a latência. As seções a seguir deste resumo descreverão cada fator mais detalhadamente.

Para obter mais informações sobre tamanhos de VM e sobre o IOPS, taxa de transferência e latência disponível para cada tipo de VM, consulte [tamanhos de VM Linux](../articles/virtual-machines/linux/sizes.md) ou [tamanhos de VM Windows](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Taxa de transferência** | **Latência** |
| --- | --- | --- | --- |
| **Cenário de exemplo** |Aplicativo OLTP corporativo que exige taxa muito alta de transações por segundo. |Aplicativo de data warehouse corporativo que processa grandes volumes de dados. |Aplicativos quase em tempo real que exigem respostas instantâneas às solicitações de usuário, como jogos online. |
| Fatores de desempenho | &nbsp; | &nbsp; | &nbsp; |
| **Tamanho de E/S** |E/S menores geram IOPS mais altas. |E/S maiores geram Taxa de Transferência mais altas. | &nbsp;|
| **Tamanho da VM** |Use um tamanho de VM que ofereça IOPS maior que o requisito do seu aplicativo. |Use um tamanho de VM com limite de Taxa de Transferência maior que o requisito do seu aplicativo. |Use um tamanho de VM que ofereça limites de escala maiores que o requisito do seu aplicativo. |
| **Tamanho do disco** |Use um tamanho de disco que ofereça IOPS maior que o requisito de seu aplicativo. |Use um tamanho de disco com limite de Taxa de Transferência maior que o requisito do seu aplicativo. |Use um tamanho de disco que ofereça limites de escala maiores que o requisito do seu aplicativo. |
| **Limites de escala de VM e disco** |O limite de IOPS do tamanho da VM escolhido deve ser maior que o total de IOPS impulsionado pelos discos do Armazenamento Premium anexados a ela. |O limite de Taxa de Transferência do tamanho da VM escolhido deve ser maior que o total de Taxa de Transferência impulsionado pelos discos do Armazenamento Premium anexados a ela. |Os limites de escala do tamanho da VM escolhido devem ser maiores que o total de limites de escala de discos do Armazenamento Premium anexados. |
| **Cache de disco** |Habilite o Cache ReadOnly nos discos do Armazenamento Premium com operações pesadas de leitura para obter IOPS mais alta de leitura. | &nbsp; |Habilite o Cache ReadOnly nos discos do Armazenamento Premium com operações pesadas de leitura para obter latências de leitura bem baixas. |
| **Distribuição de disco** |Use vários discos e distribua-os em conjunto para obter um limite combinado mais alto de IOPS e Taxa de Transferência. O limite combinado por VM deve ser maior que os limites combinados de discos Premium anexados. | &nbsp; | &nbsp; |
| **Tamanho da distribuição** |Tamanho menor de distribuição para padrão pequeno de E/S aleatório visto em aplicativos OLTP. Por exemplo, use o tamanho de distribuição de 64 KB para SQL Server aplicativo OLTP. |Tamanho maior de distribuição para padrão grande de E/S sequencial visto em aplicativos de data warehouse. Por exemplo, use o tamanho de distribuição de 256 KB para SQL Server aplicativo de data warehouse. | &nbsp; |
| **Multithreading** |Use multithreading para enviar por push números maiores de solicitações ao Armazenamento Premium que levarão à IOPS e Taxa de Transferência mais altas. Por exemplo, no SQL Server, defina um valor MAXDOP alto para alocar mais CPUs para o SQL Server. | &nbsp; | &nbsp; |
| **Profundidade da Fila** |Profundidade da fila maior gera IOPS mais alta. |Uma Profundidade da Fila maior gera uma Taxa de Transferência mais alta. |Profundidade da fila menor gera latências mais baixas. |

## <a name="nature-of-io-requests"></a>Natureza das solicitações de E/S

Uma solicitação de E/S é uma unidade da operação de entrada/saída que seu aplicativo executará. Identificar a natureza das solicitações de E/S, aleatória ou sequencial, leitura ou gravação, grande ou pequena, ajudará você a determinar os requisitos de desempenho do aplicativo. É importante entender a natureza das solicitações de e/s para tomar as decisões certas ao projetar sua infraestrutura de aplicativo.

O tamanho de E/S é um dos fatores mais importantes. O tamanho de E/S é o tamanho da solicitação de operação de entrada/saída gerada pelo aplicativo. O tamanho de E/S tem um impacto significativo no desempenho, especificamente na IOPS e na largura de banda que o aplicativo é capaz de atingir. A fórmula a seguir mostra a relação entre IOPS, tamanho de e/s e largura de banda/taxa de transferência.  
    ![](media/premium-storage-performance/image1.png)

Alguns aplicativos permitem a você alterar o tamanho de E/S, enquanto outros aplicativos, não. Por exemplo, o SQL Server determina por si só o tamanho ideal de E/S e não fornece aos usuários nenhum botão para alterá-lo. Por outro lado, o Oracle oferece um parâmetro chamado [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) com o qual você pode configurar o tamanho da solicitação de E/S do banco de dados.

Se estiver usando um aplicativo que não permite alterar o tamanho de E/S, use as diretrizes neste artigo para otimizar o KPI de desempenho que é mais relevante para o aplicativo. Por exemplo,

* Um aplicativo OLTP gera milhões de solicitações de E/S pequenas e aleatórias. Para lidar com esses tipos de solicitações de e/s, você deve projetar a infraestrutura do aplicativo para obter um IOPS maior.  
* Um aplicativo de data warehouse gera solicitações de E/S grandes e sequenciais. Para lidar com esses tipos de solicitações de e/s, você deve projetar sua infraestrutura de aplicativo para obter maior largura de banda ou taxa de transferência.

Se estiver usando um aplicativo que permita alterar o tamanho de E/S, use esta regra prática para o tamanho de E/S além de outras diretrizes de desempenho:

* Tamanho menor de E/S para obter IOPS mais alta. Por exemplo, 8 KB para um aplicativo OLTP.  
* Tamanho maior de E/S para obter largura de banda/Taxa de Transferência mais alta. Por exemplo, 1024 KB para um aplicativo de data warehouse.

Veja um exemplo de como é possível calcular a IOPS e a Taxa de Transferência/largura de banda do seu aplicativo. Considere um aplicativo usando um disco P30. A IOPS e a Taxa de Transferência/largura de banda máximas que um disco P30 pode atingir é de 5.000 IOPS e 200 MB por segundo, respectivamente. Agora, se seu aplicativo exigir a IOPS máxima do disco P30 e você usar um tamanho de E/S menor, como 8 KB, a largura de banda resultante que você poderá obter será de 40 MB por segundo. No entanto, se seu aplicativo exigir a Taxa de Transferência/largura de banda máxima do disco P30 e você usar um tamanho de E/S maior, como 1024 KB, a IOPS resultante será menor, 200 IOPS. Sendo assim, ajuste o tamanho de E/S, de tal modo que ele atenda aos requisitos de IOPS e Taxa de Transferência/Largura de Banda do aplicativo. A tabela abaixo resume os diferentes tamanhos de E/S e a IOPS e a Taxa de Transferência correspondentes para um disco P30.

| Requisito de aplicativo | Tamanho de E/S | IOPS | Taxa de Transferência/Largura de Banda |
| --- | --- | --- | --- |
| IOPS Máx. |8 KB |5\.000 |40 MB por segundo |
| Taxa de Transferência Máxima |1024 KB |200 |200 MB por segundo |
| Taxa de Transferência Máxima + IOPS alta |64 KB |3\.200 |200 MB por segundo |
| IOPS máxima + Taxa de Transferência alta |32 KB |5\.000 |160 MB por segundo |

Para obter IOPS e Largura de Banda mais altas do que o valor máximo de um único disco do Armazenamento Premium, use vários discos premium distribuídos em conjunto. Por exemplo, distribua dois discos P30 para obter uma IOPS de 10.000 IOPS ou um combinado de Taxa de Transferência de 400 MB por segundo. Como explicado na próxima seção, você deve usar um tamanho de VM que ofereça suporte à IOPS e à Taxa de Transferência de disco combinado.

> [!NOTE]
> À medida que você aumenta a IOPS ou a Taxa de Transferência, a outra também aumenta. Fique dentro dos limites de Taxa de Transferência ou de IOPS do disco ou da VM ao aumentar uma das duas.

Para ver os efeitos do tamanho de E/S no desempenho do aplicativo, você pode executar ferramentas de parâmetros comparação na VM e nos discos. Crie várias execuções de teste e use diferentes tamanhos de E/S para cada execução a fim de ver o impacto. Consulte a seção de parâmetros de comparação no final deste artigo para obter mais detalhes.

## <a name="high-scale-vm-sizes"></a>Tamanhos de VM de alta escala

Quando você começa a criar um aplicativo, uma das primeiras tarefas é escolher uma VM para hospedar o aplicativo. O Armazenamento Premium surge com os tamanhos de VM de alta escala que podem executar aplicativos que exigem potência mais alta de computação e um alto desempenho de E/S do disco local. Essas VMs fornecem processadores mais rápidos, uma taxa mais alta de memória para núcleo e uma SSD (unidade de estado sólido) para o disco local. Exemplos de VMs de alta escala que dão suporte ao armazenamento Premium são as VMs da série DS, DSv2 e GS.

As VMs de alta escala estão disponíveis em tamanhos diferentes com um número diferente de núcleos de CPU, memória, sistema operacional e tamanho de disco temporário. Cada tamanho de VM também tem um número máximo de discos de dados que você pode anexar à VM. Sendo assim, o tamanho de VM escolhido afetará a quantidade de capacidade de armazenamento, processamento e memória disponível para o aplicativo. Ele também afeta o custo da computação e do armazenamento. Por exemplo, abaixo estão as especificações do maior tamanho de VM em uma série DS, uma DSv2 e uma GS:

| Tamanho da VM | Núcleos de CPU | Memória | Tamanhos de disco da VM | Máx. de discos de dados | Tamanho do cache | IOPS | Limites de E/S do cache da largura de banda |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |SO = 1023 GB <br> SSD local = 224 GB |32 |576 GB |50.000 IOPS <br> 512 MB por segundo |4\.000 IOPS e 33 MB por segundo |
| Standard_GS5 |32 |448 GB |SO = 1023 GB <br> SSD local = 896 GB |64 |4224 GB |80.000 IOPS <br> 2.000 MB por segundo |5\.000 IOPS e 50 MB por segundo |

Para exibir uma lista completa de todos os tamanhos de VM do Azure disponíveis, consulte [tamanhos de VM do Windows](../articles/virtual-machines/windows/sizes.md) ou [tamanhos de VM do Linux](../articles/virtual-machines/linux/sizes.md). Escolha um tamanho de VM que possa atender aos requisitos de desempenho de aplicativo desejados. Além disso, leve em consideração as seguintes considerações importantes ao escolher tamanhos de VM.

*Limites de Escala*  
Os limites máximos de IOPS por VM e por disco são diferentes e independentes um do outro. Verifique se o aplicativo está impulsionando a IOPS dentro dos limites da VM, bem como os discos premium anexados a ela. Caso contrário, o desempenho do aplicativo será limitado.

Por exemplo, suponha que o requisito de um aplicativo seja de 4.000 IOPS. Para chegar a isso, você provisiona um disco P30 em uma VM DS1. O disco P30 pode oferecer até 5.000 IOPS. No entanto, a VM DS1 é limitada a 3.200 IOPS. Consequentemente, o desempenho do aplicativo será restrito pelo limite da VM de 3.200 IOPS e haverá degradação do desempenho. Para evitar essa situação, escolha um tamanho de disco e VM que atenda aos requisitos do aplicativo.

*Custo da operação*  
Em muitos casos, é possível que o custo geral de operação usando o Armazenamento Premium seja menor do que usando o Armazenamento Padrão.

Por exemplo, considere um aplicativo que exija 16.000 IOPS. Para atingir esse desempenho, você precisará de uma\_VM IaaS do Azure padrão D14, que pode fornecer um máximo de IOPS de 16.000 usando discos de 1 TB de armazenamento padrão de 32. Cada disco de armazenamento Standard de 1 TB pode alcançar um máximo de 500 IOPS. O custo estimado dessa VM por mês será de US$ 1.570. O custo mensal de 32 discos de armazenamento padrão será de US$ 1.638. O custo mensal total estimado será de US$ 3.208.

No entanto, se você tiver hospedado o mesmo aplicativo no Armazenamento Premium, será preciso uma VM menor e menos discos de Armazenamento Premium, reduzindo, assim, o custo geral. Uma VM Standard\_DS13 pode atender ao requisito de 16.000 IOPS usando quatro discos P30. A VM DS13 tem um máximo de 25.600 IOPS e cada disco P30 tem um máximo de 5.000 IOPS. Em geral, essa configuração pode alcançar 5.000 x 4 = 20.000 IOPS. O custo estimado dessa VM por mês será de US$ 1.003. O custo mensal de quatro discos P30 de armazenamento premium será de US$ 544,34. O custo total mensal estimado será de US$ 1.544.

A tabela abaixo resume o detalhamento do custo desse cenário para Armazenamento Premium e Standard.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Custo de VM por mês** |US$ 1.570,58 (Standard\_D14) |US$ 1.003,66 (Standard\_DS13) |
| **Custo de discos por mês** |$1638.40 (32 x 1 TB de discos) |US$ 544,34 (4 discos x P30) |
| **Custo geral por mês** |US$ 3.208,98 |US$ 1.544,34 |

*Distribuições do Linux*  

Com o Armazenamento Premium do Azure, você obtém o mesmo nível de Desempenho para VMs que executam Windows e Linux. Há suporte para vários tipos de distribuição Linux, e você pode ver a lista completa [aqui](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). É importante observar que as diferentes distribuições são mais adequadas para tipos diferentes de carga de trabalho. Você verá diferentes níveis de desempenho dependendo da distribuição em que a carga de trabalho está sendo executada. Teste as distribuições Linux com seu aplicativo e escolha a mais adequada.

Ao executar Linux com Armazenamento Premium, verifique as últimas atualizações dos drivers necessários para garantir alto desempenho.

## <a name="premium-storage-disk-sizes"></a>Tamanhos de disco do armazenamento Premium

O armazenamento Premium do Azure oferece oito tamanhos de disco de GA e três tamanhos de disco que estão na versão prévia, no momento. Cada tamanho de disco tem um limite de escala diferente para IOPS, largura de banda e armazenamento. Escolha o tamanho certo do disco do Armazenamento Premium de acordo com os requisitos do aplicativo e o tamanho da VM de alta escala. A tabela a seguir mostra os 11 tamanhos de discos e seus recursos. Atualmente, os tamanhos de disco 4, P6, P15, P60, P70, e P80 têm suporte apenas para o Managed Disks.

| Tipo de discos premium  | P4    | P6    | P10   | P15 | P20   | P30   | P40   | P50   | P60   | P70   | P80   |
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 32 GiB | 64 GiB | 128 GiB| 256 GiB| 512 GB            | 1,024 GiB (1 TiB)    | 2,048 GiB (2 TiB)    | 4,095 GiB (4 TiB)    | 8,192 GiB (8 TiB)    | 16,384 GiB (16 TiB)    | 32,767 GiB (32 TiB)    |
| IOPS por disco       | 120   | 240   | 500   | 1100 | 2\.300              | 5\.000              | 7500              | 7500              | 12.500              | 15.000              | 20.000              |
| Taxa de transferência por disco | 25 MiB por segundo  | 50 MiB por segundo  | 100 MiB por segundo |125 MiB por segundo | 150 MiB por segundo | 200 MiB por segundo | 250 MiB por segundo | 250 MiB por segundo | 480 MiB por segundo | 750 MiB por segundo | 750 MiB por segundo |

Quantos discos você escolhe depende do tamanho do disco escolhido. Você pode usar um único disco P50 ou vários discos P10 para atender aos requisitos do aplicativo. Leve em conta as considerações listadas abaixo ao fazer sua escolha.

*Limites de Escala (IOPS e Taxa de Transferência)*  
Os limites de IOPS e Taxa de Transferência de cada tamanho de disco Premium são diferentes e independentes dos limites de escala da VM. Verifique se o total de IOPS e Taxa de Transferência dos discos estão dentro dos limites de escala do tamanho escolhido de VM.

Por exemplo, se o requisito de um aplicativo for de, no máximo, 250 MB/s de Taxa de Transferência e você estiver usando uma VM DS4 com um único disco P30. A VM DS4 pode fornecer uma Taxa de Transferência de 256 MB/s. No entanto, um único disco P30 tem o Limite de taxa de transferência de 200 MB/s. Consequentemente, o aplicativo será limitado a 200 MB/s, devido ao limite do disco. Para superar esse limite, provisione mais de um disco de dados à VM ou redimensione seus discos para P40 ou P50.

> [!NOTE]
> as leituras atendidas pelo cache não estão incluídas na IOPS e Taxa de Transferência do disco, portanto, não estão sujeitas aos limites de disco. O cache tem seu limite de IOPS e Taxa de Transferência separado por VM.
>
> Por exemplo, inicialmente as leituras e gravações são de 60 MB/s e 40 MB/s, respectivamente. Ao longo do tempo, o cache aquece e atende cada vez mais leituras no cache. Assim, você pode obter Taxa de Transferência de gravação mais alta do disco.

*Número de discos*  
Determine o número de discos que você precisará ao avaliar os requisitos de aplicativo. Cada tamanho de VM também tem um limite de número de discos que você pode anexar à VM. Normalmente, é duas vezes o número de núcleos. Verifique se o tamanho de VM que você escolheu pode oferecer suporte ao número de discos necessário.

Lembre-se de que os discos do Armazenamento Premium têm recursos de desempenho mais alto comparados aos discos do Armazenamento Padrão. Portanto, se estiver migrando seu aplicativo da VM de IaaS do Azure usando Armazenamento Padrão para Armazenamento Premium, provavelmente você precisará de poucos discos premium para atingir o mesmo desempenho ou mais alto para seu aplicativo.

## <a name="disk-caching"></a>Cache de disco

As VMs de alta escala que aproveitam o Armazenamento Premium do Azure têm uma tecnologia de cache de várias camadas chamada BlobCache. O BlobCache usa uma combinação de RAM da máquina Virtual e SSD local para cache. Esse cache está disponível para os discos persistentes do Armazenamento Premium e os discos locais da VM. Por padrão, essa configuração de cache é definida como Leitura/Gravação para discos do SO e ReadOnly para discos de dados hospedados no Armazenamento Premium. Com o cache de disco habilitado nos discos do Armazenamento Premium, as VMs de alta escala podem atingir níveis extremamente altos de desempenho que excedem o desempenho do disco subjacente.

> [!WARNING]
> O cache de disco não tem suporte para discos 4 TiB e maiores. Se vários discos estiverem anexados à sua VM, cada disco com menos de 4 TiB dará suporte a cache.
>
> Alterar a configuração de cache de um disco do Azure desanexa e anexa novamente o disco de destino. Se for o disco do sistema operacional, a VM será reiniciada. Pare todos os aplicativos/serviços que podem ser afetados por essa interrupção antes de alterar a configuração de cache do disco.

Para saber mais sobre como o BlobCache funciona, consulte a postagem do blog interno [Armazenamento Premium do Azure](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

É importante habilitar o cache no conjunto certo de discos. Se você deve habilitar o cache de disco em um disco premium ou não dependerá do padrão da carga de trabalho que o disco manipulará. A tabela abaixo mostra as configurações padrão de cache para sistema operacional e discos de dados.

| **Tipo de disco** | **Configuração padrão de cache** |
| --- | --- |
| Disco de SO |Leitura/Gravação |
| Disco de dados |ReadOnly |

Veja a seguir as configurações recomendadas de cache de disco para discos de dados:

| **Configuração do cache de disco** | **recomendação sobre quando usar essa configuração** |
| --- | --- |
| Nenhum |Configure o cache do host como None para discos de gravação pesada e somente gravação. |
| ReadOnly |Configure o cache do host como ReadOnly para discos de leitura/gravação e somente leitura. |
| ReadWrite |Configure o cache do host como ReadWrite somente se o aplicativo tratar adequadamente a gravação de dados em cache em discos persistentes quando necessário. |

*ReadOnly*  
Ao configurar o cache ReadOnly em discos de dados do Armazenamento Premium, você pode obter baixa latência de leitura e IOPS de leitura e Taxa de Transferência muito altas para seu aplicativo. Isso se deve a dois motivos:

1. As leituras executadas no cache, que está na memória da VM e na SSD local, são muito mais rápidas do que as leituras no disco de dados, que está no armazenamento blob do Azure.  
1. O Armazenamento Premium não conta as leituras atendidas no cache para IOPS e Taxa de Transferência do disco. Portanto, o aplicativo é capaz de atingir um total mais alto de IOPS e Taxa de Transferência.

*ReadWrite*  
Por padrão, os discos do sistema operacional têm o cache ReadWrite habilitado. Recentemente, adicionamos suporte ao cache ReadWrite também nos discos de dados. Se estiver usando o cache ReadWrite, você deverá ter uma maneira adequada de gravar os dados do cache nos discos persistentes. Por exemplo, o SQL Server manipula a gravação de dados em cache nos discos de armazenamento persistentes por sua própria conta. Usar o cache ReadWrite com um aplicativo que não manipule a persistência dos dados necessários pode levar à perda de dados no caso de falha da VM.

Por exemplo, você pode aplicar essas diretrizes ao SQL Server em execução no Armazenamento Premium seguindo estes passos.

1. Configure o cache "ReadOnly" em discos do Armazenamento Premium que hospedam arquivos de dados.  
   a.  A leitura rápida no cache reduz o tempo de consulta do SQL Server, uma vez que as páginas de dados são recuperadas muito mais rapidamente do cache do que diretamente dos discos de dados.  
   b.  Atender às leituras no cache, significa que há Taxa de Transferência adicional disponível nos discos de dados premium. O SQL Server pode usar essa Taxa de Transferência adicional para recuperar mais páginas de dados e outras operações, como backup/restauração, cargas em lote e recriações de índice.  
1. Configure o cache "None" nos discos do Armazenamento Premium que hospedam os arquivos de log.  
   a.  Os arquivos de log têm basicamente operações pesadas de gravação. Sendo assim, eles não se beneficiam do cache ReadOnly.

## <a name="optimize-performance-on-linux-vms"></a>Otimizar o desempenho em VMs Linux

Para todos os SSDs Premium ou Discos Ultra com cache definido como **ReadOnly** ou **nenhum**, é necessário desabilitar "barreiras" ao montar o sistema de arquivos. Você não precisa de barreiras para esse cenário, pois as gravações em discos de Armazenamento Premium são duráveis para essas configurações de cache. Quando a solicitação de gravação for concluída, os dados terão sido gravados no armazenamento persistente. Para desabilitar "barreiras", use um dos seguintes métodos. Escolha o seguinte para o sistema de arquivos:
  
* Para **reiserFS**, para desabilitar as barreiras, use a opção de montagem `barrier=none`. (Para habilitar as barreiras, use `barrier=flush`.)
* Para **ext3/ext4**, para desabilitar as barreiras, use a opção de montagem `barrier=0`. (Para habilitar as barreiras, use `barrier=1`.)
* Para **XFS**, para desabilitar as barreiras, use a opção de montagem `nobarrier`. (Para habilitar as barreiras, use `barrier`.)
* Para discos de armazenamento premium com cache definido como **ReadWrite**, habilite as barreiras para durabilidade de gravação.
* Para que rótulos de volume persistam depois de reiniciar a máquina virtual, você deve atualizar /etc/fstab com as referências do identificador universalmente exclusivo (UUID) nos discos. Para obter mais informações, confira [Adicionar um disco gerenciado a uma VM Linux](../articles/virtual-machines/linux/add-disk.md).

As seguintes distribuições Linux foram validadas para SSDs Premium. Para obter melhor desempenho e estabilidade com SSDs Premium, recomendamos que você atualize suas VMs para uma dessas versões ou versões posteriores. 

Algumas das versões exigem um LIS (Serviços de Integração do Linux) v4.0 para Azure mais recente. Para baixar e instalar uma distribuição, siga o link listado na tabela a seguir. Podemos adicionar imagens à lista à medida que concluímos a validação. Nossas validações mostram que o desempenho varia para cada imagem. O desempenho depende da carga de trabalho e das configurações de imagem. Imagens diferentes são ajustadas para tipos diferentes de carga de trabalho.

| Distribuição | Versão | Kernel com suporte | Detalhes |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 obrigatório](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Confira a observação na próxima seção* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 recomendado](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Confira a observação na próxima seção* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 ou RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 ou RHCK c/[LIS 4.1+](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 ou RHCK c/[LIS 4.1+](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |

### <a name="lis-drivers-for-openlogic-centos"></a>Drivers LIS para Openlogic CentOS

Se você estiver executando VMs com o OpenLogic CentOS, execute o comando a seguir para instalar os drivers mais recentes:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Para ativar os novos drivers, reinicie a VM.

## <a name="disk-striping"></a>Distribuição de discos

Quando uma VM de alta escala é anexada aos vários discos persistentes do Armazenamento Premium, os discos podem ser divididos em conjunto para agregar a respectiva capacidade de armazenamento, IOPS e largura de banda.

No Windows, você pode usar Espaços de Armazenamento para dividir discos em conjunto. É preciso configurar uma coluna para cada disco em um pool. Caso contrário, o desempenho geral do volume distribuído poderá ser menor que o esperado devido a uma distribuição irregular do tráfego entre os discos.

Importante: Usando a interface de usuário do Gerenciador do Servidor UI, você pode definir o número total de colunas até 8 para um volume distribuído. Ao anexar mais de oito discos, use o PowerShell para criar o volume. Usando o PowerShell, é possível definir o número de colunas como igual ao número de discos. Por exemplo, se houver 16 discos em um único conjunto de distribuição; especifique 16 colunas no parâmetro *NumberOfColumns* do cmdlet *New-VirtualDisk* do PowerShell.

No Linux, use o utilitário MDADM para distribuir os discos em conjunto. Para ver etapas detalhadas sobre como distribuir discos no Linux, consulte [Configurar o Software RAID no Linux](../articles/virtual-machines/linux/configure-raid.md).

*Tamanho da distribuição*  
Uma configuração importante na distribuição de disco é o tamanho dela. O tamanho da distribuição ou tamanho do bloco é a menor parte de dados que o aplicativo pode incluir em um volume distribuído. O tamanho da distribuição que você configura depende do tipo de aplicativo e de seu padrão de solicitação. Se você escolher o tamanho de distribuição errado, isso pode levar ao alinhamento incorreto de E/S, o que leva à degradação de desempenho do aplicativo.

Por exemplo, se uma solicitação de E/S gerada pelo seu aplicativo for maior que o tamanho da distribuição do disco, o sistema de armazenamento a gravará entre limites de unidade de distribuição em mais de um disco. Quando for a hora de acessar esses dados, ele terá que procurar em mais de uma unidade de distribuição para concluir a solicitação. O efeito cumulativo de tal comportamento pode levar à degradação substancial de desempenho. Por outro lado, se o tamanho da solicitação de E/S for menor que o tamanho da distribuição e se ela for de natureza aleatória, as solicitações de E/S poderão ser adicionadas no mesmo disco, causando um gargalo e, por fim, a degradação do desempenho de E/S.

De acordo com o tipo de carga de trabalho que o aplicativo está executando, escolha um tamanho de distribuição apropriado. Para solicitações pequenas e aleatórias de E/S, use um tamanho de distribuição menor. Ao passo que para solicitações de e/s sequenciais grandes, use um tamanho de distribuição maior. Descubra as recomendações de tamanho de distribuição para o aplicativo que será executado no Armazenamento Premium. Para SQL Server, configure o tamanho de distribuição de 64 KB para cargas de trabalho OLTP e 256 KB para cargas de trabalho de data warehouse. Confira [Melhores práticas de desempenho para o SQL Server em VMs do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) para saber mais.

> [!NOTE]
> você pode usar a distribuição com um máximo de 32 discos do armazenamento premium em uma VM série DS e 64 discos do armazenamento premium em uma VM série GS.

## <a name="multi-threading"></a>Multithreading

O Azure projetou a plataforma Armazenamento Premium para ser extremamente paralela. Portanto, um aplicativo com multithread atinge desempenho muito mais alto que um aplicativo de único thread. Um aplicativo com multithread divide as tarefas entre vários threads e aumenta a eficiência de sua execução ao utilizar ao máximo os recursos de VM e disco.

Por exemplo, se o aplicativo estiver em execução em uma VM de único núcleo usando dois threads, a CPU poderá alternar entre os dois threads para obter eficiência. Enquanto um thread está aguardando em um disco que a E/S seja concluída, a CPU pode alternar para o outro thread. Dessa forma, dois threads podem fazer mais do que um único thread. Se a VM tiver mais de um núcleo, ela diminui ainda mais o tempo de execução, uma vez que cada núcleo pode executar tarefas paralelamente.

Você não poderá alterar a forma como um aplicativo pronto para uso implementa único thread ou multithreading. Por exemplo, o SQL Server é capaz de lidar com várias CPUs e vários núcleos. No entanto, o SQL Server decide sob quais condições ele utilizará um ou mais threads para processar uma consulta. Ele pode executar consultas e criar índices usando multithreading. Para uma consulta que envolva união de tabelas grandes e classificação de dados antes do retorno ao usuário, o SQL Server provavelmente usará vários threads. No entanto, um usuário não pode controlar se o SQL Server executará uma consulta usando um único thread ou vários threads.

Há definições de configuração que você pode alterar para influenciar esse multithreading ou processamento paralelo de um aplicativo. Por exemplo, no caso do SQL Server, ele é a configuração máxima do grau de paralelismo. Essa configuração chamada MAXDOP, permite que você configure o número máximo de processadores que o SQL Server pode usar no processamento paralelo. É possível configurar MAXDOP para consultas individuais ou operações de índice. Isso é benéfico quando você deseja equilibrar recursos do sistema para um aplicativo crítico de desempenho.

Por exemplo, digamos que seu aplicativo que usa SQL Server está executando uma consulta grande e uma operação de índice ao mesmo tempo. Vamos supor que você gostaria que a operação de índice tivesse um desempenho melhor do que a consulta grande. Nesse caso, você pode definir o valor MAXDOP da operação de índice para que seja maior que o valor MAXDOP da consulta. Dessa forma, o SQL Server tem maior número de processadores que pode aproveitar para a operação de índice em comparação ao número de processadores que pode dedicar à consulta grande. Lembre-se de que você não controla o número de threads que o SQL Server usará para cada operação. É possível controlar o número máximo de processadores que está sendo dedicado ao multithreading.

Saiba mais sobre [Graus de Paralelismo](https://technet.microsoft.com/library/ms188611.aspx) no SQL Server. Descubra as configurações que influenciam o multithreading em seu aplicativo e as respectivas configurações para otimizar o desempenho.

## <a name="queue-depth"></a>Profundidade da fila

A profundidade da fila ou o tamanho da fila ou do intervalo é o número de solicitações de e/s pendentes no sistema. O valor da profundidade da fila determina quantas operações de e/s o seu aplicativo pode alinhar, quais serão processadas pelos discos de armazenamento. Ele afeta todos os três indicadores de desempenho do aplicativo que discutimos neste artigo aula sobre visualização., IOPS, taxa de transferência e latência.

A profundidade da fila e o multithreading estão intimamente relacionados. O valor de profundidade da fila indica quanto multithreading pode ser obtido pelo aplicativo. Se a profundidade da fila for grande, o aplicativo poderá executar mais operações simultaneamente. Em outras  palavras, mais multithreading. Se a profundidade da fila for pequena, mesmo que o aplicativo esteja com multithread, ele não terá solicitações suficientes alinhadas para execução simultânea.

Normalmente, aplicativos prontos para uso não permitem que você altere a profundidade da fila, pois se definida incorretamente, fará mais mal do que bem. Os aplicativos definirão o valor correto da profundidade da fila para obter o desempenho ideal. No entanto, é importante compreender esse conceito para que você possa solucionar problemas de desempenho com seu aplicativo. Também é possível observar os efeitos da profundidade da fila executando ferramentas de parâmetros de comparação no sistema.

Alguns aplicativos fornecem configurações para influenciar a profundidade da fila. Por exemplo, a configuração MAXDOP (grau máximo de paralelismo) do SQL Server explicada na seção anterior. MAXDOP é uma forma de influenciar a profundidade da fila e o multithreading, embora isso não altere diretamente o valor da profundidade da fila do SQL Server.

*Profundidade de fila alta*  
Uma profundidade alta de fila alinha mais operações no disco. O disco sabe da próxima solicitação na fila antecipadamente. Consequentemente, o disco pode agendar operações com antecedência e processá-las em uma sequência ideal. Uma vez que o aplicativo está enviando mais solicitações ao disco, este pode processar mais E/S paralelamente. No fim, o aplicativo poderá atingir IOPS mais alta. Uma vez que o aplicativo está processando mais solicitações, a Taxa de Transferência total do aplicativo também aumenta.

Normalmente, um aplicativo pode atingir a taxa máxima de transferência com 8 a pouco mais de 16 E/S pendentes por disco anexado. Se uma profundidade de fila for uma, o aplicativo não está enviando o IOs suficiente para o sistema e processará menos valor em um determinado período. Em outras palavras, Taxa de Transferência menor.

Por exemplo, no SQL Server, configurar o valor MAXDOP de uma consulta para "4" informa ao SQL Server que ele pode usar até quatro núcleos para executar a consulta. O SQL Server determinará qual é o melhor valor de profundidade de fila e o número de núcleos para a execução da consulta.

*Profundidade de fila ideal*  
Um valor muito alto de profundidade de fila também tem suas desvantagens. Se o valor de profundidade da fila for muito alto, o aplicativo tentará impulsionar uma IOPS muito alta. A menos que o aplicativo tenha discos persistentes com provisão suficiente de IOPS, isso pode afetar negativamente as latências do aplicativo. A fórmula a seguir mostra a relação entre IOPS, latência e profundidade de fila.  
    ![](media/premium-storage-performance/image6.png)

Você não deve configurar a profundidade da fila para algum valor alto, mas para um valor ideal, o que pode fornecer IOPS suficiente ao aplicativo sem afetar as latências. Por exemplo, se a latência do aplicativo precisa ser de 1 milissegundo, a profundidade da fila necessária para alcançar 5.000 IOPS será, QD = 5000 x 0,001 = 5.

*Profundidade da fila para volume distribuído*  
Para um volume distribuído, mantenha uma profundidade de fila alta o suficiente, de tal forma que cada disco tenha uma profundidade de fila de pico individualmente. Por exemplo, considere um aplicativo que envia uma profundidade de fila de 2 e que há quatro discos na faixa. As duas solicitações de E/S vão para os dois discos e os dois discos restantes ficarão ociosos. Sendo assim, configure a profundidade da fila para que todos os discos possam estar ocupados. A fórmula abaixo mostra como determinar a profundidade da fila de volumes distribuídos.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Limitação

O Armazenamento Premium do Azure provisiona um número especificado de IOPS e Taxa de Transferência, dependendo dos tamanhos da VM e do disco que você escolhe. Sempre que o aplicativo tentar impulsionar IOPS ou Taxa de Transferência acima desses limites com os quais a VM ou o disco podem lidar, o Armazenamento Premium será restrito. Isso se manifesta na forma de degradação de desempenho do aplicativo. Isso pode significar maior latência, taxa de transferência mais baixa ou IOPS menor. Se o Armazenamento Premium não for restrito, o aplicativo poderá falhar completamente, excedendo o que seus recursos são capazes de alcançar. Portanto, para evitar problemas de desempenho devido à limitação, sempre provisione recursos suficientes ao aplicativo. Leve em consideração o que abordamos nas seções acima sobre tamanhos de disco e VM Os parâmetros de comparação são a melhor maneira de entender de quais recursos você precisará para hospedar o aplicativo.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os tipos de disco disponíveis:

* [Selecione um tipo de disco](../articles/virtual-machines/windows/disks-types.md)  

Para usuários do SQL Server, leia os artigos sobre Práticas recomendadas de desempenho para o SQL Server:

* [Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Armazenamento Premium do Azure fornece desempenho mais alto para SQL Server na VM do Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
