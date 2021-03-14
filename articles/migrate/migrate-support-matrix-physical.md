---
title: Suporte para avaliação de servidor físico nas migrações para Azure
description: Saiba mais sobre o suporte para avaliação de servidor físico com a avaliação de servidor de migrações para Azure
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: cb5a1a51a7d622c1b0a605d155ade2f08022ab67
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592485"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matriz de suporte para avaliação do servidor físico 

Este artigo resume os pré-requisitos e requisitos de suporte ao avaliar servidores físicos para migração para o Azure, usando a ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Se você quiser migrar servidores físicos para o Azure, examine a [matriz de suporte de migração](migrate-support-matrix-physical-migration.md).


Para avaliar servidores físicos, você cria um projeto de migrações para Azure e adiciona a ferramenta de avaliação do servidor ao projeto. Depois que a ferramenta for adicionada, implante o [dispositivo do Migrações para Azure](migrate-appliance.md). O dispositivo continuamente descobre computadores locais e envia metadados e dados de desempenho do computador para o Azure. Após concluir a descoberta, reúna os computadores descobertos em grupos e execute uma avaliação para um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 servidores físicos em um único [projeto de migrações para Azure](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Além dos servidores físicos, um projeto pode incluir VMs VMware e VMs do Hyper-V, até os limites de avaliação de cada uma.
**Discovery** | O dispositivo de migrações para Azure pode descobrir até 1000 servidores físicos.
**Avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.<br/><br/> Você pode avaliar até 35.000 computadores em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.

## <a name="physical-server-requirements"></a>Requisitos de servidor físico

**Implantação de servidor físico:** O servidor físico pode ser autônomo ou implantado em um cluster.

**Sistema operacional:** Todos os sistemas operacionais Windows e Linux podem ser avaliados quanto à migração.

**Permissões:**
- Para servidores Windows, use uma conta de domínio para computadores ingressados no domínio e uma conta local para computadores que não são ingressados no domínio. A conta de usuário deve ser adicionada a estes grupos: Usuários de Gerenciamento Remoto, Usuários do Monitor de Desempenho e Usuários do Log de Desempenho.
- Para os servidores Linux, você precisa de uma conta raiz nos servidores Linux que deseja descobrir. Como alternativa, é possível definir uma conta não raiz com as funcionalidades necessárias usando os seguintes comandos:

**Comando** | **Finalidade**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(se /usr/sbin/fdisk não estiver presente)_ | Para coletar dados de configuração de disco
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Para coletar dados de desempenho do disco
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Para coletar o número de série do BIOS
chmod a+r /sys/class/dmi/id/product_uuid | Para coletar o GUID do BIOS



## <a name="azure-migrate-appliance-requirements"></a>Requisitos de dispositivo para as Migrações para Azure

As Migrações para Azure usam o dispositivo das [Migrações para Azure](migrate-appliance.md) para descoberta e avaliação. O dispositivo para servidores físicos pode ser executado em uma VM ou em um computador físico. 

- Saiba mais sobre [os requisitos de dispositivo](migrate-appliance.md#appliance---physical) para servidores físicos.
- Saiba mais sobre as URLs do Azure que o dispositivo precisa acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
- Você define o dispositivo usando um [script do PowerShell](how-to-set-up-appliance-physical.md) que você baixa do portal do Azure.
No Azure governamental, implante o dispositivo [usando esse script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Acesso à porta

A tabela a seguir resume os requisitos de porta para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Dispositivo** | Conexões de entrada na porta TCP 3389, para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Conexões de entrada na porta 44368, para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Conexões de saída nas portas 443 (HTTPS), para enviar metadados de descoberta e desempenho para migrações para Azure.
**Servidores físicos** | **Windows:** Conexão de entrada na porta WinRM 5985 (HTTP) ou 5986 (HTTPS) para efetuar pull de metadados de configuração e desempenho de servidores Windows. <br/><br/> **Linux:**  Conexões de entrada na porta 22 (TCP) para efetuar pull de metadados de configuração e desempenho de servidores Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos da análise de dependência baseada em agente

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre os computadores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agente. Atualmente, somente a análise de dependência baseada em agente tem suporte para servidores físicos.

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Você deve ter um projeto de Migrações para Azure em vigor, com a ferramenta de Avaliação do Servidor adicionada ao projeto.<br/><br/>  Implante a visualização de dependência depois de configurar um dispositivo de Migrações para Azure para descobrir os computadores locais<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/> Aprenda a configurar o dispositivo de Migrações para Azure para avaliação de [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) ou servidores físicos.
**Azure Governamental** | A visualização de dependência não está disponível no Azure Governamental.
**Log Analytics** | As Migrações para Azure usam a solução [Mapa do Serviço](../azure-monitor/vm/service-map.md) nos [logs do Azure Monitor](../azure-monitor/logs/log-query-overview.md) para visualização de dependência.<br/><br/> Associe um workspace do Log Analytics novo ou existente a um projeto de Migrações para Azure. Não é possível modificar o espaço de trabalho de um projeto de Migrações para Azure depois que ele foi adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura que o projeto de Migrações para Azure.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou oeste da Europa. Os espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> No Log Analytics, o espaço de trabalho associado às Migrações para Azure é marcado com a chave do Projeto de Migração e o nome do projeto.
**Agentes necessários** | Em cada computador que você deseja analisar, instale os seguintes agentes:<br/><br/> O [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> O [agente de dependência](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Se os computadores locais não estiverem conectados com a Internet, será necessário baixar e instalar o gateway do Log Analytics.<br/><br/> Saiba mais sobre a instalação do [agente de dependência](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de Trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura que o projeto de Migrações para Azure.<br/><br/> As Migrações para Azure são compatível com os espaços de trabalho que residem nas regiões leste dos EUA, sudeste da Ásia e oeste da Europa.<br/><br/>  O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Não é possível modificar o espaço de trabalho de um projeto de Migrações para Azure depois que ele foi adicionado.
**Custos** | A solução Mapa do Serviço não gera encargos nos primeiros 180 dias (desde o dia da associação do workspace do Log Analytics com o projeto de Migrações para Azure)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> O uso de qualquer solução que não seja o Mapa do Serviço no workspace do Log Analytics associado gerará [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics Standard.<br/><br/> Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa do Serviço não será mais gratuito e cada nó será cobrado de acordo com a camada paga do workspace do Log Analytics/<br/><br/>Se você tiver projetos criados antes da disponibilidade geral de Migrações para Azure (disponibilidade geral - 28 de fevereiro de 2018), pode ter gerado encargos adicionais do Mapa do Serviço. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes da disponibilidade geral ainda são passíveis de cobrança.
**Gerenciamento** | Ao registrar agentes no espaço de trabalho, use a ID e a chave fornecidas pelo projeto de Migrações para Azure.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto de Migrações para Azure associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/logs/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado por Migrações para Azure, a menos que exclua o projeto de Migrações para Azure. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se os computadores não estiverem conectados com a Internet, será necessário instalar o gateway do Log Analytics.
**Azure Governamental** | A análise de dependência baseada em agente não é compatível.

## <a name="next-steps"></a>Próximas etapas

[Prepare-se para a avaliação do servidor físico](./tutorial-discover-physical.md).