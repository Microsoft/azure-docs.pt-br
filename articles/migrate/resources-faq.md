---
title: Migrações para Azure - Perguntas frequentes (FAQ) | Microsoft Docs
description: Apresenta respostas para perguntas frequentes sobre Migrações para Azure
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: snehaa
ms.openlocfilehash: 0708502087ae6880d9559cf17f0ba9982b2ba040
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372477"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Migrações para Azure - Perguntas frequentes (FAQ)

Este artigo contém perguntas frequentes sobre o Migrações para Azure. Se você tiver mais perguntas após a leitura deste artigo, publique-as no [fórum do Migrações para Azure](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Geral

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Em quais regiões geográficas do Azure há suporte das Migrações para Azure?

Você pode encontrar a lista para [VMware aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e para o [Hyper-V aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Como o Migrações para Azure é diferente do Azure Site Recovery?

As migrações para Azure fornecem um hub centralizado para iniciar, executar e acompanhar a descoberta, a avaliação e a migração de máquinas e cargas de trabalho para o Azure. [Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) é uma solução de recuperação de desastre. A migração de servidor de migrações para Azure aproveita Azure Site Recovery no back-end para habilitar os cenários de migração para a migração de comparação entre computadores locais e com o deslocamento de precisão.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Dispositivo de migrações para Azure (servidores VMware/físicos)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Como o dispositivo de migrações para Azure se conecta ao Azure?

A conexão pode ser pela Internet ou você pode usar o ExpressRoute com emparelhamento público/Microsoft.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Quais requisitos de conectividade de rede são necessários para a migração e avaliação do servidor de migrações para Azure

Para as URLs e portas necessárias para que as migrações para Azure se comuniquem com o Azure, examine as matrizes de suporte do [VMware](migrate-support-matrix-vmware.md) e [Hyper-V](migrate-support-matrix-hyper-v.md) .

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>Posso proteger a VM do dispositivo que configurei com o modelo?

Os componentes adicionais (por exemplo, antivírus) podem ser adicionados ao modelo, desde que as regras de comunicação e de firewall necessárias para o dispositivo de migrações para Azure sejam deixadas como estão.   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Quais dados são coletados pelo dispositivo de migrações para Azure?

Você pode obter detalhes sobre os dados coletados pelo dispositivo de migrações para Azure [aqui](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Há algum impacto no desempenho sobre o ambiente analisado do VMware ou do Hyper-V?

Com a criação de perfil contínua de dados de desempenho, o dispositivo de migrações para Azure cria perfis de computadores locais para medir os dados de desempenho da VM. Isso tem um impacto de desempenho quase zero nos hosts Hyper-V/ESXi, bem como no vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Onde e por quanto tempo os dados coletados são armazenados?

Os dados coletados pelo dispositivo de migrações para Azure são armazenados no local do Azure que você especificar ao criar o projeto de migração. Os dados são armazenados com segurança em uma assinatura da Microsoft e são excluídos quando você exclui o projeto de migrações para Azure.

Para a visualização de dependência, se você instalar agentes nas VMs, os dados coletados pelos agentes de dependência serão armazenados nos EUA, em um espaço de trabalho Log Analytics criado na assinatura do Azure. Esses dados são excluídos quando você exclui o espaço de trabalho do Log Analytics na sua assinatura. [Saiba mais](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>Qual é o volume de dados carregados pelo dispositivo de migrações para Azure durante a criação de perfil contínua?

O volume de dados enviados para migrações para Azure varia de acordo com vários parâmetros. Para dar um número de indícios, um projeto de migrações para Azure com 10 computadores (cada um com um disco e uma NIC), envia cerca de 50 MB por dia. Esse é um valor aproximado, que é alterado com base no número de pontos de dados para NICs e discos (os dados enviados não são lineares se o número de computadores, NICs ou discos aumenta).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Os dados são criptografados em repouso e em trânsito?

Sim para ambos. Os metadados são enviados com segurança para o serviço de migrações para Azure pela Internet, via HTTPS. Os metadados são armazenados em um [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)e no [armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) em uma assinatura da Microsoft e são criptografados em repouso.

Os dados coletados pelos agentes de dependência também são criptografados em trânsito (HTTPS seguro) e armazenados em um espaço de trabalho Log Analytics na assinatura do usuário. Ele também é criptografado em repouso.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Como o dispositivo de migrações para Azure se comunica com o vCenter Server e o serviço de migrações para Azure?

O dispositivo se conecta à vCenter Server (porta 443) usando as credenciais fornecidas quando você configura o dispositivo. Ele consulta o vCenter Server usando o VMware PowerCLI para coletar metadados sobre as VMs gerenciadas pelo vCenter Server. Ele coleta os dados de configuração sobre VMs (núcleos, memória, discos, NIC, etc.), bem como o histórico de desempenho de cada VM no último mês. Os metadados coletados são enviados para a avaliação do servidor de migrações para Azure (pela Internet via HTTPS) para avaliação.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Posso conectar o mesmo dispositivo a vários servidores vCenter?

Sim, um único dispositivo de migrações para Azure pode ser usado para descobrir vários servidores vCenter, mas não simultaneamente. Você precisa executar descobertas uma após a outra.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Alterei o tamanho da máquina. Posso executar novamente uma avaliação?

O dispositivo de migração do Azure coleta continuamente informações sobre o ambiente local. No entanto, uma avaliação é um instantâneo pontual de VMs locais. Se você alterar as configurações em uma VM que deseja avaliar, use a opção ' recalcular ' para atualizar a avaliação com as alterações mais recentes.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>Como posso descobrir um ambiente multilocatário na avaliação do servidor de migrações para Azure?

Para o VMware, se você tiver um ambiente compartilhado entre locatários e não quiser descobrir as VMs de um locatário na assinatura de outro locatário, crie vCenter Server credenciais com acesso somente a essas VMs que você deseja descobrir. Em seguida, use as credenciais quando iniciar desativar a descoberta no dispositivo de migrações para Azure.

Para o Hyper-V, a descoberta usa credenciais de host do Hyper-V, se as VMs compartilharem o mesmo host do Hyper-V, atualmente não há nenhuma maneira de separar a descoberta.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Quantas VMs podem ser descobertas usando um único dispositivo de migração?

Você pode descobrir até 10.000 VMs VMware e até 5.000 VMs Hyper-V usando um único dispositivo de migração.  Se você tiver mais computadores no seu ambiente local, saiba como dimensionar o [Hyper-V](scale-hyper-v-assessment.md) e a avaliação do [VMware](scale-vmware-assessment.md) .

## <a name="azure-migrate-server-assessment"></a>Migrações para Azure: Avaliação de Servidor

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>O Azure migra: Avaliação de suporte de avaliação do servidor de servidores físicos?

Não, as migrações para Azure atualmente não dão suporte à avaliação de servidores físicos.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>O Migrações para Azure precisa do vCenter Server para descobrir um ambiente VMware?

Sim, as migrações para Azure precisam vCenter Server para descobrir um ambiente VMware. Ele não dá suporte à descoberta de hosts ESXi que não são gerenciados pelo vCenter Server.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre usar as migrações para Azure: Avaliação do servidor e o MAP Toolkit?

Migrações para Azure: A avaliação do servidor fornece avaliação de migração para ajudar com a preparação da migração e a avaliação de cargas de trabalho para migração para o Azure. [O kit de ferramentas de avaliação e planejamento da Microsoft (MAP)](https://www.microsoft.com/download/details.aspx?id=7826) tem outras funcionalidades, como o planejamento de migração para versões mais recentes de sistemas operacionais de cliente e servidor Windows e o acompanhamento de uso de software. Para esses cenários, continue a usar o Kit de ferramentas MAP.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Como migrações para Azure: A avaliação do servidor é diferente da Planejador de Implantações do Azure Site Recovery?

Migrações para Azure: A avaliação do servidor é uma ferramenta de planejamento de migração. Planejador de Implantações do Azure Site Recovery é uma ferramenta de planejamento de recuperação de desastre.

- **Migração do VMware/Hyper-V para o Azure**: Se você pretende migrar seus servidores locais para o Azure, use a migração do Azure: Ferramenta de avaliação de servidor para planejamento de migração. A ferramenta avalia as cargas de trabalho locais e fornece orientações, ideias e mecanismos para ajudá-lo a migrar para o Azure. Quando estiver pronto com seu plano de migração, você poderá usar ferramentas como migrações para Azure: Migração de servidor, para migrar os computadores para o Azure.
- **Recuperação de desastre do VMware/Hyper-V para o Azure**: Para a recuperação de desastre no Azure usando Site Recovery, use o Planejador de Implantações de Site Recovery para o planejamento de recuperação de desastre. Site Recovery Planejador de Implantações faz uma avaliação profunda Site Recovery específica de seu ambiente local. Ele fornece recomendações necessárias por Site Recovery para operações de desastres bem-sucedidas, como replicação e failover de VMs.

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>As migrações para Azure dão suporte à estimativa de custo com base em Enterprise Agreement (EA)?

Atualmente, as migrações para Azure não dão suporte à estimativa de custo para [Enterprise Agreement oferta](https://azure.microsoft.com/offers/enterprise-agreement-support/). A solução alternativa é especificar o pré-pago como a oferta e especificar manualmente a porcentagem de desconto (aplicável à assinatura) no campo ' desconto ' das propriedades de avaliação.

  ![Desconto](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Qual é a diferença entre o dimensionamento com base no dimensionamento e no desempenho no local?

- No como o dimensionamento local, as migrações para Azure não consideram os dados de desempenho da VM. Ele dimensiona as VMs com base na configuração local. -Em dimensionamento baseado em desempenho, o dimensionamento é baseado nos dados de utilização.
- Por exemplo, se uma VM local tiver 4 núcleos e 8 GB de memória com 50% de utilização da CPU e 50% de utilização de memória, como o dimensionamento local recomendará uma SKU de VM do Azure com quatro núcleos e 8 GB de memória. O dimensionamento baseado em desempenho, no entanto, recomenda um SKU de VM de dois núcleos e 4 GB, pois a porcentagem de utilização é considerada.
- Da mesma forma, o dimensionamento de disco depende de duas propriedades de avaliação – critérios de dimensionamento e tipo de armazenamento.
= Se o critério de dimensionamento for baseado em desempenho e o tipo de armazenamento for automático, os valores de IOPS e taxa de transferência do disco serão considerados ao identificar o tipo de disco de destino (Standard ou Premium).
- Se o critério de dimensionamento for baseado em desempenho e o tipo de armazenamento for Premium, um disco Premium será recomendado. A SKU do disco Premium é selecionada com base no tamanho do disco local. A mesma lógica é usada para fazer o dimensionamento do disco quando o critério de dimensionamento é o dimensionamento local e o tipo de armazenamento é Standard ou Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Que impacto que a utilização do percentil e o histórico de desempenho têm sobre as recomendações de tamanho?

Essas propriedades só são aplicáveis ao dimensionamento com base no desempenho.

- As migrações para Azure coletam o histórico de desempenho de máquinas locais e as usam para recomendar o tamanho da VM e o tipo de disco no Azure.
- O dispositivo cria o perfil continuamente no ambiente local para coletar dados de utilização em tempo real a cada 20 segundos. O dispositivo acumula as amostras de 20 segundos e cria um único ponto de dados a cada 15 minutos. Para criar o ponto de dados único, o dispositivo seleciona o valor de pico de todas as amostras de 20 segundos e envia-o para o Azure.
- Quando você cria uma avaliação no Azure (com base no valor de duração do desempenho e do percentual do histórico de desempenho), as migrações para Azure calculam o valor efetivo de utilização e as usam para o dimensionamento.
- Por exemplo, se você definir a duração do desempenho como um dia e o valor do percentil como 95 percentil, as migrações para Azure usarão os pontos de exemplo de 15 minutos enviados pelo coletor para o último dia, os classifica em ordem crescente e escolhe o valor de 95 º percentil como o utilização efetiva.
- O valor do 95 º percentil garante que você está ignorando as exceções, o que pode ocorrer se você usar o 99 º percentil. Se você deseja escolher o pico de uso para o período e não quiser perder exceções, selecione o 99 º percentil.

### <a name="what-is-dependency-visualization"></a>O que é a visualização de dependência?

A visualização de dependências permite que você avalie grupos de VMs para migração com maior confiança. Ele faz verificações entre as dependências de computador antes de executar uma avaliação. A visualização de dependência ajuda a garantir que nada seja deixado para trás e evite interrupções inesperadas ao migrar para o Azure. As migrações para Azure aproveitam a solução Mapa do Serviço nos logs de Azure Monitor, para habilitar a visualização de dependência.

> [!NOTE]
> A funcionalidade de visualização de dependências não está disponível no Azure Governamental.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>É necessário pagar para usar o recurso de visualização de dependência?

Nº [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços das Migrações para Azure.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>É necessário instalar alguma coisa para a visualização de dependência?

Para usar a visualização de dependência, você precisa fazer o download e instalar agentes em cada computador local que você deseja avaliar.

- O [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) precisa ser instalado em cada computador.
- O [Agente de Dependência](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) precisa ser instalado em cada máquina.
- Além disso, se você tiver máquinas sem conectividade com a Internet, será necessário fazer o download e instalar o gateway do Log Analytics nelas.

Você não precisa desses agentes, a menos que esteja usando a visualização de dependência.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Posso usar um workspace existente para visualização de dependência?

Sim, você pode anexar um espaço de trabalho existente ao projeto de migração e aproveitá-lo para visualização de dependência. [Saiba mais](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização de dependência?

Não, a visualização de dependência não pode ser exportada. No entanto, como as Migrações para Azure usam o Mapa do Serviço para visualização de dependência, você pode usar as [APIs REST do Mapa do Serviço](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) para obter as dependências em um formato json.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Como posso automatizar a instalação do Microsoft Monitoring Agent (MMA) e do agente de dependência?

[Use esse script](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) para a instalação dos agentes. [Siga estas instruções](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) para instalar o MMA usando a linha de comando ou automação. Para MMA, aproveite [esse script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Além de scripts, você pode usar ferramentas de implantação como System Center Configuration Manager, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) , etc. para implantar os agentes.

### <a name="what-operating-systems-are-supported-by-mma"></a>Quais sistemas operacionais são compatíveis com o MMA?

- [Examine](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) a lista de sistemas operacionais Windows com suporte do MMA.
- [Examine](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) a lista de sistemas operacionais Linux com suporte do MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Quais são os sistemas operacionais com suporte do Dependency Agent?

[Examine](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) os sistemas operacionais Windows com suporte pelo Dependency Agent.
[Examine](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) a lista de sistemas operacionais Linux com suporte pelo Dependency Agent.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Posso Visualizar dependências no Azure migrar por mais de uma hora?
Não, você pode visualizar dependências por até uma hora. Você pode voltar para uma data específica no histórico, até o último mês, mas a duração máxima da visualização é de uma hora. Por exemplo, você pode usar a duração de tempo no mapa de dependências para exibir dependências de ontem, mas só pode exibi-la para uma janela de uma hora. No entanto, você pode usar os logs de Azure Monitor para [consultar dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>A visualização de dependência tem suporte para grupos com mais de 10 VMs?
Você pode [Visualizar dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) com até 10 VMS. Se você tiver um grupo com mais de 10 VMs, recomendamos dividir o grupo em grupos menores e, em seguida, Visualizar as dependências.

## <a name="azure-migrate-server-migration"></a>Migrações para Azure: Migração de Servidor

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Como migrações para Azure: A migração do servidor é diferente da Azure Site Recovery?

Migrações para Azure: A migração do servidor aproveita o mecanismo de replicação do Site Recovery para a migração baseada em agente de VMs VMware, a migração de VMs do Hyper-V e a migração de servidores físicos para o Azure. A opção sem agente para migrar VMs VMware é criada nativamente na migração de servidor.

## <a name="next-steps"></a>Próximas etapas
Leia a [visão geral do Migrações para Azure](migrate-services-overview.md)
