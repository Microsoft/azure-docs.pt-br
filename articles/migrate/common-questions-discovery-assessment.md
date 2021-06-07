---
title: Perguntas sobre descoberta, avaliação e análise de dependência em migrações para Azure
description: Obtenha respostas para perguntas comuns sobre descoberta, avaliação e análise de dependência em migrações para Azure.
author: rashijoshi
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 629459d22b18b326307b45bb512d16622808b533
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562623"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Descoberta, avaliação e análise de dependência-perguntas comuns

Este artigo responde a perguntas comuns sobre descoberta, avaliação e análise de dependência em migrações para Azure. Se você tiver outras dúvidas, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre as migrações para Azure
- Perguntas sobre o [dispositivo migrações para Azure](common-questions-appliance.md)
- Perguntas sobre a [migração do servidor](common-questions-server-migration.md)
- Obter perguntas respondidas no [Fórum de migrações para Azure](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Quais geografias têm suporte para descoberta e avaliação com migrações para Azure?

Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Quantos servidores posso descobrir com um dispositivo?

Você pode descobrir até 10.000 servidores do ambiente VMware, até 5.000 servidores do ambiente Hyper-V e até 1000 servidores físicos usando um único dispositivo. Se você tiver mais servidores, leia sobre como [dimensionar uma avaliação do Hyper-V](scale-hyper-v-assessment.md), [dimensionando uma avaliação do VMware](scale-vmware-assessment.md)ou [dimensionando uma avaliação de servidor físico](scale-physical-assessment.md).

## <a name="how-do-i-choose-the-assessment-type"></a>Como fazer escolher o tipo de avaliação?

- Use as **avaliações de VM do Azure** quando desejar avaliar servidores de seu ambiente do [VMware](how-to-set-up-appliance-vmware.md) e do [Hyper-V](how-to-set-up-appliance-hyper-v.md) local e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para VMs do Azure. [Saiba mais](concepts-assessment-calculation.md)

- Use o tipo de avaliação **SQL do Azure** quando desejar avaliar sua SQL Server local do ambiente VMware para migração para o banco de dados SQL do Azure ou o SQL instância gerenciada do Azure. [Saiba mais](concepts-assessment-calculation.md)

- Use as avaliações da **AVS (solução do Azure VMware)** quando desejar avaliar suas [VMs VMware](how-to-set-up-appliance-vmware.md) locais para migração para a [solução VMware do Azure (AVS)](../azure-vmware/introduction.md) usando esse tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

- Você pode usar um grupo comum com computadores VMware somente para executar os dois tipos de avaliações. Se você estiver executando avaliações da AVS nas Migrações para Azure pela primeira vez, recomendamos criar um novo grupo de computadores VMware.
 

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Por que os dados de desempenho estão ausentes para alguns/todos os servidores em minha VM do Azure e/ou relatório de avaliação do AVS?

Para a avaliação "baseada em desempenho", a exportação do relatório de avaliação diz "PercentageOfCoresUtilizedMissing" ou "PercentageOfMemoryUtilizedMissing" quando o dispositivo de migrações para Azure não pode coletar dados de desempenho para os servidores locais. Verifique:

- Se os servidores estiverem ligados durante o período durante o qual você está criando a avaliação
- Se apenas os contadores de memória estiverem ausentes e você estiver tentando avaliar os servidores no ambiente do Hyper-V. Nesse cenário, habilite a memória dinâmica nos servidores e ' recalcule ' a avaliação para refletir as alterações mais recentes. O dispositivo pode coletar valores de utilização de memória para servidores no ambiente Hyper-V somente quando o servidor tiver a memória dinâmica habilitada.

- Se todos os contadores de desempenho estiverem ausentes, verifique se as conexões de saída nas portas 443 (HTTPS) são permitidas.

    > [!Note]
    > Se algum dos contadores de desempenho estiver ausente, migrações para Azure: a avaliação do servidor voltará para os núcleos/memória alocados locais e recomendará um tamanho de VM adequadamente.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Por que os dados de desempenho estão ausentes para algumas/todas as instâncias/bancos de dado SQL em minha avaliação do SQL Azure?

Para garantir que os dados de desempenho sejam coletados, verifique:

- Se os SQL Servers estiverem ligados durante o período durante o qual você está criando a avaliação
- Se o status da conexão do SQL Agent no Azure migrar for ' Connected ' e verificar a última pulsação 
- Se o status de conexão da migração do Azure para todas as instâncias do SQL for ' conectado ' na folha da instância do SQL descoberta
- Se todos os contadores de desempenho estiverem ausentes, verifique se as conexões de saída nas portas 443 (HTTPS) são permitidas

Se algum dos contadores de desempenho estiver ausente, a avaliação do SQL do Azure recomendará a menor configuração do Azure SQL para essa instância/banco de dados.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Por que a confiança de classificação da minha avaliação é baixa?

A classificação de confiança é calculada para avaliações de "baseadas em desempenho" com base na porcentagem de [pontos de dados disponíveis](./concepts-assessment-calculation.md#ratings) necessária para computar a avaliação. Veja abaixo os motivos pelos quais uma avaliação poderia obter uma classificação de baixa confiança:

- Você não criou o perfil do ambiente pelo tempo para o qual está criando a avaliação. Por exemplo, se você está criando uma avaliação com duração de desempenho definida como uma semana, precisa aguardar pelo menos uma semana após iniciar a descoberta para que todos os pontos de dados sejam coletados. Se você não puder esperar pela duração, altere a duração do desempenho para um período menor e **recalcule** a avaliação.
 
- A avaliação não é capaz de coletar os dados de desempenho de alguns ou de todos os servidores no período de avaliação. Para uma classificação de alta confiança, verifique se: 
    - Os servidores estão ligados durante a avaliação
    - Conexões de saída nas portas 443 são permitidas
    - Para servidores Hyper-V, a memória dinâmica está habilitada 
    - O status de conexão dos agentes nas migrações para Azure são "conectados" e verifica a última pulsação
    - Para avaliações de SQL do Azure, o status de conexão de migrações para Azure para todas as instâncias do SQL é "conectado" na folha da instância do SQL descoberta

    **Recalcule** a avaliação para refletir as alterações mais recentes na classificação de confiança.

- Para avaliações de VM do Azure e AVS, poucos servidores foram criados após o início da descoberta. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas poucos servidores foram criados no ambiente apenas uma semana atrás. Nesse caso, os dados de desempenho para os novos servidores não estarão disponíveis durante toda a duração e a classificação de confiança será baixa. [Saiba mais](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Para avaliações do SQL do Azure, poucas instâncias ou bancos de dados SQL foram criados após o início da descoberta. Por exemplo, se você estiver criando uma avaliação para o histórico de desempenho do último mês, mas poucas instâncias ou bancos de dados SQL foram criados no ambiente apenas uma semana atrás. Nesse caso, os dados de desempenho para os novos servidores não estarão disponíveis durante toda a duração e a classificação de confiança será baixa. [Saiba mais](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="i-want-to-try-out-the-new-azure-sql-assessment"></a>Quero experimentar a nova avaliação do Azure SQL
A descoberta e a avaliação de instâncias e bancos de dados do SQL Server em execução no ambiente VMware já estão em versão prévia. Veja uma introdução [neste tutorial](tutorial-discover-vmware.md). Se você quiser experimentar esse recurso em um projeto existente, verifique se você concluiu os [pré-requisitos](how-to-discover-sql-existing-project.md) neste artigo.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Não consigo ver alguns servidores ao criar uma avaliação do SQL do Azure

- A avaliação do SQL do Azure só pode ser feita em servidores que executam o local em que as instâncias do SQL foram descobertas. Se você não vir os servidores e as instâncias do SQL que deseja avaliar, aguarde algum tempo para a descoberta ser concluída e, em seguida, crie a avaliação. 
- Se você não conseguir ver um grupo criado anteriormente ao criar a avaliação, remova qualquer servidor não VMware ou qualquer servidor sem uma instância do SQL do grupo.
- Se você estiver executando avaliações do SQL do Azure no Migrações para Azure pela primeira vez, recomendamos criar um novo grupo de servidores.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>Quero entender como a preparação da minha instância foi computada?
A preparação para suas instâncias SQL foi calculada após a verificação da compatibilidade de recursos com o tipo de implantação do Azure SQL de destino (Banco de Dados SQL do Azure ou Instância Gerenciada de SQL do Azure). [Saiba mais](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>Por que a preparação para todas as minhas instâncias do SQL são marcadas como desconhecidas?
Se sua descoberta foi iniciada recentemente e ainda estiver em andamento, você poderá ver a preparação para algumas ou todas as instâncias do SQL como desconhecidas. Recomendamos que você aguarde algum tempo para o dispositivo criar o perfil do ambiente e recalcular a avaliação.
A descoberta do SQL é executada uma vez a cada 24 horas e talvez seja necessário aguardar até um dia para que as últimas alterações de configuração sejam refletidas. 

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>Por que a preparação para algumas das minhas instâncias do SQL são marcadas como desconhecidas?
Isso pode acontecer se: 
- A descoberta ainda está em andamento. Recomendamos que você aguarde algum tempo para o dispositivo criar o perfil do ambiente e recalcular a avaliação.
- Há alguns problemas de descoberta que você precisa corrigir na folha Erros e Notificações.

A descoberta do SQL é executada uma vez a cada 24 horas e talvez seja necessário aguardar até um dia para que as últimas alterações de configuração sejam refletidas.

## <a name="my-assessment-is-in-outdated-state"></a>Minha avaliação está no estado Desatualizado

### <a name="azure-vmavs-assessment"></a>Avaliação de VM/AVS do Azure
Se houver alterações locais nos servidores que estão em um grupo que foi avaliado, a avaliação será marcada como desatualizada. Uma avaliação pode ser marcada como "desatualizada" devido a uma ou mais alterações nas propriedades abaixo:
- Número de núcleos de processador
- Memória alocada
- Tipo de inicialização ou firmware
- Nome, versão e arquitetura do sistema operacional
- Número de discos
- Número de adaptadores de rede
- Alteração do tamanho do disco (GB alocados)
- Atualização das propriedades da NIC. Exemplo: alterações de endereço Mac, adição de endereço IP, etc.

**Recalcule** a avaliação para refletir as alterações mais recentes na avaliação.

### <a name="azure-sql-assessment"></a>Avaliação do SQL do Azure
Se houver alterações nas instâncias do SQL locais e nos bancos de dados que estão em um grupo que foi avaliado, a avaliação será marcada como **desatualizada**:
- A instância SQL foi adicionada ou removida de um servidor
- O banco de dados SQL foi adicionado ou removido de uma instância SQL
- Tamanho total do banco de dados em uma instância SQL alterada em mais de 20%
- Alteração no número de núcleos de processador e/ou memória alocada

**Recalcule** a avaliação para refletir as alterações mais recentes na avaliação.

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>Por que recebi a recomendação de um tipo de implantação de destino específico?
O Migrações para Azure recomenda um tipo específico de implantação do SQL do Azure que seja compatível com sua instância SQL. A migração para um destino recomendado pela Microsoft reduz o esforço geral de migração. Essa SKU (configuração de SQL do Azure) foi recomendada depois de considerar as características de desempenho de sua instância SQL e os bancos de dados que ela gerencia. Se várias configurações do SQL do Azure estiverem qualificadas, recomendamos a um, que é a mais econômica. [Saiba mais](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>Qual destino de implantação devo escolher se minha instância SQL estiver pronta para o banco de dados do SQL do Azure e para a Instância Gerenciada de SQL do Azure? 
Se sua instância estiver pronta para o banco de dados de SQL do Azure e para a Instância Gerenciada de SQL do Azure, recomendamos o tipo de implantação de destino para o qual o custo estimado da configuração do SQL do Azure for menor.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>Por que minha instância está marcada como potencialmente pronta para a VM do Azure na minha avaliação do Azure SQL?
Isso pode acontecer quando o tipo de implantação de destino escolhido nas propriedades de avaliação é **Recomendado** e a instância do SQL não está pronta para o Banco de Dados SQL do Azure e para a Instância Gerenciada de SQL do Azure. É recomendável que o usuário crie uma avaliação no Migrações para Azure com o tipo de avaliação como **VM do Azure** para determinar se o servidor no qual a instância está sendo executada está pronto para migrar para uma VM do Azure.
É recomendável que o usuário crie uma avaliação no Azure migrar com o tipo de avaliação como **VM do Azure** para determinar se o servidor no qual a instância está sendo executada está pronto para migrar para uma VM do Azure em vez disso:
- As avaliações de VM do Azure nas migrações para Azure estão em boas-mudança e não considerarão as métricas de desempenho específicas para a execução de instâncias e bancos de dados SQL na máquina virtual do Azure. 
- Quando você executa uma avaliação de VM do Azure em um servidor, o tamanho recomendado e as estimativas de custo são para todas as instâncias em execução no servidor e podem ser migradas para uma VM do Azure usando a ferramenta de migração de servidor. Antes de migrar, [examine as diretrizes de desempenho](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) para SQL Server em máquinas virtuais do Azure.

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>Não consigo ver alguns bancos de dados em minha avaliação, embora a instância faça parte da avaliação

A avaliação do SQL do Azure inclui apenas bancos de dados que estão no status online. Caso o banco de dados esteja em qualquer outro status, a avaliação ignorará a preparação, o dimensionamento e o cálculo de custo para esses bancos de dados. Caso queira avaliar esses bancos de dados, mude o status dele e recalcule a avaliação em algum momento.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Quero comparar os custos para executar minhas instâncias do SQL na VM do Azure em vez do banco de dados SQL do Azure/Azure SQL Instância Gerenciada

Você pode criar uma avaliação com o tipo **VM do Azure** no mesmo grupo que foi usado na avaliação do **SQL do Azure**. Em seguida, você pode comparar os dois relatórios lado a lado. No entanto, as avaliações de VM do Migrações para Azure se concentram no momento e não se nas métricas de desempenho específicas para executar instâncias e bancos de dados SQL na máquina virtual do Azure. Quando você executa uma avaliação de VM do Azure em um servidor, o tamanho recomendado e as estimativas de custo são para todas as instâncias em execução no servidor e podem ser migradas para uma VM do Azure usando a ferramenta de migração de servidor. Antes de migrar, [examine as diretrizes de desempenho](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) para SQL Server em máquinas virtuais do Azure.

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>O custo de armazenamento em minha avaliação do SQL Azure é zero
Para o Azure SQL Instância Gerenciada, não há custo de armazenamento adicionado para o primeiro armazenamento de 32 GB/instância/mês, e o custo de armazenamento adicional é adicionado para armazenamento em incrementos de 32 GB. [Saiba mais](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Não consigo ver alguns grupos ao criar uma avaliação da AVS (solução do Azure VMware)

- A avaliação da AVS pode ser feita em grupos que têm apenas computadores VMware. Remova os computadores não VMware do grupo se você pretende executar uma avaliação da AVS.
- Se você estiver executando avaliações da AVS nas Migrações para Azure pela primeira vez, recomendamos criar um novo grupo de computadores VMware.

## <a name="i-cant-see-some-vm-types-and-sizes-in-azure-government"></a>Não consigo ver alguns tipos e tamanhos de VM no Azure governamental

Os tipos de VM e os tamanhos com suporte para avaliação e migração dependem da disponibilidade no local do Azure governamental. Você pode [examinar e comparar](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) os tipos de VM no Azure governamental.

## <a name="the-size-of-my-server-changed-can-i-run-an-assessment-again"></a>O tamanho do meu servidor foi alterado. Posso executar uma avaliação novamente?

O dispositivo de migração do Azure coleta continuamente informações sobre o ambiente local.  Uma avaliação é um instantâneo point-in-time de servidores locais. Se você alterar as configurações em um servidor que deseja avaliar, use a opção recalcular para atualizar a avaliação com as alterações mais recentes.

## <a name="how-do-i-discover-servers-in-a-multitenant-environment"></a>Como fazer descobrir servidores em um ambiente multilocatário?

- **VMware**: se um ambiente for compartilhado entre locatários e você não quiser descobrir os servidores de um locatário na assinatura de outro locatário, crie VMware vCenter Server credenciais que possam acessar somente os servidores que você deseja descobrir. Em seguida, use essas credenciais quando iniciar a descoberta no dispositivo de migrações para Azure.
- **Hyper-v**: a descoberta usa credenciais de host do Hyper-v. Se os servidores compartilharem o mesmo host do Hyper-V, não há como separar a descoberta no momento.  

## <a name="do-i-need-vcenter-server"></a>Preciso de vCenter Server?

Sim, as migrações para Azure exigem vCenter Server em um ambiente VMware para executar a descoberta. As migrações para Azure não dão suporte à descoberta de hosts ESXi que não são gerenciados pelo vCenter Server.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Quais são as opções de dimensionamento em uma avaliação de VM do Azure?

Com o dimensionamento local, as migrações para Azure não consideram os dados de desempenho do servidor para avaliação. As migrações para Azure avaliam os tamanhos de VM com base na configuração local. Com o dimensionamento baseado em desempenho, o dimensionamento é baseado nos dados de utilização.

Por exemplo, se um servidor local tiver quatro núcleos e 8 GB de memória às 50% de utilização da CPU e 50% de utilização da memória:
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

A visualização de dependência pode ajudá-lo a avaliar grupos de servidores para migrar com maior confiança. A visualização de dependência faz verificações entre as dependências do computador antes de executar uma avaliação. Ele ajuda a garantir que nada seja deixado para trás e ajuda a evitar interrupções inesperadas ao migrar para o Azure. As Migrações para Azure usam a solução Mapa do Serviço no Azure Monitor para habilitar a visualização de dependência. [Saiba mais](concepts-dependency-visualization.md).

> [!NOTE]
> A análise de dependência baseada em agente não está disponível no Azure governamental. Você pode usar a análise de dependência sem agente

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Qual é a diferença entre o agente e o sem agente?

As diferenças entre a visualização sem agente e a visualização baseada em agente são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseado em agente**
--- | --- | ---
Suporte | Essa opção está atualmente em visualização e só está disponível para servidores no ambiente VMware. [Examine](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) os sistemas operacionais com suporte. | Em disponibilidade geral (GA).
Agente | Não é necessário instalar agentes em computadores que você deseja verificar. | Agentes a serem instalados em cada computador local que você deseja analisar: o [MMA (Microsoft Monitoring Agent)](../azure-monitor/agents/agent-windows.md)e o [Dependency Agent](../azure-monitor/agents/agents-overview.md#dependency-agent). 
Pré-requisitos | [Examine](concepts-dependency-visualization.md#agentless-analysis) os pré-requisitos e os requisitos de implantação. | [Examine](concepts-dependency-visualization.md#agent-based-analysis) os pré-requisitos e os requisitos de implantação.
Log Analytics | Não necessário. | As Migrações para Azure usam a solução [Mapa do Serviço](../azure-monitor/vm/service-map.md) nos [logs do Azure Monitor](../azure-monitor/logs/log-query-overview.md) para visualização de dependência. [Saiba mais](concepts-dependency-visualization.md#agent-based-analysis).
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

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)
- [Agente de dependência](../azure-monitor/agents/agents-overview.md#dependency-agent)
- Se você tiver computadores que não têm conectividade com a Internet, baixe e instale o Log Analytics gateway neles.

Você precisará desses agentes somente se usar a visualização de dependência baseada em agente.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um workspace existente?

Sim, para a visualização de dependências baseada em agente, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para a visualização de dependência. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização de dependência?

Não, o relatório de visualização de dependência na visualização baseada em agente não pode ser exportado. No entanto, as migrações para Azure usam Mapa do Serviço e você pode usar a [API REST mapa do serviço](/rest/api/servicemap/machines/listconnections) para recuperar as dependências no formato JSON.

## <a name="can-i-automate-agent-installation"></a>Posso automatizar a instalação do agente?

Para visualização de dependência baseada em agente:

- Use um [script para instalar o Dependency Agent](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent).
- Para MMA, [use a linha de comando ou automação](../azure-monitor/agents/log-analytics-agent.md#installation-options)ou use um [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Além de scripts, você pode usar ferramentas de implantação como o Microsoft Endpoint Configuration Manager e o [Intigua](https://www.intigua.com/intigua-for-azure-migration) para implantar os agentes.

## <a name="what-operating-systems-does-mma-support"></a>A quais sistemas operacionais o MMA dá suporte?

- Exiba a lista de [sistemas operacionais Windows aos quais o MMA dá suporte](../azure-monitor/agents/log-analytics-agent.md#installation-options).
- Exiba a lista de [sistemas operacionais Linux aos quais o MMA dá suporte](../azure-monitor/agents/log-analytics-agent.md#installation-options).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Posso Visualizar dependências por mais de uma hora?

Para a visualização baseada em agente, você pode visualizar dependências de até uma hora. Você pode voltar até um mês até uma data específica no histórico, mas a duração máxima da visualização é de uma hora. Por exemplo, você pode usar a duração de tempo no mapa de dependências para exibir dependências de ontem, mas você pode exibir dependências somente para uma janela de uma hora. No entanto, você pode usar os logs de Azure Monitor para [consultar dados de dependência](./how-to-create-group-machine-dependencies.md) por uma duração maior.

Para a visualização sem agente, você pode exibir o mapa de dependências de um único servidor de uma duração de entre uma hora e 30 dias.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-servers"></a>Posso Visualizar dependências de grupos com mais de 10 servidores?

Você pode [Visualizar dependências](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) para grupos que têm até 10 servidores. Se você tiver um grupo com mais de 10 servidores, é recomendável dividir o grupo em grupos menores e, em seguida, Visualizar as dependências.

## <a name="next-steps"></a>Próximas etapas

Leia a [visão geral de migrações para Azure](migrate-services-overview.md).