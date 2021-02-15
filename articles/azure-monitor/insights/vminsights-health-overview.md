---
title: Azure Monitor para VMs a integridade do convidado (versão prévia)
description: Visão geral do recurso de integridade no Azure Monitor para VMs, incluindo como você pode exibir a integridade de suas máquinas virtuais e receber alertas quando uma máquina virtual se tornar não íntegra.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 96bed9f3b04e54e2e9a5234d78f9a2660126742e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686647"
---
# <a name="azure-monitor-for-vms-guest-health-preview"></a>Azure Monitor para VMs a integridade do convidado (versão prévia)
Azure Monitor para VMs integridade de convidado permite que você exiba a integridade das máquinas virtuais com base em um conjunto de medidas de desempenho que são amostradas em intervalos regulares do sistema operacional convidado. Você pode verificar rapidamente a integridade de todas as máquinas virtuais em uma assinatura ou grupo de recursos, fazer uma busca detalhada sobre a integridade detalhada de uma determinada máquina virtual ou ser notificado proativamente quando uma máquina virtual se tornar não íntegra. 

## <a name="enable-virtual-machine-health"></a>Habilitar integridade da máquina virtual
Consulte [habilitar Azure monitor para VMs integridade de convidado (versão prévia)](vminsights-health-enable.md) para obter detalhes sobre como habilitar o recurso de integridade de convidado e integrar máquinas virtuais.

## <a name="pricing"></a>Preços
Não há custo direto para o recurso de integridade de convidado, mas há um custo de ingestão e armazenamento de dados de estado de integridade no espaço de trabalho Log Analytics. Todos os dados são armazenados na tabela *HealthStateChangeEvent* . Consulte [gerenciar o uso e os custos com os logs de Azure monitor](../platform/manage-cost-storage.md) para obter detalhes sobre os preços e os custos.

## <a name="view-virtual-machine-health"></a>Exibir integridade da máquina virtual
A coluna de **integridade da VM convidada** **na página Introdução** fornece uma exibição rápida da integridade de cada máquina virtual em uma determinada assinatura ou grupo de recursos. A integridade atual de cada máquina virtual é exibida enquanto os ícones de cada grupo mostram o número de máquinas virtuais atualmente em cada estado nesse grupo.

[![Página de introdução com integridade da VM convidada](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>Monitores
Clique no status de integridade de uma máquina virtual para exibir o status detalhado de cada um de seus monitores. Cada monitor mede a integridade de um componente específico. A integridade geral da máquina virtual é determinada pela integridade de seus monitores individuais. 

![Exemplo de monitores](media/vminsights-health-overview/monitors.png)

A tabela a seguir lista os monitores de unidade e agregação disponíveis atualmente para cada máquina virtual. 

| Monitor | Tipo | Descrição |
|:---|:---|:---|
| Utilização da CPU | Unidade | Percentual de utilização do processador. |
| Sistemas de arquivos | Agregado | Agregar integridade de todos os sistemas de arquivos na VM do Linux. |
| Sistema de arquivos  | Agregado | Integridade de cada sistema de arquivos individual na VM do Linux. O nome do monitor é o nome do sistema de arquivos. |
| Espaço livre | Unidade | Porcentagem de espaço livre no sistema de arquivos. |
| Discos lógicos | Agregado | Integridade agregada de todos os discos lógicos na VM do Windows. |
| Disco lógico  | Agregado | Integridade de cada disco lógico individual na VM do Windows. O nome do monitor é o nome do disco. |
| Memória | Agregado | Integridade agregada da memória na VM. |
| Memória disponível | Unidade | Megabytes disponíveis na VM. |

## <a name="health-states"></a>Estados de integridade
Cada monitor exemplifica os valores no agente a cada minuto e compara os valores de amostra com os critérios para cada Estado de integridade. Se os critérios para um estado específico forem true, o monitor será definido para esse estado. Se nenhum dos critérios for verdadeiro, o monitor será definido como um estado íntegro. Os dados são enviados do agente para Azure Monitor a cada três minutos ou imediatamente se houver uma alteração de estado.

Cada monitor tem uma janela lookback e analisa quaisquer exemplos coletados nesse momento. Por exemplo, um monitor pode ter uma janela lookback de 240 segundos procurando o valor máximo entre pelo menos 2 valores de amostra, mas não mais do que os 3 últimos. Embora os valores sejam amostrados em uma taxa regular, o número de amostra em uma janela específica pode variar ligeiramente se houver qualquer interrupção na operação do agente.

Os monitores têm cada um com os Estados de integridade em potencial na tabela a seguir e estarão em um e somente um em um determinado momento. Quando um monitor é inicializado, ele é iniciado em um estado íntegro.

| Estado de Integridade | Descrição |
|:---|:---|
| Íntegros  | O monitor não excede o limite crítico ou de aviso no momento. |
| Aviso  | O monitor excedeu o limite de aviso (se definido). |
| Crítico | O monitor excedeu o limite crítico (se definido). |
| Desconhecido  | Não foram coletados dados suficientes para determinar o estado de integridade. |
| Desabilitado | O monitor está desabilitado no momento. |
| Nenhum     | O monitor acabou de ser iniciado e ainda não foi avaliado ou o objeto monitorado não existe mais. |



Há dois tipos de monitores, conforme mostrado na tabela a seguir.

| Monitor | Descrição |
|:---|:---|
| Monitor de unidade | Mede algum aspecto de um recurso ou aplicativo. Isso pode estar verificando um contador de desempenho para determinar o desempenho do recurso ou sua disponibilidade. |
| Monitor agregado | Agrupa vários monitores para fornecer um único estado de integridade agregado. Um monitor agregado pode conter um ou mais monitores de unidade e outros monitores agregados. |


  
### <a name="health-rollup-policy"></a>Política de acumulação de integridade
O estado de integridade de uma máquina virtual é determinado pelo acúmulo de integridade de cada uma de suas unidades e de monitores agregados. Cada monitor agregado usa uma política de Rollup de integridade de pior estado em que o estado do monitor agregado corresponde ao estado do monitor filho com o pior estado de integridade.  

No exemplo a seguir, o monitor de **espaço livre** está em um estado crítico que faz a função até as agregações de sua instância e, em seguida, para os **sistemas de arquivos**. Embora a **utilização da CPU** esteja em um estado de aviso, a máquina virtual é definida como crítica, pois esse é o pior estado abaixo dela. Se o espaço livre fosse retornar a um estado íntegro, a máquina virtual seria alterada para um estado de aviso, uma vez que a utilização da CPU se tornaria o monitor com o pior estado.

![Exemplo de rollup de integridade](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>Detalhes do monitor
Selecione um monitor para exibir seus detalhes que incluem as guias a seguir.

A **visão geral** fornece uma descrição do monitor, a última vez que ele foi avaliado e os valores de amostra para determinar o estado de integridade atual. O número de amostras pode variar com base na definição do monitor e volatilidade dos valores.

[![Visão geral dos detalhes do monitor](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**Histórico** lista o histórico de alterações de estado para o monitor. Você pode expandir qualquer uma das alterações de estado para exibir valores avaliados para determinar o estado de integridade. Clique em **Exibir configuração aplicada** para exibir a configuração do monitor no momento em que o estado de integridade foi alterado.



[![Histórico de detalhes do monitor](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>Configuração
Exiba e modifique a configuração do monitor para a VM selecionada. Consulte [Configurar o monitoramento em Azure monitor para VMs integridade de convidado (versão prévia)](vminsights-health-enable.md) para obter detalhes.

[![Configuração de detalhes do monitor](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>Próximas etapas

- [Habilite a integridade do convidado em agentes de Azure Monitor para VMs e integrados.](vminsights-health-enable.md)
- [Configure monitores usando o portal do Azure.](vminsights-health-configure.md)
- [Configure monitores usando regras de coleta de dados.](vminsights-health-configure-dcr.md)