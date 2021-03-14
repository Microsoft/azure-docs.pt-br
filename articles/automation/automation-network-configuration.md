---
title: Detalhes de configuração de rede da automação do Azure
description: Este artigo fornece detalhes das informações de rede exigidas pela configuração de estado da automação do Azure, Hybrid Runbook Worker de automação do Azure, Gerenciamento de Atualizações e Controle de Alterações e inventário
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 36331e9c07926d4d3ffff136aefa2f9a77d47cb4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708876"
---
# <a name="azure-automation-network-configuration-details"></a>Detalhes de configuração de rede da automação do Azure

Esta página fornece detalhes de rede necessários para [Hybrid runbook Worker e configuração de estado](#hybrid-runbook-worker-and-state-configuration)e para [Gerenciamento de atualizações e controle de alterações e inventário](#update-management-and-change-tracking-and-inventory).

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Configuração de Hybrid Runbook Worker e estado

A porta e as URLs a seguir são necessárias para o Hybrid Runbook Worker e para [configuração de estado de automação](automation-dsc-overview.md) para se comunicar com a automação do Azure.

* Porta: somente 443 necessário para acesso de Internet de saída
* URL global: `*.azure-automation.net`
* URL global de US Gov-Virgínia: `*.azure-automation.us`
* Serviço do agente: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Planejamento de rede para Hybrid Runbook Worker

Para um sistema ou usuário Hybrid Runbook Worker para se conectar e se registrar com a automação do Azure, ele deve ter acesso ao número da porta e às URLs descritas nesta seção. O trabalhador também deve ter acesso às [portas e às URLs necessárias para que o agente de log Analytics](../azure-monitor/agents/agent-windows.md) se conecte ao espaço de trabalho Azure monitor log Analytics.

Se você tiver uma conta da Automação definida para uma região específica, você pode restringir a comunicação do Hybrid Runbook Worker para esse centro de dados regional. Examine os [registros DNS usados pela automação do Azure](how-to/automation-region-dns-records.md) para os registros DNS necessários.

### <a name="configuration-of-private-networks-for-state-configuration"></a>Configuração de redes privadas para configuração de estado

Se os nós estiverem localizados em uma rede privada, a porta e as URLs definidas acima serão necessárias. Esses recursos fornecem conectividade de rede para o nó gerenciado e permitem que a DSC se comunique com a Automação do Azure.

Se você estiver usando recursos de DSC que se comunicam entre os nós, como os recursos [WaitFor*](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), também será necessário permitir o tráfego entre os nós. Confira a documentação de cada recurso de DSC para entender esses requisitos de rede.

Para entender os requisitos do cliente para o TLS 1,2, confira [imposição tls 1,2 para a automação do Azure](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="update-management-and-change-tracking-and-inventory"></a>Gerenciamento de Atualizações e Controle de Alterações e inventário

Os endereços nessa tabela são necessários para Gerenciamento de Atualizações e para Controle de Alterações e inventário. O parágrafo após a tabela também se aplica a ambos.

A comunicação com esses endereços usa a **porta 443**.

|Público do Azure  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*. azure-automation.us|

Quando você cria regras de segurança de grupo de rede ou configura o Firewall do Azure para permitir o tráfego para o serviço de automação e o espaço de trabalho Log Analytics, use as [marcas de serviço](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** e **AzureMonitor**. Isso simplifica o gerenciamento contínuo de suas regras de segurança de rede. Para se conectar ao serviço de automação de suas VMs do Azure de forma segura e privada, examine [usar o link privado do Azure](./how-to/private-link-security.md). Para obter a marca de serviço e as informações de intervalo atuais para incluir como parte de suas configurações de firewall local, consulte [arquivos JSON para download](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [automação gerenciamento de atualizações visão geral](update-management\overview.md).
* Saiba mais sobre [Hybrid runbook Worker](automation-hybrid-runbook-worker.md).
* Saiba mais sobre [controle de alterações e inventário](change-tracking\overview.md).
* Saiba mais sobre a [configuração de estado de automação](automation-dsc-overview.md).