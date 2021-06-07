---
title: Solucionar problemas de visualização de dependência e de avaliação em migrações para Azure
description: Obtenha ajuda com a visualização de dependência e avaliação nas migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 78e54543c94cd6e8434023b61516242c2491f353
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863591"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Solucionar problemas de visualização de avaliação/dependência

Este artigo ajuda você a solucionar problemas com a visualização de dependência e avaliação com as [migrações para Azure: descoberta e avaliação](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problemas de preparação da avaliação

Corrija os problemas de preparação da avaliação da seguinte maneira:

**Problema** | **Correção**
--- | ---
Tipo de inicialização sem suporte | O Azure não dá suporte a VMs com um tipo de inicialização EFI. Recomendamos que você converta o tipo de inicialização para BIOS antes de executar uma migração. <br/><br/>Você pode usar a migração de servidor de migrações para o Azure para lidar com a migração dessas VMs. Ele converterá o tipo de inicialização da VM para BIOS durante a migração.
Sistema operacional Windows com suporte condicional | O sistema operacional passou pela data de fim do suporte e precisa de um contrato de suporte personalizado (CSA) para [dar suporte no Azure](/troubleshoot/azure/virtual-machines/server-software-support). Considere a atualização antes de migrar para o Azure. Examine as informações sobre como [preparar servidores que executam o Windows Server 2003](prepare-windows-server-2003-migration.md) para migração para o Azure.
Sistema operacional Windows sem suporte | O Azure dá suporte apenas a [versões selecionadas do sistema operacional Windows](/troubleshoot/azure/virtual-machines/server-software-support). Considere atualizar o servidor antes de migrar para o Azure.
SO Linux condicionalmente endossado | O Azure endossa apenas [as versões selecionadas do sistema operacional Linux](../virtual-machines/linux/endorsed-distros.md). Considere atualizar o servidor antes de migrar para o Azure. Consulte também [aqui](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment) para obter mais detalhes.
SO Linux não endossado | O servidor pode iniciar no Azure, mas o Azure não fornece suporte ao sistema operacional. Considere atualizar para uma [versão do Linux endossada](../virtual-machines/linux/endorsed-distros.md) antes de migrar para o Azure.
Sistema operacional desconhecido | O sistema operacional da VM foi especificado como "other" em vCenter Server. Esse comportamento impede que as migrações para Azure verifiquem a prontidão do Azure da VM. Verifique se o sistema operacional tem [suporte](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) do Azure antes de migrar o servidor.
Versão de bits sem suporte | As VMs com sistemas operacionais de 32 bits podem ser inicializadas no Azure, mas recomendamos que você atualize para 64 bits antes de migrar para o Azure.
Requer uma assinatura Microsoft Visual Studio | O servidor está executando um sistema operacional cliente Windows, que tem suporte apenas por meio de uma assinatura do Visual Studio.
VM não encontrada para o desempenho de armazenamento necessário | O desempenho de armazenamento (operações de entrada/saída por segundo [IOPS] e taxa de transferência) necessário para o servidor excede o suporte à VM do Azure. Reduza os requisitos de armazenamento para o servidor antes da migração.
VM não encontrada para o desempenho de rede necessário | O desempenho de rede (entrada/saída) necessário para o servidor excede o suporte de VM do Azure. Reduza os requisitos de rede para o servidor.
VM não encontrada no local especificado | Use um local de destino diferente antes da migração.
Um ou mais discos inadequados | Um ou mais discos anexados à VM não atendem aos requisitos do Azure. Um<br/><br/> Migrações para Azure: a descoberta e a avaliação atualmente não dão suporte a discos SSD Ultra e avalia os discos com base nos limites de disco para discos gerenciados Premium (32 TB).<br/><br/> Para cada disco anexado à VM, verifique se o tamanho do disco é < 64 TB (com suporte de discos SSD Ultra).<br/><br/> Se não estiver, reduza o tamanho do disco antes de migrar para o Azure, ou use vários discos no Azure e distribua [-os juntos](../virtual-machines/premium-storage-performance.md#disk-striping) para obter limites de armazenamento maiores. Certifique-se de que o desempenho (IOPS e taxa de transferência) necessário para cada disco tem suporte dos [discos de máquina virtual gerenciada](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)pelo Azure.
Um ou mais adaptadores de rede inadequados. | Remova adaptadores de rede não utilizados do servidor antes da migração.
A contagem de disco excede o limite | Remova os discos não utilizados do servidor antes da migração.
O tamanho do disco excede o limite | Migrações para Azure: a descoberta e a avaliação atualmente não dão suporte a discos SSD Ultra e avalia os discos com base nos limites de disco Premium (32 TB).<br/><br/> No entanto, o Azure dá suporte a discos com tamanho de até 64 TB (com suporte de discos SSD Ultra). Reduza os discos para menos de 64 TB antes da migração ou use vários discos no Azure e distribua [-os juntos](../virtual-machines/premium-storage-performance.md#disk-striping) para obter limites de armazenamento mais altos.
O disco não está disponível no local especificado | Verifique se o disco está em seu local de destino antes de migrar.
O disco não está disponível para a redundância especificada | O disco deve usar o tipo de armazenamento de redundância definido nas configurações de avaliação (LRS por padrão).
Não foi possível determinar a adequação do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo.
VM com núcleos necessários e memória não encontrada | O Azure não pôde encontrar um tipo de VM adequado. Reduza a memória e o número de núcleos do servidor local antes de migrar.
Não foi possível determinar a adequação da VM devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação para um ou mais discos devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação para um ou mais adaptadores de rede devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Nenhum tamanho de VM encontrado para a instância reservada de moeda de oferta | Servidor marcado como não adequado porque o tamanho da VM não foi encontrado para a combinação selecionada de RI, oferta e moeda. Edite as propriedades de avaliação para escolher as combinações válidas e recalcular a avaliação. 
Protocolo de Internet condicionalmente pronto | Aplicável somente a avaliações da AVS (solução VMware do Azure). A AVS não dá suporte ao fator de endereços de Internet IPv6. Entre em contato com a equipe da AVS para obter diretrizes de correção se o servidor for detectado com o IPv6.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Ferramenta de migração sugerida na avaliação de AVS baseada em importação marcada como desconhecida

Para servidores importados por meio de um arquivo CSV, a ferramenta de migração padrão no e a avaliação AVS são desconhecidas. No entanto, para servidores no ambiente VMware, é recomendável usar a solução de HCX (extensão de nuvem híbrida) do VMware. [Saiba mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>VMs do Linux são "prontamente prontas" em uma avaliação de VM do Azure

No caso de VMs do VMware e Hyper-V, a avaliação de VM do Azure marca as VMs do Linux como "prontamente prontas" devido a uma lacuna conhecida. 

- A lacuna impede que ele detecte a versão secundária do sistema operacional Linux instalada nas VMs locais.
- Por exemplo, para RHEL 6,10, atualmente, a avaliação de VM do Azure detecta apenas RHEL 6 como a versão do sistema operacional. Isso ocorre porque o vCenter Server ar o host Hyper-V não fornece a versão do kernel para sistemas operacionais de VM do Linux.
-  Como o Azure endossa apenas versões específicas do Linux, as VMs do Linux estão atualmente marcadas como prontas para avaliação de VM do Azure.
- Você pode determinar se o SO Linux em execução na VM local é endossado no Azure examinando o [suporte do Azure Linux](../virtual-machines/linux/endorsed-distros.md).
-  Depois de verificar a distribuição endossada, você pode ignorar este aviso.

Essa lacuna pode ser resolvida habilitando a [descoberta de aplicativos](./how-to-discover-applications.md) nas VMs VMware. A avaliação de VM do Azure usa o sistema operacional detectado da VM usando as credenciais de convidado fornecidas. Esses dados do sistema operacional identificam as informações corretas do so no caso de VMs Windows e Linux.

## <a name="operating-system-version-not-available"></a>Versão do sistema operacional não disponível

Para servidores físicos, as informações de versão secundária do sistema operacional devem estar disponíveis. Se não estiver disponível, entre em contato com Suporte da Microsoft. Para servidores no ambiente VMware, as migrações para Azure usam as informações do sistema operacional especificadas para a VM no vCenter Server. No entanto, vCenter Server não fornece a versão secundária para sistemas operacionais. Para descobrir a versão secundária, você precisa configurar a [descoberta de aplicativos](./how-to-discover-applications.md). Para VMs do Hyper-V, não há suporte para a descoberta de versão secundária do sistema operacional. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>SKUs do Azure maiores do que localmente em uma avaliação de VM do Azure

A avaliação de VM do Azure pode recomendar SKUs de VM do Azure com mais núcleos e memória do que a alocação local atual com base no tipo de avaliação:

- A recomendação de SKU de VM depende das propriedades de avaliação.
- Isso é afetado pelo tipo de avaliação que você executa na avaliação de VM do Azure: *baseada em desempenho* ou *como local*.
- Para avaliações baseadas em desempenho, a avaliação de VM do Azure considera os dados de utilização das VMs locais (CPU, memória, disco e utilização de rede) para determinar o SKU de VM de destino correto para suas VMs locais. Ela também adiciona um fator de conforto ao determinar a utilização efetiva.
- Para o dimensionamento local, os dados de desempenho não são considerados e o SKU de destino é recomendado com base na alocação local.

Para mostrar como isso pode afetar as recomendações, vamos usar um exemplo:

Temos uma VM local com quatro núcleos e oito GB de memória, com 50% de utilização da CPU e 50% de utilização da memória e um fator de conforto especificado de 1,3.

-  Se a avaliação for **como local**, é recomendável um SKU de VM do Azure com quatro núcleos e 8 GB de memória.
- Se a avaliação for baseada em desempenho, com base na utilização efetiva de CPU e memória (50% de 4 núcleos * 1,3 = 2,6 núcleos e 50% de 8 GB de memória * 1,3 = 5,3 GB de memória), o SKU de VM mais barato de quatro núcleos (contagem de núcleos com suporte mais próximo) e oito GB de memória (tamanho de memória mais próximo com suporte
- [Saiba mais](concepts-assessment-calculation.md#types-of-assessments) sobre o dimensionamento da avaliação.

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Por que os SKUs de disco do Azure recomendados são maiores do que localmente em uma avaliação de VM do Azure?

A avaliação de VM do Azure pode recomendar um disco maior com base no tipo de avaliação.
- O dimensionamento de disco depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento.
- Se os critérios de dimensionamento forem **baseados em desempenho** e o tipo de armazenamento for definido como **automático**, os valores de IOPS e taxa de transferência do disco serão considerados ao identificar o tipo de disco de destino (HDD Standard, SSD Standard ou Premium). Um SKU de disco do tipo de disco é recomendado, e a recomendação considera os requisitos de tamanho do disco local.
- Se os critérios de dimensionamento forem **baseados em desempenho** e o tipo de armazenamento for **Premium**, um SKU de disco Premium no Azure será recomendado com base nos requisitos de IOPS, taxa de transferência e tamanho do disco local. A mesma lógica é usada para executar o dimensionamento de disco quando os critérios de dimensionamento são **locais** e o tipo de armazenamento é **HDD Standard**, **SSD Standard** ou **Premium**.

Por exemplo, se você tiver um disco local com 32 GB de memória, mas o IOPS agregado de leitura e gravação para o disco for de 800 IOPS, a avaliação de VM do Azure recomendará um disco Premium (devido aos requisitos de IOPS mais altos) e, em seguida, recomendará uma SKU de disco que possa dar suporte ao IOPS e ao tamanho necessários. A correspondência mais próxima neste exemplo seria P15 (256 GB, IOPS de 1100). Embora o tamanho exigido pelo disco local fosse de 32 GB, a avaliação de VM do Azure recomenda um disco maior devido ao requisito de IOPS alto do disco local.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Por que os dados de desempenho estão ausentes para algumas/todas VMs no meu relatório de avaliação?

Para a avaliação “baseada em desempenho”, o relatório de avaliação exportado indica “PercentageOfCoresUtilizedMissing” ou “PercentageOfMemoryUtilizedMissing” quando o dispositivo de Migrações para Azure não pode coletar dados de desempenho das VMs locais. Verifique:

- Se as VMs estão ativadas pelo tempo para o qual está criando a avaliação
- Se apenas os contadores de memória estiverem ausentes e você estiver tentando avaliar as VMs do Hyper-V, verifique se você tem memória dinâmica habilitada nessas VMs. Há um problema conhecido atualmente devido ao qual o dispositivo de Migrações para Azure não pode coletar a utilização de memória para essas VMs.
- Se todos os contadores de desempenho estiverem ausentes, verifique se os requisitos de acesso à porta para avaliação foram atendidos. Saiba mais sobre os requisitos de acesso de porta para [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) e avaliação [física](./migrate-support-matrix-physical.md#port-access) .
Observação – Se algum dos contadores de desempenho estiver ausente, migrações para Azure: a descoberta e a avaliação voltarão para os núcleos/memória locais alocados e recomendará um tamanho de VM adequadamente.

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Por que os dados de desempenho estão ausentes para alguns/todos os servidores em minha VM do Azure e/ou relatório de avaliação do AVS?

Para a avaliação "baseada em desempenho", a exportação do relatório de avaliação diz "PercentageOfCoresUtilizedMissing" ou "PercentageOfMemoryUtilizedMissing" quando o dispositivo de migrações para Azure não pode coletar dados de desempenho para os servidores locais. Verifique:

- Se os servidores estiverem ligados durante o período durante o qual você está criando a avaliação
- Se apenas os contadores de memória estiverem ausentes e você estiver tentando avaliar os servidores no ambiente do Hyper-V. Nesse cenário, habilite a memória dinâmica nos servidores e ' recalcule ' a avaliação para refletir as alterações mais recentes. O dispositivo pode coletar valores de utilização de memória para servidores no ambiente Hyper-V somente quando o servidor tiver a memória dinâmica habilitada.

- Se todos os contadores de desempenho estiverem ausentes, verifique se as conexões de saída nas portas 443 (HTTPS) são permitidas.

    > [!Note]
    > Se algum dos contadores de desempenho estiver ausente, migrações para Azure: a descoberta e a avaliação voltarão para os núcleos/memória alocados no local e recomendará um tamanho de VM de acordo.


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

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>A licença do sistema operacional está incluída em uma avaliação de VM do Azure?

A avaliação de VM do Azure atualmente considera o custo de licença do sistema operacional somente para servidores Windows. Os custos de licença para servidores Linux não são considerados atualmente.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Como funciona o dimensionamento baseado em desempenho em uma avaliação de VM do Azure?

A avaliação de VM do Azure coleta continuamente dados de desempenho de servidores locais e usa-os para recomendar a SKU de máquina virtual e SKU de disco no Azure. [Saiba como](concepts-assessment-calculation.md#calculate-sizing-performance-based) os dados baseados em desempenho são coletados.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Por que minha avaliação mostra um aviso de que foi criada com uma combinação inválida de instâncias reservadas, tempo de atividade e desconto da VM (%)?
Quando você seleciona ' instâncias reservadas ', o ' desconto (%) ' e as propriedades ' VM de tempo de atividade ' não são aplicáveis. À medida que sua avaliação foi criada com uma combinação inválida dessas propriedades, os botões editar e recalcular são desabilitados. Crie uma nova avaliação. [Saiba mais](./concepts-assessment-calculation.md#whats-an-assessment).

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Não vejo dados de desempenho para alguns adaptadores de rede em meus servidores físicos

Isso pode acontecer se o servidor físico tiver a virtualização do Hyper-V habilitada. Nesses servidores, devido a uma lacuna de produtos, a migração do Azure atualmente descobre os adaptadores de rede física e virtual. A taxa de transferência de rede é capturada apenas nos adaptadores de rede virtual descobertos.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>O SKU de VM do Azure recomendado para meu servidor físico está superdimensionado

Isso pode acontecer se o servidor físico tiver a virtualização do Hyper-V habilitada. Nesses servidores, migrações para Azure atualmente descobre os adaptadores de rede física e virtual. Portanto, o não. de adaptadores de rede descobertos é maior do que o real. Como a avaliação de VM do Azure escolhe uma VM do Azure que pode dar suporte ao número necessário de adaptadores de rede, isso pode potencialmente resultar em uma VM superdimensionada. [Saiba mais](./concepts-assessment-calculation.md#calculating-sizing) sobre o impacto de não. de adaptadores de rede em dimensionamento. Esta é uma lacuna do produto que será abordada no futuro.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>Categoria de preparação "não está pronta" para meu servidor físico

A categoria de preparação pode estar marcada incorretamente como "não está pronta" no caso de um servidor físico que tenha a virtualização do Hyper-V habilitada. Nesses servidores, devido a uma lacuna de produtos, a migração do Azure atualmente descobre os adaptadores físicos e virtuais. Portanto, o não. de adaptadores de rede descobertos é maior do que o real. Em avaliações locais e baseadas em desempenho, a avaliação de VM do Azure escolhe uma VM do Azure que pode dar suporte ao número necessário de adaptadores de rede. Se for descoberto que o número de adaptadores de rede está sendo maior que 32, n º máximo de NICs com suporte em VMs do Azure, o servidor será marcado como "não pronto".  [Saiba mais](./concepts-assessment-calculation.md#calculating-sizing) sobre o impacto de não. de NICs no dimensionamento.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>Número de NICs descobertos acima do real para servidores físicos

Isso pode acontecer se o servidor físico tiver a virtualização do Hyper-V habilitada. Nesses servidores, migrações para Azure atualmente descobre os adaptadores físicos e virtuais. Portanto, o não. de NICs descobertas é maior do que o real.

## <a name="dependency-visualization-in-azure-government"></a>Visualização de dependência no Azure governamental

Não há suporte para a análise de dependência baseada em agente no Azure governamental. Use a análise de dependência sem agente.


## <a name="dependencies-dont-show-after-agent-install"></a>As dependências não são mostradas após a instalação do agente

Depois de instalar os agentes de visualização de dependência em VMs locais, as migrações para Azure normalmente leva 15-30 minutos para exibir as dependências no Portal. Se você tiver aguardado por mais de 30 minutos, verifique se o Microsoft Monitoring Agent (MMA) pode se conectar ao espaço de trabalho Log Analytics.

Para VMs do Windows:
1. No painel de controle, inicie o MMA.
2. Nas **Propriedades de Microsoft Monitoring Agent**  >  **log Analytics do Azure (OMS)**, verifique se o **status** do espaço de trabalho está verde.
3. Se o status não for verde, tente remover o espaço de trabalho e adicioná-lo novamente ao MMA.

    ![Status do MMA](./media/troubleshoot-assessment/mma-properties.png)

Para VMs do Linux, certifique-se de que os comandos de instalação para MMA e o Dependency Agent tenham êxito. Consulte mais diretrizes para solução de problemas [aqui](../azure-monitor/vm/service-map.md#post-installation-issues).

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

- **Agente MMS**: examine os sistemas operacionais [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)e [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) com suporte.
- **Agente de dependência**: os sistemas operacionais [Windows e Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) com suporte.

## <a name="visualize-dependencies-for--1-hour"></a>Visualizar dependências para > 1 hora

Com a análise de dependência sem agente, você pode visualizar dependências ou exportá-las em um mapa por uma duração de até 30 dias.

Com a análise de dependência baseada em agente, embora as migrações para Azure permitam que você volte a uma data específica no último mês, a duração máxima para a qual você pode visualizar as dependências é de uma hora. Por exemplo, você pode usar a funcionalidade duração de tempo no mapa de dependências para exibir dependências de ontem, mas você pode exibi-las somente para um período de uma hora. No entanto, você pode usar os logs de Azure Monitor para [consultar os dados de dependência](./how-to-create-group-machine-dependencies.md) por uma duração maior.

## <a name="visualized-dependencies-for--10-servers"></a>Dependências visualizadas para servidores > 10

Nas migrações para Azure, com a análise de dependência baseada em agente, você pode [Visualizar dependências para grupos](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) com até 10 VMS. Para grupos maiores, recomendamos que você divida as VMs em grupos menores para visualizar as dependências.


## <a name="servers-show-install-agent"></a>Os servidores mostram "instalar agente"

Após a migração de servidores com a visualização de dependência habilitada para o Azure, os servidores podem mostrar a ação "instalar agente" em vez de "Exibir dependências" devido ao seguinte comportamento:

- Após a migração para o Azure, os servidores locais são desativados e as VMs equivalentes são giradas no Azure. Esses servidores adquirem um endereço MAC diferente.
- Os servidores também podem ter um endereço IP diferente, dependendo se você manteve o endereço IP local ou não.
- Se os endereços MAC e IP forem diferentes do local, as migrações para Azure não associarão os servidores locais a qualquer Mapa do Serviço dados de dependência. Nesse caso, ele mostrará a opção para instalar o agente em vez de exibir as dependências.
- Após a migração de um teste para o Azure, os servidores locais permanecem ligados conforme o esperado. Os servidores equivalentes giram no Azure adquirem um endereço MAC diferente e podem adquirir endereços IP diferentes. A menos que você bloqueie o tráfego de log de Azure Monitor de saída desses servidores, as migrações para Azure não associarão os servidores locais a qualquer Mapa do Serviço dados de dependência e, portanto, mostrarão a opção de instalar agentes, em vez de exibir dependências.

## <a name="dependencies-export-csv-shows-unknown-process"></a>O CSV de exportação de dependências mostra "processo desconhecido"
Na análise de dependência sem agente, os nomes de processo são capturados em uma base de melhor esforço. Em determinados cenários, embora os nomes do servidor de origem e de destino e a porta de destino sejam capturados, não é possível determinar os nomes dos processos em ambas as extremidades da dependência. Nesses casos, o processo é marcado como "processo desconhecido".

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate"></a>Meu espaço de trabalho Log Analytics não está listado ao tentar configurar o espaço de trabalho em migrações para Azure
Atualmente, as Migrações para Azure dão suporte à criação do espaço de trabalho do OMS nas regiões leste dos EUA, Sudeste Asiático e Oeste da Europa. Se o espaço de trabalho for criado fora da migração do Azure em qualquer outra região, ele não poderá ser associado a um projeto no momento.


## <a name="capture-network-traffic"></a>Capturar o tráfego de rede

Colete os logs de tráfego de rede da seguinte maneira:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Pressione F12 para iniciar a Ferramentas para Desenvolvedores. Se necessário, desmarque a configuração  **Limpar entradas na navegação** .
3. Selecione a guia **rede** e inicie a captura do tráfego de rede:
   - No Chrome, selecione **Preservar log**. A gravação deve ser iniciada automaticamente. Um círculo vermelho indica que o tráfego está sendo capturado. Se o círculo vermelho não aparecer, selecione o círculo preto para iniciar.
   - No Microsoft Edge e no Internet Explorer, a gravação deve ser iniciada automaticamente. Se não estiver, selecione o botão verde Play.
4. Tente reproduzir o erro.
5. Depois que você tiver encontrado o erro durante a gravação, interrompa a gravação e salve uma cópia da atividade registrada:
   - No Chrome, clique com o botão direito do mouse e selecione **salvar como Har com conteúdo**. Essa ação compacta e exporta os logs como um arquivo. Har.
   - No Microsoft Edge ou Internet Explorer, selecione a opção **Exportar tráfego capturado** . Essa ação compacta e exporta o log.
6. Selecione a guia **console** para verificar se há avisos ou erros. Para salvar o log do console:
   - No Chrome, clique com o botão direito em qualquer lugar no log do console. Selecione **salvar como**, para exportar e compactar o log.
   - No Microsoft Edge ou no Internet Explorer, clique com o botão direito do mouse nos erros e selecione **copiar tudo**.
7. Fechar as Ferramentas para Desenvolvedores.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>De onde os dados do sistema operacional em minha avaliação são descobertos?

- Para VMs VMware, por padrão, são os dados do sistema operacional fornecidos pelo vCenter. 
   - Para VMs do VMware Linux, se a descoberta de aplicativos estiver habilitada, os detalhes do sistema operacional serão buscados na VM convidada. Para verificar quais detalhes do sistema operacional na avaliação, vá para a exibição servidores descobertos e passe o mouse sobre o valor na coluna "sistema operacional". No texto que aparece, você poderá ver se os dados do sistema operacional que você vê são coletados do vCenter Server ou da VM convidada usando as credenciais da VM. 
   - Para VMs do Windows, os detalhes do sistema operacional são sempre buscados no vCenter Server.
- Para VMs do Hyper-V, os dados do sistema operacional são coletados do host do Hyper-V
- Para servidores físicos, ele é buscado no servidor.

## <a name="next-steps"></a>Próximas etapas

[Crie](how-to-create-assessment.md) ou [Personalize](how-to-modify-assessment.md) uma avaliação.