---
title: Latência no Armazenamento de Blobs – Armazenamento do Microsoft Azure
description: Entenda e meça a latência para operações do Armazenamento de Blobs e saiba como projetar seus aplicativos do Armazenamento de Blobs para baixa latência.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75749716"
---
# <a name="latency-in-blob-storage"></a>Latência no armazenamento de blobs

A latência, às vezes chamada de tempo de resposta, é a quantidade de tempo que um aplicativo deve aguardar até que uma solicitação seja concluída. A latência pode afetar diretamente o desempenho de um aplicativo. A baixa latência costuma ser importante para cenários que envolvem seres humanos, como a condução de transações de cartão de crédito ou o carregamento de páginas da Web. Sistemas que precisam processar eventos de entrada com altas taxas, como logs de telemetria ou eventos de IoT, também exigem baixa latência. Este artigo descreve como entender e medir a latência das operações em blobs de blocos e como projetar seus aplicativos para baixa latência.

O Armazenamento do Azure oferece duas opções de desempenho diferentes para blobs de blocos: Premium e Standard. Os blobs de blocos Premium oferecem latência significativamente menor e mais consistente do que os blobs de blocos Standard por meio de discos SSD de alto desempenho. Para saber mais, confira **Armazenamento de Blobs de blocos com desempenho Premium** em [Armazenamento de Blobs do Azure: camadas de acesso frequente, esporádico e de arquivos](storage-blob-storage-tiers.md).

## <a name="about-azure-storage-latency"></a>Sobre a latência do Armazenamento do Azure

A latência de Armazenamento do Azure está relacionada a taxas de solicitação para operações de armazenamento do Azure. As taxas de solicitação também são conhecidas como IOPS (operações de entrada/saída por segundo).

Para calcular a taxa de solicitação, primeiro determine o período que cada solicitação leva para ser concluída e, em seguida, calcule quantas solicitações podem ser processadas por segundo. Por exemplo, suponha que uma solicitação leva de 50 milissegundos (MS) para ser concluída. Um aplicativo que usa um thread com uma operação pendente de leitura ou gravação deve atingir 20 IOPS (1 segundo ou 1000 MS/50 ms por solicitação). Teoricamente, se a contagem de threads dobrar, o aplicativo deverá ser capaz de alcançar 40 IOPS. Se as operações de leitura ou gravação assíncronas pendentes para cada thread forem duplicadas, o aplicativo deverá ser capaz de alcançar 80 IOPS.

Na prática, as taxas de solicitação nem sempre são dimensionadas de forma linear, devido à sobrecarga no cliente gerada pelo agendamento de tarefas, pela alternância de contexto e assim por diante. No lado do serviço, pode haver variabilidade na latência devido à pressão no sistema do Armazenamento do Azure, diferenças na mídia de armazenamento usada, ruído de outras cargas de trabalho, tarefas de manutenção e outros fatores. Por fim, a conexão de rede entre o cliente e o servidor pode afetar a latência do Armazenamento do Azure devido a congestionamento, redirecionamento ou outras interrupções.

A largura de banda do Armazenamento do Azure, também conhecida como taxa de transferência, está relacionada à taxa de solicitação e pode ser calculada multiplicando a taxa de solicitação (IOPS) pelo tamanho da solicitação. Por exemplo, supondo um valor de 160 solicitações por segundo, cada 256 KiB de dados resulta em uma taxa de transferência de 40.960 KiB por segundo ou 40 MiB por segundo.

## <a name="latency-metrics-for-block-blobs"></a>Métricas de latência para blobs de bloco

O Armazenamento do Azure fornece duas métricas de latência para blobs de blocos. Essas métricas podem ser exibidas no portal do Azure:

- A **latência de ponta a ponta (E2E)** mede o intervalo de quando o Armazenamento do Azure recebe o primeiro pacote da solicitação até que o Armazenamento do Azure receba uma confirmação do cliente no último pacote da resposta.

- A **latência do servidor** mede o intervalo entre quando o Armazenamento do Azure recebe o último pacote da solicitação até que o primeiro pacote da resposta seja retornado do Armazenamento do Azure.

A imagem a seguir mostra a **Latência média de sucesso E2E** e a **Latência média de sucesso do servidor** para uma carga de trabalho de exemplo que chama a operação `Get Blob`:

![Captura de tela mostrando métricas de latência para a operação Obter Blob](media/storage-blobs-latency/latency-metrics-get-blob.png)

Em condições normais, há pouca diferença entre a latência de ponta a ponta e a latência do servidor, que é a imagem mostrada para a carga de trabalho de exemplo.

Se você examinar suas métricas de latência de servidor e de ponta a ponta e descobrir que a latência de ponta a ponta é significativamente maior do que a do servidor, investigue e resolva a origem da latência adicional.

Se a latência de ponta a ponta e de servidor forem semelhantes, mas você precisar de uma latência mais baixa, considere migrar para o Armazenamento de Blobs de blocos Premium.

## <a name="factors-influencing-latency"></a>Fatores que influenciam a latência

O fator principal que influencia a latência é o tamanho da operação. Leva mais tempo para concluir operações maiores, devido à quantidade de dados sendo transferidos pela rede e processados pelo Armazenamento do Azure.

O diagrama a seguir mostra o tempo total para operações de vários tamanhos. Para pequenas quantidades de dados, o intervalo de latência é basicamente gasto tratando a solicitação, em vez de transferir dados. O intervalo de latência aumenta um pouco conforme o tamanho da operação aumenta (marcado como 1 no diagrama abaixo). À medida que o tamanho da operação aumenta, mais tempo é gasto na transferência de dados, de modo que o intervalo de latência total seja dividido entre a manipulação da solicitação e a transferência dos dados (marcado como 2 no diagrama abaixo). Com tamanhos de operação maiores, o intervalo de latência é quase exclusivamente gasto na transferência de dados e o tratamento da solicitação é basicamente insignificante (marcado como 3 no diagrama abaixo).

![Captura de tela mostrando o tempo total da operação por tamanho da operação](media/storage-blobs-latency/operation-time-size-chart.png)

Fatores de configuração do cliente, como simultaneidade e threading, também afetam a latência. A taxa de transferência geral depende de quantas solicitações de armazenamento estão em trânsito em qualquer determinado momento e de como seu aplicativo lida com threading. Os recursos do cliente, incluindo CPU, memória, armazenamento local e interfaces de rede, também podem afetar a latência.

O processamento de solicitações de Armazenamento do Azure exige recursos de CPU e de memória do cliente. Se o cliente estiver sob pressão devido a uma máquina virtual com baixo consumo ou algum processo sem controle no sistema, haverá menos recursos disponíveis para processar as solicitações do Armazenamento do Azure. Qualquer contenção ou falta de recursos do cliente resultará em um aumento na latência de ponta a ponta sem um aumento na latência do servidor, aumentando a lacuna entre as duas métricas.

Igualmente importante é o adaptador de rede e o pipe de rede entre o cliente e o Armazenamento do Azure. A distância física poderá ser um fator significativo, por exemplo, se uma VM do cliente estiver em um local ou região do Azure diferente. Outros fatores, como saltos de rede, roteamento de ISP e estado da Internet, podem influenciar a latência geral do armazenamento.

Para avaliar a latência, primeiro estabeleça métricas de linha de base para seu cenário. As métricas de linha de base fornecem a latência de ponta a ponta e de servidor esperadas no contexto do seu ambiente de aplicativo, dependendo do perfil da carga de trabalho, das definições de configuração do aplicativo, dos recursos do cliente, do pipe de rede e de outros fatores. Quando tem métricas de linha de base, você pode identificar com mais facilidade as condições anormais versus normais. As métricas de linha de base também permitem que você observe os efeitos de parâmetros alterados, como a configuração do aplicativo ou os tamanhos de VM.

## <a name="next-steps"></a>Próximas etapas

- [Metas de escalabilidade e desempenho do Armazenamento de Blobs](scalability-targets.md)
- [Lista de verificação de desempenho e escalabilidade do Armazenamento de Blobs](storage-performance-checklist.md)
