---
title: Migrar para o monitor de conexão do monitor de conexão
titleSuffix: Azure Network Watcher
description: Saiba como migrar para o monitor de conexão do monitor de conexão.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 3c094bf5397d15e24c3b1a58369a74438c99017e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018404"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Migrar para o monitor de conexão do monitor de conexão (clássico)

Você pode migrar os monitores de conexão existentes para um novo monitor de conexão aprimorado com apenas alguns cliques e sem tempo de inatividade. Para saber mais sobre os benefícios, consulte [Monitor de conexão](./connection-monitor-overview.md).

## <a name="key-points-to-note"></a>Pontos-chave a serem observados

A migração ajuda a produzir os seguintes resultados:

* Os agentes e as configurações de firewall funcionam como estão. Nenhuma alteração é necessária. 
* Os monitores de conexão existentes são mapeados para o monitor de conexão > grupo de teste > formato de teste. Ao selecionar **Editar**, você pode exibir e modificar as propriedades do novo monitor de conexão, baixar um modelo para fazer alterações no monitor de conexão e enviá-lo por meio de Azure Resource Manager. 
* As máquinas virtuais do Azure com a extensão do observador de rede enviam dados para o espaço de trabalho e para as métricas. O monitor de conexão torna os dados disponíveis por meio das novas métricas (ChecksFailedPercent e RoundTripTimeMs) em vez das métricas antigas (ProbesFailedPercent e AverageRoundtripMs). 
* Monitoramento de dados:
   * **Alertas**: migrados automaticamente para as novas métricas.
   * **Painéis e integrações**: requer a edição manual do conjunto de métricas. 
    
## <a name="prerequisites"></a>Pré-requisitos

Se você estiver usando um espaço de trabalho personalizado, verifique se o observador de rede está habilitado em sua assinatura e na região do seu espaço de trabalho de Log Analytics. 

## <a name="migrate-the-connection-monitors"></a>Migrar os monitores de conexão

1. Para migrar os monitores de conexão mais antigos para o mais recente, selecione **Monitor de conexão** e, em seguida, selecione **migrar monitores de conexão**.

    ![Captura de tela mostrando a migração de monitores de conexão para o monitor de conexão.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selecione sua assinatura e os monitores de conexão que você deseja migrar e selecione **migrar selecionado**. 

Com apenas alguns cliques, você migrou os monitores de conexão existentes para o monitor de conexão. 

Agora você pode personalizar as propriedades do monitor de conexão, alterar o espaço de trabalho padrão, baixar modelos e verificar o status da migração. 

Após o início da migração, as seguintes alterações ocorrem: 
* O recurso Azure Resource Manager muda para o monitor de conexão mais recente.
    * O nome, a região e a assinatura do monitor de conexão permanecem inalterados. A ID do recurso não é afetada.
    * A menos que o monitor de conexão seja personalizado, um espaço de trabalho de Log Analytics padrão é criado na assinatura e na região do monitor de conexão. Esse espaço de trabalho é onde os dados de monitoramento são armazenados. Os dados de resultado do teste também são armazenados nas métricas.
    * Cada teste é migrado para um grupo de teste chamado *DefaultTest*.
    * Os pontos de extremidade de origem e destino são criados e usados no novo grupo de teste. Os nomes padrão são *defaultSourceEndpoint* e *defaultDestinationEndpoint*.
    * A porta de destino e o intervalo de investigação são movidos para uma configuração de teste chamada *defaultTestConfiguration*. O protocolo é definido com base nos valores de porta. Os limites de êxito e outras propriedades opcionais são deixados em branco.
* Os alertas de métricas são migrados para alertas de métricas do monitor de conexão. As métricas são diferentes, portanto, a alteração. Para obter mais informações, consulte [monitoramento de conectividade de rede com o monitor de conexão](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Os monitores de conexão migrados não são mais exibidos como a solução de monitor de conexão mais antiga. Agora eles estão disponíveis para uso somente no monitor de conexão.
* Todas as integrações externas, como painéis em Power BI e Grafana, e integrações com sistemas SIEM (gerenciamento de eventos e informações de segurança), devem ser migradas manualmente. Essa é a única etapa manual que você precisa executar para migrar a configuração.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o monitor de conexão, consulte:
* [Migrar do monitor de Monitor de Desempenho de Rede para conexão](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Criar monitor de conexão usando o portal do Azure](./connection-monitor-create-using-portal.md)