---
title: Suporte para avaliação do Hyper-V em migrações para Azure
description: Saiba mais sobre o suporte à avaliação do Hyper-V com a avaliação do servidor de migrações para Azure
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 5b5c85b599f02cedc3bb1bda84c28ef2169c8e2d
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754038"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matriz de suporte para avaliação do Hyper-V

Este artigo resume os pré-requisitos e requisitos de suporte ao avaliar as VMs do Hyper-V para migração para o Azure, usando a ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Se você quiser migrar VMs do Hyper-V para o Azure, examine a [matriz de suporte de migração](migrate-support-matrix-hyper-v-migration.md).

Para configurar a avaliação de VM do Hyper-V, você cria um projeto de migrações para Azure e adiciona a ferramenta de avaliação do servidor ao projeto. Depois que a ferramenta for adicionada, implante o [dispositivo do Migrações para Azure](migrate-appliance.md). O dispositivo continuamente descobre computadores locais e envia metadados e dados de desempenho do computador para o Azure. Após concluir a descoberta, reúna os computadores descobertos em grupos e execute uma avaliação para um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 VMs do Hyper-V em um único [projeto de migrações para Azure](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Além das VMs do Hyper-V, um projeto pode incluir VMs VMware e servidores físicos, até os limites de avaliação de cada um.
**Discovery** | O dispositivo de migrações para Azure pode descobrir até 5000 VMs do Hyper-V.<br/><br/> O dispositivo pode se conectar a até 300 hosts Hyper-V.
**Avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.<br/><br/> Você pode avaliar até 35.000 VMs em uma única avaliação para um grupo.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.



## <a name="hyper-v-host-requirements"></a>Requisitos de host do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Host do Hyper-V**       | O host Hyper-V pode ser autônomo ou implantado em um cluster.<br/><br/> O host Hyper-V pode executar o Windows Server 2019, o Windows Server 2016 ou o Windows Server 2012 R2. Também há suporte para a instalação do Server Core desses sistemas operacionais. <br/>Não é possível avaliar as VMs localizadas em hosts Hyper-V que executam o Windows Server 2012.
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. <br/> Se você não quiser atribuir permissões de administrador, crie uma conta de usuário local ou de domínio e adicione a conta de usuário a esses grupos – usuários de gerenciamento remoto, administradores do Hyper-V e usuários de monitor de desempenho. |
| **Comunicação remota do PowerShell**   | A [comunicação remota do PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) deve ser habilitada em cada host Hyper-V. |
| **Réplica do Hyper-V**       | Se você usar a réplica do Hyper-V (ou se tiver várias VMs com os mesmos identificadores de VM) e descobrir as VMs originais e replicadas usando as migrações para Azure, a avaliação gerada pelas migrações para Azure pode não ser precisa. |


## <a name="vm-requirements"></a>Requisitos de VM

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais podem ser avaliados quanto à migração.  |
| **Serviços de Integração**       | Os [Integration Services do Hyper-V](/virtualization/hyper-v-on-windows/reference/integration-services) devem estar em execução em VMs que você avaliar, a fim de capturar informações do sistema operacional. |
| **Storage** | Disco local, DAS, JBOD, espaços de armazenamento, CSV, SMB. Há suporte para esse armazenamento de host Hyper-V no qual VHD/VHDX são armazenados. <br/> Há suporte para os controladores virtuais IDE e SCSI| 

## <a name="azure-migrate-appliance-requirements"></a>Requisitos de dispositivo para as Migrações para Azure

As Migrações para Azure usam o dispositivo das [Migrações para Azure](migrate-appliance.md) para descoberta e avaliação. Você pode implantar o dispositivo usando um VHD do Hyper-V compactado que você baixa do portal ou usando um [script do PowerShell](deploy-appliance-script.md).

- Saiba mais sobre [os requisitos de dispositivo](migrate-appliance.md#appliance---hyper-v) para o Hyper-V.
- Saiba mais sobre as URLs do Azure que o dispositivo precisa acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
- No Azure Governamental, você deve implantar o dispositivo [usando um script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Acesso à porta

A tabela a seguir resume os requisitos de porta para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Dispositivo** | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivos usando a URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Conexões de saída nas portas 443 (HTTPS), para enviar metadados de descoberta e desempenho para migrações para Azure.
**Host/cluster do Hyper-V** | Conexão de entrada na porta do WinRM 5985 (HTTP) ou 5986 (HTTPS) para extrair metadados e dados de desempenho para VMs do Hyper-V usando uma sessão modelo CIM (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos da análise de dependência baseada em agente

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre os computadores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agente. Atualmente, o Hyper-V dá suporte apenas à visualização de dependências baseadas em agente. 

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Você deve ter um projeto de Migrações para Azure em vigor, com a ferramenta de Avaliação do Servidor adicionada ao projeto.<br/><br/>  Implante a visualização de dependência depois de configurar um dispositivo de Migrações para Azure para descobrir os computadores locais<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/> Saiba como configurar o dispositivo de migrações para Azure para avaliação de [VMs do Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Governamental** | A visualização de dependência não está disponível no Azure Governamental.
**Log Analytics** | As Migrações para Azure usam a solução [Mapa do Serviço](../azure-monitor/insights/service-map.md) nos [logs do Azure Monitor](../azure-monitor/log-query/log-query-overview.md) para visualização de dependência.<br/><br/> Associe um workspace do Log Analytics novo ou existente a um projeto de Migrações para Azure. Não é possível modificar o espaço de trabalho de um projeto de Migrações para Azure depois que ele foi adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura que o projeto de Migrações para Azure.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou oeste da Europa. Os espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> No Log Analytics, o espaço de trabalho associado às Migrações para Azure é marcado com a chave do Projeto de Migração e o nome do projeto.
**Agentes necessários** | Em cada computador que você deseja analisar, instale os seguintes agentes:<br/><br/> O [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> O [agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se os computadores locais não estiverem conectados com a Internet, será necessário baixar e instalar o gateway do Log Analytics.<br/><br/> Saiba mais sobre a instalação do [agente de dependência](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de Trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura que o projeto de Migrações para Azure.<br/><br/> As Migrações para Azure são compatível com os espaços de trabalho que residem nas regiões leste dos EUA, sudeste da Ásia e oeste da Europa.<br/><br/>  O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> Não é possível modificar o espaço de trabalho de um projeto de Migrações para Azure depois que ele foi adicionado.
**Custos** | A solução Mapa do Serviço não gera encargos nos primeiros 180 dias (desde o dia da associação do workspace do Log Analytics com o projeto de Migrações para Azure)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> O uso de qualquer solução que não seja o Mapa do Serviço no workspace do Log Analytics associado gerará [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics Standard.<br/><br/> Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa do Serviço não será mais gratuito e cada nó será cobrado de acordo com a camada paga do workspace do Log Analytics/<br/><br/>Se você tiver projetos criados antes da disponibilidade geral de Migrações para Azure (disponibilidade geral - 28 de fevereiro de 2018), pode ter gerado encargos adicionais do Mapa do Serviço. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes da disponibilidade geral ainda são passíveis de cobrança.
**Gerenciamento** | Ao registrar agentes no espaço de trabalho, use a ID e a chave fornecidas pelo projeto de Migrações para Azure.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto de Migrações para Azure associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado por Migrações para Azure, a menos que exclua o projeto de Migrações para Azure. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se os computadores não estiverem conectados com a Internet, será necessário instalar o gateway do Log Analytics.
**Azure Governamental** | A análise de dependência baseada em agente não é compatível.

## <a name="next-steps"></a>Próximas etapas

[Preparar a avaliação de VM do Hyper-V](./tutorial-discover-hyper-v.md)