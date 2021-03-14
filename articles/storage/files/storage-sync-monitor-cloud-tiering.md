---
title: Monitorar Sincronização de Arquivos do Azure camadas de nuvem | Microsoft Docs
description: Detalhes sobre as métricas a serem usadas para monitorar as políticas de camadas de nuvem.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 93e9d9a552ab1bff16ca15d6bb11faeb5d47a503
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204289"
---
# <a name="monitor-cloud-tiering"></a>Monitorar camadas de nuvem
Há duas maneiras de monitorar sua política de camadas de nuvem: a folha de propriedades do ponto de extremidade do servidor e a Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Monitoramento por meio do ponto de extremidade do servidor

Entre no [portal do Azure](https://portal.azure.com/), em seguida, navegue até a folha de **Propriedades do ponto de extremidade do servidor** para o ponto de extremidade do servidor que você deseja monitorar e role para baixo até a seção camadas de nuvem. 

![Uma captura de tela da seção camadas da nuvem nas propriedades do ponto de extremidade do servidor.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

A **economia de espaço** é a quantidade de espaço economizada habilitando a disposição em camadas na nuvem. Se o tamanho do volume local for grande o suficiente para manter todos os seus dados, você terá 0% de economia de espaço. Se você tiver 0% ou uma baixa porcentagem de economia de espaço, isso poderá indicar que a camada de nuvem não é benéfica com o tamanho atual do cache local. 

**Taxa de acesso ao cache** é a porcentagem de arquivos que você abre em seu cache local. A taxa de acesso ao cache é calculada por arquivos abertos diretamente do cache/número total de arquivos abertos. Uma taxa de impacto de cache de 100% significa que todos os arquivos que você abriu na última hora já estavam em seu cache local. Se seu objetivo é reduzir a saída, isso indica que sua política atual está funcionando bem.

> [!NOTE]
> Cargas de trabalho com padrões de acesso aleatórios normalmente têm taxas de impacto de cache menores. 

**Tamanho total (nuvem)** é o tamanho dos arquivos que foram sincronizados com a nuvem. 

**Tamanho em cache (servidor)** é o tamanho total dos arquivos no servidor, baixados e em camadas. Às vezes, o tamanho em cache pode ser maior do que o tamanho total dos arquivos na nuvem. Variáveis como o tamanho do cluster do volume no servidor podem causar isso. Se o tamanho em cache for maior do que o desejado, considere aumentar a política de espaço livre do volume. 

A **política de volume em vigor** é usada pelo sincronização de arquivos do Azure para determinar a quantidade de espaço livre a ser deixada no volume no qual o ponto de extremidade do servidor está. Quando há vários pontos de extremidade do servidor no mesmo volume, a política de espaço livre do volume mais alto entre os pontos de extremidade do servidor se torna a política de volume efetiva para todos os pontos de extremidade do servidor nesse volume. Por exemplo, se houver dois pontos de extremidade de servidor no mesmo volume, um com 30% de espaço livre em volume e outro com 50% de espaço livre em volume, a política de espaço livre do volume efetivo para ambos os pontos de extremidade do servidor será 50%.

**Espaço livre no volume atual** é o espaço livre no volume disponível no momento no servidor local. Se você tiver alta saída, mas mais espaço livre no volume disponível antes da política de espaço livre do volume ser acionada, considere desabilitar a política de data. Outro problema pode ser de fora dos arquivos em camadas no momento, o arquivo acessado mais recentemente é maior do que o espaço livre do volume restante antes que a política seja iniciada. Nesse caso, considere aumentar o tamanho do volume local. 

## <a name="monitoring-via-azure-monitor"></a>Monitoramento via Azure Monitor

Vá para o **serviço de sincronização de armazenamento** e selecione **métricas** na navegação lateral. 

Há três métricas diferentes que você pode usar para monitorar sua saída especificamente da camada de nuvem:

- Tamanho de recall em camadas de nuvem
    - Este é o tamanho total dos dados recuperados em bytes e pode ser usado para identificar a quantidade de dados que estão sendo recuperados.
- Tamanho de recall de camadas de nuvem por aplicativo
    - Este é o tamanho total dos dados recuperados em bytes por um aplicativo e pode ser usado para identificar o que está rechamando os dados.
- Taxa de transferência de recall em camadas de nuvem
    - Isso mede a rapidez com que os dados estão sendo recuperados em bytes e devem ser usados se você tiver preocupações com o desempenho. 

Para ser mais específico no que você deseja que os gráficos sejam exibidos, considere o uso de **Adicionar filtro** e **aplicar divisão**.
 
Para obter detalhes sobre os diferentes tipos de métricas para Sincronização de Arquivos do Azure e como usá-las, consulte [monitorar sincronização de arquivos do Azure](storage-sync-files-monitoring.md).

Para obter detalhes sobre como usar as métricas, consulte [introdução ao Azure Metrics Explorer.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started).

Se você quiser alterar sua política de camadas de nuvem, consulte [escolher políticas de camadas de nuvem](storage-sync-choose-cloud-tiering-policies.md).

## <a name="next-steps"></a>Próximas etapas
* [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)