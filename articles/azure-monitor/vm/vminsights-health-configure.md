---
title: Configurar o monitoramento na integridade do convidado do insights de VM (versão prévia)
description: Descreve como modificar o monitoramento padrão para a integridade de convidado do insights de VM (versão prévia) usando o portal do Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 0f6599bb9f379cf471dafbb83a7deefbb05c0dbe
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052204"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-preview"></a>Configurar o monitoramento na integridade do convidado do insights de VM (versão prévia)
A integridade de convidado do insights de VM permite que você exiba a integridade de uma máquina virtual conforme definido por um conjunto de medidas de desempenho que são amostradas em intervalos regulares. Este artigo descreve como você pode modificar o monitoramento padrão usando o portal do Azure. Ele também descreve os conceitos fundamentais dos monitores necessários para [Configurar o monitoramento usando uma regra de coleta de dados](vminsights-health-configure-dcr.md).

## <a name="open-monitor-configuration"></a>Abrir configuração do monitor
Abra a configuração do monitor bin The portal do Azure selecionando o monitor e, em seguida, a guia **Configuration** .

[![Configuração de detalhes do monitor](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Habilitar ou desabilitar monitores
Os monitores de unidade e os monitores agregados têm uma configuração de **status do monitor de integridade** que permite habilitar ou desabilitar o monitor. Quando um monitor é habilitado, sua integridade é exibida e usada para definir a integridade da VM. Quando um monitor é desabilitado, sua integridade não é calculada e não é usada para definir a integridade da VM.

| Configuração | Descrição |
|:---|:---|
| habilitado | O monitor é habilitado independentemente da configuração de seu pai. |
| Desabilitado | O monitor é desabilitado independentemente da configuração de seu pai. |
| Igual ao pai | O monitor será habilitado ou desabilitado dependendo da configuração de seu pai. |

Quando um monitor é desabilitado, todos os critérios são mostrados como não disponíveis, conforme mostrado no exemplo a seguir.

![Monitor desabilitado](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> Se um monitor pai estiver desabilitado, todos os monitores filho também serão desabilitados. Se você habilitar explicitamente o monitor filho, o pai também será habilitado, mas seu status de configuração permanecerá o mesmo. Nesse caso, você receberá a seguinte mensagem no monitor pai.
>
> *Há uma discrepância, pois o status configurado do monitor é ' desabilitado ', mas o estado de integridade não reflete isso. Isso ocorre porque as alterações configuradas estão sendo propagadas ou qualquer um de seus monitores filhos foi explicitamente habilitado.*

## <a name="enable-or-disable-virtual-machine"></a>Habilitar ou desabilitar a máquina virtual
Você pode desabilitar o monitoramento de uma VM para interromper temporariamente todos os monitores. Você pode desabilitar o monitoramento de uma VM, por exemplo, quando estiver executando a manutenção nela.

| Configuração | Descrição |
|:---|:---|
| habilitado  | O estado de integridade do computador é exibido. |
| Desabilitado | O estado de integridade do computador é mostrado como **desabilitado**. Os alertas não são criados. |

## <a name="health-state-logic"></a>Lógica do estado de integridade
A lógica do estado de integridade para um monitor de unidade define os critérios para definir seu estado de integridade. Você pode especificar quantos Estados de integridade um monitor tem e o limite de como cada Estado de integridade é calculado.

![Critérios de integridade de exemplo](media/vminsights-health-configure/sample-health-criteria.png)

Os monitores agregados não especificam nenhuma lógica de estado de integridade. Seu estado de integridade é definido pelos monitores de unidade sob eles de acordo com seu acúmulo de integridade.

Monitores de unidade têm dois ou três Estados de integridade. Cada um sempre terá um estado íntegro e, opcionalmente, um estado de aviso, um estado crítico ou ambos. Os Estados de aviso e crítico exigem critérios exclusivos que definem quando o monitor é definido como esse estado. O estado íntegro não tem critérios, pois esse estado é definido quando os critérios para os outros Estados não são atendidos.

No exemplo a seguir, a utilização da CPU está definida para os seguintes Estados de integridade:

- Crítico se for maior que 90%.
- Aviso se for maior ou igual a 80%.
- Íntegro se for inferior a 80%. Isso é implícito, pois é a condição em que nenhuma das outras condições se aplica.

## <a name="next-steps"></a>Próximas etapas

- [Configure monitores em escala usando regras de coleta de dados.](vminsights-health-configure-dcr.md)