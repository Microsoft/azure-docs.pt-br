---
title: Perguntas sobre descoberta, avaliação e análise de dependência em migrações para Azure
description: Obtenha respostas para perguntas comuns sobre descoberta, avaliação e análise de dependência em migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 944d867ef888e70faa659adcc0e2d4c02f003c97
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567407"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Descoberta, avaliação e análise de dependência-perguntas comuns

Este artigo responde a perguntas comuns sobre descoberta, avaliação e análise de dependência em migrações para Azure. Se você tiver outras dúvidas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre as migrações para Azure
- Perguntas sobre o [dispositivo migrações para Azure](common-questions-appliance.md)
- Perguntas sobre a [migração do servidor](common-questions-server-migration.md)
- Obter perguntas respondidas no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Quais geografias têm suporte para descoberta e avaliação com migrações para Azure?

Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Quantas VMs posso descobrir com um dispositivo?

Você pode descobrir até 10.000 VMs VMware, até 5.000 VMs Hyper-V e até 1000 servidores físicos usando um único dispositivo. Se você tiver mais computadores, leia sobre como [dimensionar uma avaliação do Hyper-V](scale-hyper-v-assessment.md), [dimensionando uma avaliação do VMware](scale-vmware-assessment.md)ou [dimensionando uma avaliação de servidor físico](scale-physical-assessment.md).

## <a name="how-do-i-choose-the-assessment-type"></a>Como fazer escolher o tipo de avaliação?

- Use as **avaliações de VM do Azure** quando desejar avaliar suas [VMs VMware](how-to-set-up-appliance-vmware.md)locais, VMS do [Hyper-V](how-to-set-up-appliance-hyper-v.md)e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para VMs do Azure. [Saiba mais](concepts-assessment-calculation.md)

- Use as avaliações da **AVS (solução do Azure VMware)** quando desejar avaliar suas [VMs VMware](how-to-set-up-appliance-vmware.md) locais para migração para a [solução VMware do Azure (AVS)](../azure-vmware/introduction.md) usando esse tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

- Você pode usar um grupo comum com computadores VMware somente para executar os dois tipos de avaliações. Se você estiver executando avaliações da AVS nas Migrações para Azure pela primeira vez, recomendamos criar um novo grupo de computadores VMware.
 

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Por que os dados de desempenho estão ausentes para algumas/todas VMs no meu relatório de avaliação?

Para a avaliação “baseada em desempenho”, o relatório de avaliação exportado indica “PercentageOfCoresUtilizedMissing” ou “PercentageOfMemoryUtilizedMissing” quando o dispositivo de Migrações para Azure não pode coletar dados de desempenho das VMs locais. Verifique:

- Se as VMs estão ativadas pelo tempo para o qual está criando a avaliação
- Se apenas os contadores de memória estiverem ausentes e você estiver tentando avaliar as VMs do Hyper-V. Nesse cenário, habilite a memória dinâmica nas VMs e ' recalcule ' a avaliação para refletir as alterações mais recentes. O dispositivo pode coletar valores de utilização de memória para VMs do Hyper-V somente quando a VM tiver a memória dinâmica habilitada.

- Se todos os contadores de desempenho estiverem ausentes, verifique se as conexões de saída nas portas 443 (HTTPS) são permitidas.

Observação - se algum dos contadores de desempenho estiver ausente, as Migrações para Azure: A avaliação do servidor volta para os núcleos alocados/memória local e recomenda um tamanho de VM de acordo.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Por que a confiança de classificação da minha avaliação é baixa?

A classificação de confiança é calculada para avaliações de "baseadas em desempenho" com base na porcentagem de [pontos de dados disponíveis](./concepts-assessment-calculation.md#ratings) necessária para computar a avaliação. Veja abaixo os motivos pelos quais uma avaliação poderia obter uma classificação de baixa confiança:

- Você não criou o perfil do ambiente pelo tempo para o qual está criando a avaliação. Por exemplo, se você está criando uma avaliação com duração de desempenho definida como uma semana, precisa aguardar pelo menos uma semana após iniciar a descoberta para que todos os pontos de dados sejam coletados. Se não puder esperar tanto tempo, altere a execução para um período menor e “recalcule” a avaliação.
 
- A avaliação do servidor não é capaz de coletar os dados de desempenho de algumas ou de todas as VMs no período de avaliação. Para uma classificação de alta confiança, verifique se: 
    - As VMs são ativadas durante a avaliação
    - Conexões de saída nas portas 443 são permitidas
    - Para VMs do Hyper-V, a memória dinâmica está habilitada 

    “Recalcule” a avaliação para refletir as alterações mais recentes na classificação de confiança.

- Algumas VMs foram criadas após o início da descoberta na Avaliação de Servidor. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente somente há uma semana. Nesse caso, os dados de desempenho das novas VMs não estariam disponíveis durante todo o período e a classificação de confiança seria baixa.

[Saiba mais](./concepts-assessment-calculation.md#confidence-ratings-performance-based) sobre a classificação de confiança.

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Não consigo ver alguns grupos ao criar uma avaliação da AVS (solução do Azure VMware)

- A avaliação da AVS pode ser feita em grupos que têm apenas computadores VMware. Remova os computadores não VMware do grupo se você pretende executar uma avaliação da AVS.
- Se você estiver executando avaliações da AVS nas Migrações para Azure pela primeira vez, recomendamos criar um novo grupo de computadores VMware.

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Não consigo ver alguns tipos de VM no Azure governamental

Os tipos de VM com suporte para avaliação e migração dependem da disponibilidade no local do Azure governamental. Você pode [examinar e comparar](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) os tipos de VM no Azure governamental.

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>O tamanho da minha VM foi alterado. Posso executar uma avaliação novamente?

O dispositivo de migração do Azure coleta continuamente informações sobre o ambiente local.  Uma avaliação é um instantâneo point-in-time de VMs locais. Se você alterar as configurações em uma VM que deseja avaliar, use a opção recalcular para atualizar a avaliação com as alterações mais recentes.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Como fazer descobrir VMs em um ambiente multilocatário?

- **VMware**: se um ambiente for compartilhado entre locatários e você não quiser descobrir as VMs de um locatário na assinatura de outro locatário, crie VMware vCenter Server credenciais que possam acessar somente as VMs que você deseja descobrir. Em seguida, use essas credenciais quando iniciar a descoberta no dispositivo de migrações para Azure.
- **Hyper-v**: a descoberta usa credenciais de host do Hyper-v. Se as VMs compartilharem o mesmo host do Hyper-V, não há como separar a descoberta no momento.  

## <a name="do-i-need-vcenter-server"></a>Preciso de vCenter Server?

Sim, as migrações para Azure exigem vCenter Server em um ambiente VMware para executar a descoberta. As migrações para Azure não dão suporte à descoberta de hosts ESXi que não são gerenciados pelo vCenter Server.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Quais são as opções de dimensionamento em uma avaliação de VM do Azure?

Com o dimensionamento local, as migrações para Azure não consideram os dados de desempenho da VM para avaliação. As migrações para Azure avaliam os tamanhos de VM com base na configuração local. Com o dimensionamento baseado em desempenho, o dimensionamento é baseado nos dados de utilização.

Por exemplo, se uma VM local tiver quatro núcleos e 8 GB de memória às 50% de utilização da CPU e 50% de utilização da memória:
- O dimensionamento local recomendará um SKU de VM do Azure que tenha quatro núcleos e 8 GB de memória.
- O dimensionamento baseado em desempenho recomendará uma SKU de VM que tenha dois núcleos e 4 GB de memória, pois a porcentagem de utilização é considerada.

Da mesma forma, o dimensionamento de disco depende de critérios de dimensionamento e tipo de armazenamento:
- Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for automático, as migrações para Azure levarão os valores de IOPS e taxa de transferência do disco em conta quando identificarem o tipo de disco de destino (Standard ou Premium).
- Se os critérios de dimensionamento forem baseados em desempenho e o tipo de armazenamento for Premium, as migrações para Azure recomendarão uma SKU de disco Premium com base no tamanho do disco local. A mesma lógica é aplicada ao dimensionamento de disco quando o dimensionamento é o local e o tipo de armazenamento é Standard ou Premium.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>O histórico de desempenho e a utilização afetam o dimensionamento em uma avaliação de VM do Azure?

Sim, o histórico de desempenho e a utilização afetam o dimensionamento em uma avaliação de VM do Azure.

### <a name="performance-history"></a>Histórico de desempenho

Somente para o dimensionamento baseado em desempenho, as migrações para Azure coletam o histórico de desempenho de computadores locais e, em seguida, o usam para recomendar o tamanho da VM e o tipo de disco no Azure:

1. O dispositivo cria o perfil continuamente no ambiente local para coletar dados de utilização em tempo real a cada 20 segundos.
2. O dispositivo acumula os exemplos de 20 segundos coletados e os usa para criar um único ponto de dados a cada 15 minutos.
3. Para criar o ponto de dados, o dispositivo seleciona o valor de pico de todas as amostras de 20 segundos.
4. O dispositivo envia o ponto de dados para o Azure.

### <a name="utilization"></a>Utilização

Quando você cria uma avaliação no Azure, dependendo da duração do desempenho e do valor do percentual do histórico de desempenho definido, as migrações para Azure calculam o valor efetivo de utilização e, em seguida, as usam para o dimensionamento.

Por exemplo, se você definir a duração do desempenho para um dia e o valor de percentil para 95 º percentil, as migrações para Azure classificarão os pontos de exemplo de 15 minutos enviados pelo coletor para o dia anterior em ordem crescente. Ele escolhe o valor de 95 º percentil como a utilização efetiva.

O uso do valor 95 º percentil garante que as exceções sejam ignoradas. As exceções poderão ser incluídas se a migração do Azure usar o 99 º percentil. Para escolher o pico de uso do período sem exceções, defina migrações para Azure para usar o 99 º percentil.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Como as avaliações baseadas em importação são diferentes das avaliações com a fonte de descoberta como dispositivo?

As avaliações de VM do Azure baseadas em importação são avaliações criadas com computadores que são importados para migrações para Azure usando um arquivo CSV. Somente quatro campos são obrigatórios para importar: nome do servidor, núcleos, memória e sistema operacional. Aqui estão algumas coisas a serem observadas: 
 - Os critérios de preparação são menos rigorosos em avaliações baseadas em importação no parâmetro de tipo de inicialização. Se o tipo de inicialização não for fornecido, supõe-se que o computador tem o tipo de inicialização BIOS e que o computador não está marcado como **condicionalmente pronto**. Em avaliações com a origem de descoberta como dispositivo, a preparação será marcada como **condicionalmente pronta** se o tipo de inicialização estiver ausente. Essa diferença no cálculo de preparação é porque os usuários podem não ter todas as informações sobre as máquinas nos estágios iniciais do planejamento de migração quando são feitas avaliações baseadas em importação. 
 - As avaliações de importação baseadas em desempenho usam o valor de utilização fornecido pelo usuário para cálculos de dimensionamento correto. Como o valor de utilização é fornecido pelo usuário, as opções **histórico de desempenho** e utilização de **percentil** são desabilitadas nas propriedades de avaliação. Em avaliações com a origem de descoberta como dispositivo, o valor de percentil escolhido é escolhido dos dados de desempenho coletados pelo dispositivo.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Por que a ferramenta de migração sugerida na avaliação de AVS baseada em importação foi marcada como desconhecida?

Para computadores importados por meio de um arquivo CSV, a ferramenta de migração padrão em uma avaliação de AVS é desconhecida. No entanto, para máquinas VMware, é recomendável usar a solução de HCX (extensão de nuvem híbrida) do VMware. [Saiba mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).


## <a name="what-is-dependency-visualization"></a>O que é a visualização de dependência?

A visualização de dependência pode ajudá-lo a avaliar grupos de VMs para migrar com maior confiança. A visualização de dependência faz verificações entre as dependências do computador antes de executar uma avaliação. Ele ajuda a garantir que nada seja deixado para trás e ajuda a evitar interrupções inesperadas ao migrar para o Azure. As Migrações para Azure usam a solução Mapa do Serviço no Azure Monitor para habilitar a visualização de dependência. [Saiba mais](concepts-dependency-visualization.md).

> [!NOTE]
> A análise de dependência baseada em agente não está disponível no Azure governamental. Você pode usar a análise de dependência sem agente

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Qual é a diferença entre o agente e o sem agente?

As diferenças entre a visualização sem agente e a visualização baseada em agente são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseado em agente**
--- | --- | ---
Suporte | Essa opção está atualmente em visualização e só está disponível para VMs VMware. [Examine](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) os sistemas operacionais com suporte. | Em disponibilidade geral (GA).
Agente | Não é necessário instalar agentes em computadores que você deseja verificar. | Agentes a serem instalados em cada computador local que você deseja analisar: o [MMA (Microsoft Monitoring Agent)](../azure-monitor/platform/agent-windows.md)e o [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent). 
Pré-requisitos | [Examine](concepts-dependency-visualization.md#agentless-analysis) os pré-requisitos e os requisitos de implantação. | [Examine](concepts-dependency-visualization.md#agent-based-analysis) os pré-requisitos e os requisitos de implantação.
Log Analytics | Não necessário. | As Migrações para Azure usam a solução [Mapa do Serviço](../azure-monitor/insights/service-map.md) nos [logs do Azure Monitor](../azure-monitor/log-query/log-query-overview.md) para visualização de dependência. [Saiba mais](concepts-dependency-visualization.md#agent-based-analysis).
Como ele funciona | Captura dados de conexão TCP em computadores habilitados para visualização de dependência. Após a descoberta, ele coleta dados em intervalos de cinco minutos. | Mapa do Serviço agentes instalados em um computador coletam dados sobre processos TCP e conexões de entrada/saída para cada processo.
Dados | Nome do servidor do computador de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor do computador de destino, processo, nome do aplicativo e porta. | Nome do servidor do computador de origem, processo, nome do aplicativo.<br/><br/> Nome do servidor do computador de destino, processo, nome do aplicativo e porta.<br/><br/> Número de conexões, latência e informações de transferência de dados são coletadas e disponibilizadas para consultas de Log Analytics. 
Visualização | O mapa de dependências de um único servidor pode ser exibido durante uma duração de uma hora a 30 dias. | Mapa de dependências de um único servidor.<br/><br/> O mapa pode ser exibido somente em uma hora.<br/><br/> Mapa de dependências de um grupo de servidores.<br/><br/> Adicionar e remover servidores de um grupo da exibição de mapa.
Exportação de dados | Os últimos 30 dias de dados podem ser baixados em um formato CSV. | Os dados podem ser consultados com Log Analytics.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Preciso implantar o dispositivo para análise de dependência sem agente?

Sim, o [dispositivo migrações para Azure](migrate-appliance.md) deve ser implantado.

## <a name="do-i-pay-for-dependency-visualization"></a>Eu pago pela visualização de dependência?

Não. Saiba mais sobre os [preços das migrações para Azure](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>O que instalo para a visualização de dependência baseada em agente?

Para usar a visualização de dependência baseada em agente, baixe e instale agentes em cada computador local que você deseja avaliar:

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)
- [Agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Se você tiver computadores que não têm conectividade com a Internet, baixe e instale o Log Analytics gateway neles.

Você precisará desses agentes somente se usar a visualização de dependência baseada em agente.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um workspace existente?

Sim, para a visualização de dependências baseada em agente, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para a visualização de dependência. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização de dependência?

Não, o relatório de visualização de dependência na visualização baseada em agente não pode ser exportado. No entanto, as migrações para Azure usam Mapa do Serviço e você pode usar a [API REST mapa do serviço](/rest/api/servicemap/machines/listconnections) para recuperar as dependências no formato JSON.

## <a name="can-i-automate-agent-installation"></a>Posso automatizar a instalação do agente?

Para visualização de dependência baseada em agente:

- Use um [script para instalar o Dependency Agent](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent).
- Para MMA, [use a linha de comando ou automação](../azure-monitor/platform/log-analytics-agent.md#installation-options)ou use um [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Além de scripts, você pode usar ferramentas de implantação como o Microsoft Endpoint Configuration Manager e o [Intigua](https://www.intigua.com/intigua-for-azure-migration) para implantar os agentes.

## <a name="what-operating-systems-does-mma-support"></a>A quais sistemas operacionais o MMA dá suporte?

- Exiba a lista de [sistemas operacionais Windows aos quais o MMA dá suporte](../azure-monitor/platform/log-analytics-agent.md#installation-options).
- Exiba a lista de [sistemas operacionais Linux aos quais o MMA dá suporte](../azure-monitor/platform/log-analytics-agent.md#installation-options).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Posso Visualizar dependências por mais de uma hora?

Para a visualização baseada em agente, você pode visualizar dependências de até uma hora. Você pode voltar até um mês até uma data específica no histórico, mas a duração máxima da visualização é de uma hora. Por exemplo, você pode usar a duração de tempo no mapa de dependências para exibir dependências de ontem, mas você pode exibir dependências somente para uma janela de uma hora. No entanto, você pode usar os logs de Azure Monitor para [consultar dados de dependência](./how-to-create-group-machine-dependencies.md) por uma duração maior.

Para a visualização sem agente, você pode exibir o mapa de dependências de um único servidor de uma duração de entre uma hora e 30 dias.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Posso Visualizar dependências de grupos com mais de 10 VMs?

Você pode [Visualizar dependências](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) para grupos que têm até 10 VMS. Se você tiver um grupo com mais de 10 VMs, é recomendável dividir o grupo em grupos menores e, em seguida, Visualizar as dependências.

## <a name="next-steps"></a>Próximas etapas

Leia a [visão geral de migrações para Azure](migrate-services-overview.md).