---
title: Suporte para avaliação do Hyper-V em migrações para Azure
description: Saiba mais sobre o suporte para a avaliação do Hyper-V com a descoberta e avaliação de migrações para Azure
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 8531d0b2252e6ddff75509046b5a4576b99d339f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870782"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matriz de suporte para avaliação do Hyper-V

Este artigo resume os pré-requisitos e requisitos de suporte ao descobrir e avaliar servidores locais em execução em um ambiente Hyper-V para migração para o Azure, usando a ferramenta [migrações para Azure: descoberta e avaliação](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) . Se você quiser migrar servidores em execução no Hyper-V para o Azure, examine a [matriz de suporte de migração](migrate-support-matrix-hyper-v-migration.md).

Para configurar a descoberta e a avaliação de servidores em execução no Hyper-V, você cria um projeto e adiciona a ferramenta migrações do Azure: descoberta e avaliação ao projeto. Depois que a ferramenta for adicionada, implante o [dispositivo do Migrações para Azure](migrate-appliance.md). O dispositivo descobre continuamente servidores locais e envia metadados de servidor e dados de desempenho para o Azure. Após a conclusão da descoberta, você coleta servidores descobertos em grupos e executa uma avaliação de um grupo.

## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 servidores em um único [projeto](migrate-support-matrix.md#project).
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Além dos servidores no Hyper-V, um projeto pode incluir servidores em servidores físicos e VMware, até os limites de avaliação de cada um.
**Discovery** | O dispositivo de migrações para Azure pode descobrir até 5000 servidores em execução no Hyper-V.<br/><br/> O dispositivo pode se conectar a até 300 hosts Hyper-V.
**Avaliação** | Você pode adicionar até 35.000 servidores em um único grupo.<br/><br/> Você pode avaliar até 35.000 servidores em uma única avaliação para um grupo.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.

## <a name="hyper-v-host-requirements"></a>Requisitos de host do Hyper-V

| **Suporte**                | **Detalhes**
| :-------------------       | :------------------- |
| **Host do Hyper-V**       | O host Hyper-V pode ser autônomo ou implantado em um cluster.<br/><br/> O host Hyper-V pode executar o Windows Server 2019, o Windows Server 2016 ou o Windows Server 2012 R2. Também há suporte para as instalações Server Core desses sistemas operacionais. <br/>Não é possível avaliar servidores localizados em hosts Hyper-V que executam o Windows Server 2012.
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. <br/> Se você não quiser atribuir permissões de administrador, crie uma conta de usuário local ou de domínio e adicione a conta de usuário a esses grupos – usuários de gerenciamento remoto, administradores do Hyper-V e usuários de monitor de desempenho. |
| **Comunicação remota do PowerShell**   | A [comunicação remota do PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting) deve ser habilitada em cada host Hyper-V. |
| **Réplica do Hyper-V**       | Se você usar a réplica do Hyper-V (ou se tiver vários servidores com os mesmos identificadores de servidor) e descobrir os servidores originais e replicados usando as migrações para Azure, a avaliação gerada pelas migrações para Azure pode não ser precisa. |

## <a name="server-requirements"></a>Requisitos de servidor

| **Suporte**                  | **Detalhes**
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais podem ser avaliados quanto à migração.  |
| **Serviços de Integração**       | Os [Integration Services do Hyper-V](/virtualization/hyper-v-on-windows/reference/integration-services) devem estar em execução em servidores que você avaliar, a fim de capturar as informações do sistema operacional. |
| **Storage** | Disco local, DAS, JBOD, espaços de armazenamento, CSV, SMB. Esses armazenamentos de host do Hyper-V nos quais o VHD/VHDX está armazenado têm suporte. <br/> Há suporte para os controladores virtuais IDE e SCSI|

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
**Host/cluster do Hyper-V** | Conexão de entrada na porta WinRM 5985 (HTTP) ou 5986 (HTTPS) para efetuar pull de metadados e dados de desempenho para servidores no Hyper-V, usando uma sessão modelo CIM (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos da análise de dependência baseada em agente

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre servidores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agente. Atualmente, o Hyper-V dá suporte apenas à visualização de dependências baseadas em agente.

**Requisito** | **Detalhes**
--- | --- 
**Antes da implantação** | Você deve ter um projeto em vigor, com a ferramenta migrações para Azure: descoberta e avaliação adicionada ao projeto.<br/><br/>  Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir seus servidores locais.<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar a migração do Azure: ferramenta de descoberta e avaliação a um projeto existente.<br/> Saiba como configurar o dispositivo para descoberta e avaliação de [servidores no Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Governamental** | A visualização de dependência não está disponível no Azure Governamental.
**Log Analytics** | As Migrações para Azure usam a solução [Mapa do Serviço](../azure-monitor/vm/service-map.md) nos [logs do Azure Monitor](../azure-monitor/logs/log-query-overview.md) para visualização de dependência.<br/><br/> Você associa um espaço de trabalho Log Analytics novo ou existente a um projeto. O espaço de trabalho para um projeto não pode ser modificado após ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura que o projeto.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou oeste da Europa. Os espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> No Log Analytics, o espaço de trabalho associado às Migrações para Azure é marcado com a chave do Projeto de Migração e o nome do projeto.
**Agentes necessários** | Em cada servidor que você deseja analisar, instale os seguintes agentes:<br/><br/> O [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> O [agente de dependência](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Se os servidores locais não estiverem conectados à Internet, você precisará baixar e instalar Log Analytics gateway neles.<br/><br/> Saiba mais sobre a instalação do [agente de dependência](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de Trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura que o projeto.<br/><br/> As Migrações para Azure são compatível com os espaços de trabalho que residem nas regiões leste dos EUA, sudeste da Ásia e oeste da Europa.<br/><br/>  O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> O espaço de trabalho para um projeto não pode ser modificado após ser adicionado.
**Custos** | A solução Mapa do Serviço não incorrerá em cobranças pelos primeiros 180 dias (a partir do dia em que você associa o espaço de trabalho Log Analytics ao projeto)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> O uso de qualquer solução que não seja o Mapa do Serviço no workspace do Log Analytics associado gerará [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics Standard.<br/><br/> Quando o projeto é excluído, o espaço de trabalho não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa do Serviço não será mais gratuito e cada nó será cobrado de acordo com a camada paga do workspace do Log Analytics/<br/><br/>Se você tiver projetos criados antes da disponibilidade geral de Migrações para Azure (disponibilidade geral - 28 de fevereiro de 2018), pode ter gerado encargos adicionais do Mapa do Serviço. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes da disponibilidade geral ainda são passíveis de cobrança.
**Gerenciamento** | Ao registrar agentes no espaço de trabalho, você usa a ID e a chave fornecidas pelo projeto.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/logs/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado pelas migrações para Azure, a menos que você exclua o projeto. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se os servidores não estiverem conectados à Internet, você precisará instalar o Log Analytics gateway neles.
**Azure Governamental** | A análise de dependência baseada em agente não é compatível.

## <a name="next-steps"></a>Próximas etapas

[Prepare-se para a avaliação de servidores em execução no Hyper-V](./tutorial-discover-hyper-v.md).