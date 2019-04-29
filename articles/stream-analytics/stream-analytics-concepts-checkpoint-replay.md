---
title: Conceitos de ponto de verificação e de recuperação de trabalho de reprodução no Azure Stream Analytics
description: Este artigo descreve conceitos de ponto de verificação e de recuperação de trabalho de reprodução no Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 9dcfbd4b5fcc8462c88b16f585424166ecd3d499
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61361868"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Conceitos de ponto de verificação e de reprodução em trabalhos do Azure Stream Analytics
Este artigo descreve os conceitos internos de ponto de verificação e de reprodução no Azure Stream Analytics e o impacto deles na recuperação de trabalho. Sempre que um trabalho do Stream Analytics é executado, as informações de estado são mantidas internamente. Essas informações de estado são salvas em um ponto de verificação periodicamente. Em alguns cenários, as informações de ponto de verificação são usadas para a recuperação de trabalho se ocorrer uma falha de trabalho ou de atualização. Em outras circunstâncias, o ponto de verificação não pode ser usado para a recuperação, e é necessária uma reprodução.

## <a name="stateful-query-logicin-temporal-elements"></a>Lógica de consulta com estado em elementos temporais
Um dos recursos exclusivos do trabalho do Azure Stream Analytics é a execução do processamento com estado, como funções de análise temporal, de junções temporais e de agregações em janela. Cada um desses operadores mantém informações de estado quando o trabalho é executado. O tamanho máximo da janela para esses elementos de consulta é de sete dias. 

O conceito de janela temporal é exibido em vários elementos de consulta do Stream Analytics:
1. Agregações em janela (GROUP BY Em cascata, Salto e Janelas deslizantes)

2. Junções temporais (JOIN com DATEDIFF)

3. Funções analíticas temporais (ISFIRST, LAST e a LAG com LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>A recuperação de trabalho de uma falha de nó, incluindo atualização do sistema operacional
Sempre que um trabalho do Stream Analytics é executado, internamente ele é dimensionado para trabalhar em vários nós de trabalho. O estado de cada nó de trabalho tem seu ponto de verificação a cada intervalo de alguns minutos, o que ajuda o sistema a se recuperar em caso de falha.

Às vezes, um determinado nó de trabalho pode falhar, ou pode ocorrer uma atualização de Sistema Operacional para o nó de trabalho. Para se recuperar automaticamente, o Stream Analytics adquire um novo nó íntegro, e o estado do nó anterior é restaurado do ponto de verificação mais recente disponível. Para retomar o trabalho, é necessária uma pequena reprodução para restaurar o estado do momento em que o ponto de verificação é definido. Geralmente, o intervalo de restauração é de apenas alguns minutos. Quando forem selecionadas Unidades de Streaming suficientes para o trabalho, a reprodução deverá ser concluída rapidamente. 

Em uma consulta totalmente paralela, o tempo de atualização após uma falha de nó de trabalho é proporcional a:

[a taxa de eventos de entrada] x [o tamanho do intervalo] / [o número de partições de processamento]

Se você observar um atraso significativo de processamento devido à falha do nó e da atualização do sistema operacional, considere tornar a pesquisa totalmente paralela, e dimensione o trabalho para alocar mais Unidades de Streaming. Para obter mais informações, confira [Dimensionar um trabalho do Azure Stream Analytics para aumentar a taxa de transferência](stream-analytics-scale-jobs.md).

O Stream Analytics atual não mostra um relatório quando esse tipo de processo de recuperação está em andamento.

## <a name="job-recovery-from-a-service-upgrade"></a>Recuperação de trabalho de uma atualização de serviço 
Ocasionalmente, a Microsoft atualiza os binários que executam os trabalhos do Stream Analytics no serviço do Azure. Nesses momentos, é feita a atualização dos usuários que estão executando trabalhos para a versão mais recente, e o trabalho é reiniciado automaticamente. 

Atualmente, o formato do ponto de verificação de recuperação não é preservado entre as atualizações. Como resultado, o estado da consulta de streaming deve ser inteiramente restaurado usando a técnica de reprodução. Para permitir que os trabalhos do Stream Analytics reproduzam exatamente a mesma entrada de antes, é importante definir a política de retenção para os dados de origem como pelo menos os tamanhos das janelas em sua consulta. Não fazer isso pode levar a resultados incorretos ou parciais durante a atualização do serviço, pois os dados de origem podem não ser retidos na extensão suficiente para incluir o tamanho máximo de janela.

Em geral, a quantidade de repetição necessária é proporcional ao tamanho da janela multiplicado pela taxa média de eventos. Por exemplo, para um trabalho com uma taxa de entrada de 1000 eventos por segundo, um tamanho de janela maior do que uma hora é considerado como tendo um tamanho grande de reprodução. Até uma hora de dados pode precisar ser reprocessada para inicializar o estado para que  possa produzir resultados completos e corretos, o que pode causar saída atrasada (sem saída) por um período prolongado. Consultas sem janelas ou outros operadores temporais, como `JOIN` ou `LAG`, teriam zero de repetição.

## <a name="estimate-replay-catch-up-time"></a>Estimar tempo de atualização de reprodução
Para estimar a duração do atraso devido a uma atualização de serviço, você pode seguir esta técnica:

1. Carregue o Hub de Eventos de entrada com dados suficientes para abranger o maior tamanho de janela na consulta, na taxa de eventos esperada. O carimbo de data/hora dos eventos deverá estar próximo ao horário do relógio durante todo esse período, já que é um feed de entrada ao vivo. Por exemplo, se você tiver uma janela de três dias em sua consulta, envie eventos para o Hub de Eventos por três dias e continue a enviar eventos. 

2. Inicie o trabalho usando **Agora** como a hora de início. 

3. Meça o tempo entre a hora de início e quando a primeira saída é gerada. O tempo será aproximadamente o tamanho do atraso que ocorreria no trabalho durante uma atualização de serviço.

4. Se o atraso for muito longo, tente particionar o trabalho e aumentar o número de SUs para que a carga seja distribuída por mais nós. Como alternativa, considere reduzir os tamanhos de janelas na consulta e executar outra agregação ou outro processamento com estado na saída produzida pelo trabalho do Stream Analytics no coletor downstream (por exemplo, usando o banco de dados SQL do Azure).

Para questões de estabilidade geral do serviço durante a atualização de trabalho de missão crítica, considere a possibilidade de executar trabalhos duplicados em regiões emparelhadas do Azure. Para obter mais informações, confira [Garantir a confiabilidade do trabalho do Stream Analytics durante atualizações do serviço](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>A recuperação de trabalho de um usuário parou e iniciou
Para editar a sintaxe de Consulta em um trabalho de streaming, ou para ajustar as entradas e saídas, o trabalho deve ser interrompido para fazer as alterações e a atualização do design do trabalho. Nesses cenários, quando um usuário para o trabalho de streaming e o inicia novamente, o cenário de recuperação é semelhante à atualização do serviço. 

Os dados de ponto de verificação não podem ser usados para uma reinicialização de trabalho iniciada pelo usuário. Para estimar o atraso de saída durante tal reinicialização, use o mesmo procedimento conforme descrito na seção anterior e aplique uma mitigação semelhante se o atraso for muito longo.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre confiabilidade e escalabilidade, confira estes artigos:
- [Tutorial: Configurar alertas para trabalhos do Stream Analytics do Azure](stream-analytics-set-up-alerts.md)
- [Dimensionar um trabalho do Azure Stream Analytics para aumentar a taxa de transferência](stream-analytics-scale-jobs.md)
- [Garantir a confiabilidade do trabalho do Stream Analytics durante atualizações do serviço](stream-analytics-job-reliability.md)
