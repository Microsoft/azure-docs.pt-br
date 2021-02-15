---
title: Configurar a coleta de dados para o agente de Azure Monitor (versão prévia)
description: Descreve como criar uma regra de coleta de dados para coletar dados de máquinas virtuais usando o agente de Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: d04fa25b9c953d151fc16d11f304c48b7046ab76
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680399"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Configurar a coleta de dados para o agente de Azure Monitor (versão prévia)

As regras de coleta de dados (DCR) definem os dados que chegam ao Azure Monitor e especificam onde devem ser enviados. Este artigo descreve como criar uma regra de coleta de dados para coletar dados de máquinas virtuais usando o agente de Azure Monitor.

Para obter uma descrição completa das regras de coleta de dados, consulte [regras de coleta de dados em Azure monitor (versão prévia)](data-collection-rule-overview.md).

> [!NOTE]
> Este artigo descreve como configurar dados para máquinas virtuais com o agente de Azure Monitor que está atualmente em versão prévia. Consulte [visão geral dos agentes de Azure monitor](agents-overview.md) para obter uma descrição dos agentes que estão geralmente disponíveis e como usá-los para coletar dados.

## <a name="data-collection-rule-associations"></a>Associações de regra de coleta de dados

Para aplicar um DCR a uma máquina virtual, crie uma associação para a máquina virtual. Uma máquina virtual pode ter uma associação a vários DCRs, e um DCR pode ter várias máquinas virtuais associadas a ele. Isso permite que você defina um conjunto de DCRs, cada um correspondendo a um requisito específico e aplique-os somente às máquinas virtuais onde eles se aplicam. 

Por exemplo, considere um ambiente com um conjunto de máquinas virtuais que executam um aplicativo de linha de negócios e outras em execução SQL Server. Você pode ter uma regra de coleta de dados padrão que se aplica a todas as máquinas virtuais e a regras de coleta de dados separadas que coletam dados especificamente para o aplicativo de linha de negócios e para SQL Server. As associações para as máquinas virtuais para as regras de coleta de dados se assemelhariam ao diagrama a seguir.

![O diagrama mostra as máquinas virtuais que hospedam aplicativos de linha de negócios e SQL Server associadas a regras de coleta de dados denominada Central-i t-padrão e LOB-app para aplicativos de linha de negócios e central-i t-default e s q l para SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Criar regra e associação no portal do Azure

Você pode usar o portal do Azure para criar uma regra de coleta de dados e associar máquinas virtuais em sua assinatura a essa regra. O agente de Azure Monitor será instalado automaticamente e uma identidade gerenciada criada para qualquer máquina virtual que ainda não tiver sido instalada.

> [!IMPORTANT]
> Atualmente, há um problema conhecido em que, se a regra de coleta de dados criar uma identidade gerenciada em uma máquina virtual que já tenha uma identidade gerenciada atribuída pelo usuário, a identidade atribuída pelo usuário será desabilitada.

No menu **Azure monitor** na portal do Azure, selecione regras de **coleta de dados** na seção **configurações** . Clique em **Adicionar** para adicionar uma nova regra de coleta de dados e atribuição.

[![Regras de coleta de dados](media/azure-monitor-agent/data-collection-rules.png)](media/azure-monitor-agent/data-collection-rules.png#lightbox)

Clique em **Adicionar** para criar uma nova regra e um conjunto de associações. Forneça um **nome de regra** e especifique uma **assinatura** e um **grupo de recursos**. Isso especifica onde o DCR será criado. As máquinas virtuais e suas associações podem estar em qualquer assinatura ou grupo de recursos no locatário.

[![Noções básicas da regra de coleta de dados](media/azure-monitor-agent/data-collection-rule-basics.png)](media/azure-monitor-agent/data-collection-rule-basics.png#lightbox)

Na guia **máquinas virtuais** , adicione máquinas virtuais que devem ter a regra de coleta de dados aplicada. Tanto as máquinas virtuais do Azure quanto os servidores habilitados para Arc do Azure em seu ambiente devem ser listados. O agente de Azure Monitor será instalado em máquinas virtuais que ainda não estão instaladas.

[![Máquinas virtuais da regra de coleta de dados](media/azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Na guia **coletar e entregar** , clique em **Adicionar fonte de dados** para adicionar uma fonte de dados e um conjunto de destino. Selecione um **tipo de fonte de dados** e os detalhes correspondentes a serem selecionados serão exibidos. Para contadores de desempenho, você pode selecionar um conjunto predefinido de objetos e sua taxa de amostragem. Para eventos, você pode selecionar em um conjunto de logs ou instalações e no nível de severidade. 

[![Fonte de dados básica](media/azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Para especificar outros logs e contadores de desempenho das [fontes de dados com suporte no momento](azure-monitor-agent-overview.md#data-sources-and-destinations) ou para filtrar eventos usando consultas XPath, selecione **personalizado**. Em seguida, você pode especificar um [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) para quaisquer valores específicos a serem coletados. Consulte o [exemplo DCR](data-collection-rule-overview.md#sample-data-collection-rule) para obter exemplos.

[![Fonte de dados personalizada](media/azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

Na guia **destino** , adicione um ou mais destinos para a fonte de dados. As fontes de dados de eventos e syslog do Windows só podem enviar para Azure Monitor logs. Os contadores de desempenho podem enviar para Azure Monitor métricas e logs de Azure Monitor.

[![Destino](media/azure-monitor-agent/data-collection-rule-destination.png)](media/azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Clique em **Adicionar fonte de dados** e **revise + criar** para examinar os detalhes da regra de coleta de dados e associação com o conjunto de VMs. Clique em **criar** para criá-lo.

> [!NOTE]
> Após a criação da regra de coleta de dados e das associações, pode levar até 5 minutos para que os dados sejam enviados aos destinos.


## <a name="create-rule-and-association-using-rest-api"></a>Criar regra e Associação usando a API REST

Siga as etapas abaixo para criar uma regra de coleta de dados e associações usando a API REST.

1. Crie manualmente o arquivo DCR usando o formato JSON mostrado no [exemplo DCR](data-collection-rule-overview.md#sample-data-collection-rule).

2. Crie a regra usando a [API REST](/rest/api/monitor/datacollectionrules/create#examples).

3. Crie uma associação para cada máquina virtual para a regra de coleta de dados usando a [API REST](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Criar Associação usando o modelo do Resource Manager

Você não pode criar uma regra de coleta de dados usando um modelo do Resource Manager, mas pode criar uma associação entre uma máquina virtual do Azure ou um servidor habilitado para Arc do Azure usando um modelo do Resource Manager. Consulte [exemplos de modelo do Resource Manager para regras de coleta de dados em Azure monitor](../samples/resource-manager-data-collection-rules.md) para modelos de exemplo.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [agente de Azure monitor](azure-monitor-agent-overview.md).
- Saiba mais sobre [regras de coleta de dados](data-collection-rule-overview.md).
