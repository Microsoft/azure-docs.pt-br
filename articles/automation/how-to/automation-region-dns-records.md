---
title: Registros DNS do datacenter do Azure usados pela automação do Azure | Microsoft Docs
description: Este artigo fornece os registros DNS exigidos pelos recursos de automação do Azure ao restringir a comunicação a uma região específica do Azure que hospeda essa conta de automação.
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: 8630afa7410aad81a7a3c61540fc74702fc6481c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576000"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Registros DNS para regiões do Azure usadas pela automação do Azure

O serviço de [automação do Azure](../automation-intro.md) usa vários registros DNS para que os recursos se conectem ao serviço. Se você tiver uma conta de Automação do Azure definida para uma região específica, você pode restringir a comunicação para esse centro de dados regional. Talvez seja necessário saber esses registros para permitir que os seguintes recursos de automação funcionem quando são hospedados atrás de um firewall:

* Hybrid Runbook Worker
* State Configuration
* Webhooks

>[!NOTE]
>O registro de Hybrid Runbook Worker do Linux falhará com os novos registros, a menos que seja a versão 1.6.10.2 ou superior. Você deve atualizar para uma versão mais recente do [agente de log Analytics para Linux](../../azure-monitor/agents/agent-linux.md) a fim de o computador receber uma versão atualizada da função de trabalho e usar esses novos registros. Os computadores existentes continuarão funcionando sem problemas.  

## <a name="dns-records-per-region"></a>Registros DNS por região

A tabela a seguir fornece o registro DNS para cada região.

>[!NOTE]
>Embora a lista de registros DNS de automação fornecida aqui tenha sido desativada, elas ainda permanecem funcionais para permitir o tempo de migração para os novos registros listados em [suporte para link privado](#support-for-private-link) e evitar falhas com seus processos de automação.

| **Região** | **Registro DNS** |
| --- | --- |
| Austrália Central |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Leste da Austrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Norte da Europa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Centro-Sul dos Estados Unidos |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Sudeste da Ásia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gov. dos EUA – Virgínia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| Centro-Oeste dos EUA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Oeste dos EUA 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Suporte para link privado

Para dar suporte ao [link privado](../../private-link/private-link-overview.md) na automação do Azure, os registros DNS para cada datacenter com suporte foram atualizados. Em vez de URLs específicas da região, as URLs são específicas da conta de automação.

| **Região** | **Registro DNS** |
| --- | --- |
| Centro-Oeste dos EUA |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| Oeste dos EUA |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| Oeste dos EUA 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| Centro dos EUA |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| Centro-Sul dos Estados Unidos |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| Centro-Norte dos EUA |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| Leste dos EUA |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| Leste dos EUA 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| Canadá Central |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| Europa Ocidental |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| Norte da Europa |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| Sudeste da Ásia |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| Leste da Ásia |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| Índia Central |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| Leste do Japão |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| Coreia Central |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| Sudeste da Austrália |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| Leste da Austrália |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| Austrália Central |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| Sul do Reino Unido |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| França Central |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| Norte da África do Sul |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| Brazil South |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| Norte da China |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| Norte da China 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| Leste da China 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| Gov. dos EUA – Virgínia |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| Governo dos EUA do Texas |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| Governo dos EUA do Arizona |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

Substitua `<accountId>` no registro DNS pelo GUID que representa a ID da conta de automação da **URL** do valor. Você pode obter a ID necessária das **chaves** em **configurações de conta** no portal do Azure.

![Página de chave primária da conta de automação](./media/automation-region-dns-records/automation-account-keys.png)

Copie o valor após *contas/* do campo **URL** - `https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`

> [!NOTE]
> Todos os registros DNS de webhook e agentservice foram atualizados para o novo estilo registros DNS para dar suporte ao link privado. Para registros DNS JRDS, há suporte para registros DNS antigos e novos de estilo. Se você não estiver usando o link privado, verá os registros DNS de estilo antigo, enquanto aqueles que usam o link privado verão o novo estilo dos registros DNS.

Recomendamos que você use os endereços listados ao definir [exceções](../automation-runbook-execution.md#exceptions). Para obter uma lista de endereços IP de região em vez de nomes de região, baixe o arquivo JSON do centro de download da Microsoft para os seguintes ambientes de nuvem:

* [Intervalos de IP do Azure e marcas de serviço – público do Azure](https://www.microsoft.com/download/details.aspx?id=56519)
* [Intervalos de IP do Azure e marcas de serviço – Azure governamental](https://www.microsoft.com/download/details.aspx?id=57063)
* [Intervalos de IP do Azure e marcas de serviço – Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)
* [Intervalos de IP do Azure e marcas de serviço – Azure China vianet 21](https://www.microsoft.com/download/details.aspx?id=57062)

O arquivo de endereço IP lista os intervalos de endereços IP que são usados nos datacenters do Microsoft Azure. Isso inclui intervalos de computação, SQL e armazenamento, e reflete os intervalos atualmente implementados e quaisquer alterações futuras nos intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana.

É uma boa ideia fazer o download do novo arquivo de endereço IP toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure.

> [!NOTE]
> Se você estiver usando o Azure ExpressRoute, lembre-se de que o arquivo de endereço IP é usado para atualizar o anúncio BGP (Border Gateway Protocol) do espaço do Azure na primeira semana de cada mês.

## <a name="next-steps"></a>Próximas etapas

* Para saber como solucionar problemas de seus Hybrid Runbook Workers, veja [Solucionar Problemas do Hybrid Runbook Worker](../troubleshoot/hybrid-runbook-worker.md#general).

* Para saber como solucionar problemas com a configuração de estado, consulte [solucionar problemas de configuração de estado](../troubleshoot/desired-state-configuration.md).