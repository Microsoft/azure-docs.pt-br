---
title: Migrando SAP HANA no Azure (instâncias grandes) para máquinas virtuais do Azure | Microsoft Docs
description: Como migrar SAP HANA no Azure (instâncias grandes) para máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd1cfb0cc8e1868e78b4d284d1b1f4e7e85aa318
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677039"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA na migração de instância grande do Azure para máquinas virtuais do Azure
Este artigo descreve possíveis cenários de implantação de instância grande do Azure e oferece abordagem de planejamento e migração com tempo de inatividade de transição minimizado

## <a name="overview"></a>Visão geral
Desde o anúncio das instâncias grandes do Azure para SAP HANA (HLI) em setembro de 2016, muitos clientes adotaram essa oferta de hardware como serviço para sua plataforma de computação na memória.  Nos últimos anos, a extensão de tamanho de VM do Azure, associada ao suporte da implantação de expansão do HANA, excedeu a demanda de capacidade de banco de dados ERP dos clientes corporativos.  Começamos a ver os clientes que expressam o interesse de migrar sua carga de trabalho SAP HANA de servidores físicos para VMs do Azure.
Este guia não é um documento de configuração passo a passo. Ele descreve os modelos comuns de implantação e oferece recomendações de planejamento e migração.  A intenção é chamar as considerações necessárias para a preparação para minimizar o tempo de inatividade da transição.

## <a name="assumptions"></a>Suposições
Este artigo pressupõe o seguinte:
- O único interesse considerado é uma migração de serviço de computação de banco de dados do HANA homogêneo da cópia de instância grande (HLI) do Hana para a VM do Azure sem atualização significativa de software ou aplicação de patch. Essas pequenas atualizações incluem o uso de uma versão do sistema operacional mais recente ou da versão do HANA explicitamente declarada com suporte em notas SAP relevantes. 
- Todas as atividades de atualização/atualizações precisam ser feitas antes ou após a migração.  Por exemplo, SAP HANA MCOS convertendo para a implantação MDC. 
- A abordagem de migração que ofereceria o menor tempo de inatividade é SAP HANA replicação do sistema. Outros métodos de migração não fazem parte do escopo deste documento.
- Essas diretrizes são aplicáveis para as SKUs Rev3 e Rev4 do HLI.
- A arquitetura de implantação do HANA permanece inalterada principalmente durante a migração.  Ou seja, um sistema com uma única instância de DR permanecerá da mesma maneira no destino.
- Os clientes revisaram e compreenderam o Contrato de Nível de Serviço (SLA) da arquitetura de destino (para ser). 
- Os termos comerciais entre HLIs e VMs são diferentes. Os clientes devem monitorar o uso de suas VMs para o gerenciamento de custos.
- Os clientes entendem que o HLI é uma plataforma de computação dedicada enquanto as VMs são executadas na infraestrutura compartilhada, mas isolada.
- Os clientes validaram que as VMs de destino dão suporte à sua arquitetura pretendida. Para ver todos os SKUs de VM com suporte para a implantação do SAP HANA, consulte o [diretório SAP Hana hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Os clientes validaram o plano de design e migração.
- Planeje a VM de recuperação de desastre junto com o site primário.  Os clientes não podem usar o HLI como o nó de recuperação de desastre para o site primário em execução em VMs após a migração.
- Os clientes copiaram os arquivos de backup necessários para as VMs de destino, com base na capacidade de recuperação e nos requisitos de conformidade dos negócios. Com os backups acessíveis à VM, ele permite a recuperação pontual durante o período de transição.
- Para o HSR HA, os clientes precisam configurar e configurar o dispositivo STONITH por SAP HANA guias de alta disponibilidade para [SLES](./high-availability-guide-suse-pacemaker.md) e [RHEL](./high-availability-guide-rhel-pacemaker.md).  Ele não é pré-configurado como o caso de HLI.
- Essa abordagem de migração não abrange as SKUs de HLI com a configuração de Optane.

## <a name="deployment-scenarios"></a>Cenários de implantação
Os modelos de implantação comuns com clientes do HLI são resumidos na tabela a seguir.  A migração para VMs do Azure para todos os cenários de HLI é possível.  Para se beneficiar dos serviços complementares do Azure disponíveis, alterações arquitetônicas secundárias podem ser necessárias.

| ID do cenário | Cenário de HLI | Migrar para a VM idêntica? | Comentário |
| --- | --- | --- | --- |
| 1 | [Único nó com um SID](./hana-supported-scenario.md#single-node-with-one-sid) | Sim | - |
| 2 | [Nó único com MCOS](./hana-supported-scenario.md#single-node-mcos) | Sim | - |
| 3 | [Nó único com DR usando replicação de armazenamento](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | Não | A replicação de armazenamento não está disponível com a plataforma virtual do Azure, altere a solução de DR atual para HSR ou backup/restauração |
| 4 | [Nó único com DR (multipropósito) usando replicação de armazenamento](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | Não | A replicação de armazenamento não está disponível com a plataforma virtual do Azure, altere a solução de DR atual para HSR ou backup/restauração |
| 5 | [HSR com STONITH para alta disponibilidade](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Sim | Nenhum SBD pré-configurado para VMs de destino.  Selecione e implante uma solução STONITH.  Opções possíveis: agente de isolamento do Azure (com suporte para [RHEL](./high-availability-guide-rhel-pacemaker.md), [SLES](./high-availability-guide-suse-pacemaker.md)), SBD |
| 6 | [HA com HSR, DR com replicação de armazenamento](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | Não | Substitua a replicação de armazenamento para necessidades de DR com HSR ou backup/restauração |
| 7 | [Failover Automático do Host (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11) | Sim | Usar o seja para armazenamento compartilhado com VMs do Azure |
| 8 | [Expansão com espera](./hana-supported-scenario.md#scale-out-with-standby) | Sim | BW/4HANA com M128s, M416s, VMs M416ms usando seja para armazenamento somente |
| 9 | [Expansão sem espera](./hana-supported-scenario.md#scale-out-without-standby) | Sim | BW/4HANA com M128s, M416s, VMs M416ms (com ou sem usar seja para armazenamento) |
| 10 | [Escalar horizontalmente com recuperação de desastre usando replicação de armazenamento](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | Não | Substitua a replicação de armazenamento para necessidades de DR com HSR ou backup/restauração |
| 11 | [Nó único com DR usando HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Sim | - |
| 12 | [HSR de nó único para DR (com otimização de custo)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Sim | - |
| 13 | [HA e DR com HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Sim | - |
| 14 | [HA e DR com HSR (com otimização de custo)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Sim | - |
| 15 | [Escale horizontalmente com DR usando HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Sim | BW/4HANA com M128s. M416s, VMs M416ms (com ou sem o uso de seja para armazenamento) |


## <a name="source-hli-planning"></a>Planejamento de origem (HLI)
Ao integrar um servidor de HLI, o gerenciamento de serviços da Microsoft e os clientes passaram pelo planejamento das configurações específicas de computação, rede, armazenamento e sistema operacional para executar o banco de dados de SAP HANA.  O planejamento semelhante precisa ocorrer para a migração para a VM do Azure.

### <a name="sap-hana-housekeeping"></a>SAP HANA a manutenção 
É uma boa prática operacional organizar o conteúdo do banco de dados para que os logs indesejados, desatualizados ou obsoletos não sejam migrados para o novo.  A manutenção geralmente envolve a exclusão ou o arquivamento de dados antigos, expirados ou inativos.  Essas ações de ' higiene de dados ' devem ser testadas em sistemas de não produção para validar sua validade de corte de dados antes do uso de produção.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Permitir conectividade de rede para novas VMs e, ou rede virtual 
Na implantação do HLI de um cliente, a rede foi configurada com base nas informações descritas no artigo [SAP Hana arquitetura de rede (instâncias grandes)](./hana-network-architecture.md). Além disso, o roteamento de tráfego de rede é feito da maneira descrita na seção "roteamento no Azure".
- Ao configurar uma nova VM como o destino de migração, se ela for colocada na rede virtual existente com os intervalos de endereços IP já permitidos para se conectar à HLI, nenhuma outra atualização de conectividade será necessária.
- Se a nova VM do Azure for colocada em um novo Rede Virtual do Microsoft Azure, pode estar em outra região e emparelhada com a rede virtual existente, a chave de serviço do ExpressRoute e a ID de recurso do provisionamento de HLI original são utilizáveis para permitir o acesso a esse novo intervalo de IP de rede virtual.  Coordene com o gerenciamento de serviços da Microsoft para habilitar a conectividade de rede virtual para HLI.  Observação: para minimizar a latência de rede entre as camadas do aplicativo e do banco de dados, as camadas do aplicativo e do banco de dados devem estar na mesma rede virtual.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Conjunto de disponibilidade de camada de aplicativo existente, Zonas de Disponibilidade e grupo de posicionamento de proximidade (PPG)
O modelo de implantação atual é feito para atender a certos objetivos de nível de serviço.  Nessa mudança, verifique se a infraestrutura de destino atenderá ou excederá as metas definidas.  
Mais provável do que não, clientes servidores de aplicativos SAP são colocados em um conjunto de disponibilidade.  Se o nível de serviço de implantação atual for satisfatório e 
- Se a VM de destino considerar o nome de host do endereço lógico de HLI, a atualização da resolução de endereços DNS (serviço de nomes de domínio) que aponta para o IP da VM funcionaria sem Atualizar nenhum perfil do SAP
- Se você não estiver usando o PPG, certifique-se de posicionar todos os servidores de aplicativo e DB na mesma zona para minimizar a latência de rede.
- Se você estiver usando PPG, consulte a seção deste documento: ' planejamento de destino, conjunto de disponibilidade, Zonas de Disponibilidade e grupo de posicionamento de proximidade (PPG) '.

### <a name="storage-replication-discontinuance-process-if-used"></a>Processo de descontinuação da replicação de armazenamento (se usado)
Se a replicação de armazenamento for usada como solução de recuperação de desastre, ela deverá ser encerrada (não agendada) depois que o aplicativo SAP tiver sido desligado.  Além disso, o último SAP HANA catálogo, arquivo de log e backups de dados foram replicados nos volumes de armazenamento de recuperação de desastres remotas.  Fazer isso como uma precaução no caso de um desastre ocorrer durante o servidor físico para a transição de VM do Azure.

### <a name="data-backups-preservation-consideration"></a>Consideração de preservação de backups de dados
Após o recorte para SAP HANA na VM do Azure, todos os backups de log ou dados baseados em instantâneos na HLI não serão facilmente acessíveis ou restauráveis para uma VM, se necessário. No período de transição antecipado, antes que o backup baseado no Azure crie um histórico suficiente para atender aos requisitos de recuperação pontual, recomendamos pegar backups de nível de arquivo, além de instantâneos em HLI, dias ou semanas antes da transferência.  Faça com que esses backups sejam copiados para uma conta de armazenamento do Azure acessível pela nova VM SAP HANA.
Além de fazer backup do conteúdo de HLI, é prudente ter backups completos do cenário do SAP prontamente acessíveis, caso uma reversão seja necessária.

### <a name="adjusting-system-monitoring"></a>Ajustando o monitoramento do sistema 
Os clientes usam muitas ferramentas diferentes para monitorar e enviar notificações de alerta para sistemas em sua estrutura SAP.  Esse item é apenas uma chamada para a ação apropriada para incorporar alterações para monitoramento e atualizar os destinatários de notificação de alerta, se necessário.

### <a name="microsoft-operations-team-involvement"></a>Envolvimento da equipe de operações da Microsoft 
Abra um tíquete do portal do Azure com base na instância de HLI existente.  Depois que o tíquete de suporte for criado, um engenheiro de suporte entrará em contato com você por email.  

### <a name="engage-microsoft-account-team"></a>Participe da equipe de conta Microsoft
Planeje a migração perto do horário de renovação de aniversário do contrato de HLI para minimizar o excesso de despesas no recurso de computação. Para encerrar a folha HLI, é necessário coordenar o encerramento do contrato e o desligamento real da unidade.

## <a name="destination-planning"></a>Planejamento de destino
Uma nova infra-estrutura para assumir o lugar de um existente merece pensar em garantir que a nova adição caiba no grande esquema de coisas.  Abaixo estão alguns pontos principais para contemplation.

### <a name="resource-availability-in-the-target-region"></a>Disponibilidade de recursos na região de destino 
A região de implantação atual dos servidores de aplicativos SAP geralmente está próxima à proximidade com o HLIs associado.  No entanto, os HLIs são oferecidos em menos locais do que as regiões do Azure disponíveis.  Ao migrar o Physical físico para a VM do Azure, também é um bom momento para ajustar a distância da proximidade de todos os serviços relacionados para otimização de desempenho.  Ao fazer isso, uma consideração importante é garantir que a região escolhida tenha todos os recursos necessários.  Por exemplo, a disponibilidade de determinada família de VM ou a oferta de zonas do Azure para configuração de alta disponibilidade.

### <a name="virtual-network"></a>Rede virtual 
Os clientes precisam escolher se deseja executar o novo banco de dados HANA em uma rede virtual existente ou criar um novo.  O principal fator de decisão é o layout de rede atual para a estrutura SAP.  Além disso, quando a infraestrutura passa de uma zona para a implantação de duas zonas e usa PPG, ela impõe alterações arquitetônicas. Para obter mais informações, consulte o artigo [PPG do Azure para latência de rede ideal com o aplicativo SAP](./sap-proximity-placement-scenarios.md).   

### <a name="security"></a>Segurança
Se o novo SAP HANA inicial de VM em uma vnet/sub-rede nova ou existente, ele representa um novo serviço comercialmente crítico que requer proteção.  O controle de acesso em conformidade com a política de segurança de informações da empresa deve ser avaliado e implantado para essa nova classe de serviço.

### <a name="vm-sizing-recommendation"></a>Recomendação de dimensionamento de VM
Essa migração também é uma oportunidade de dimensionar o seu mecanismo de computação do HANA.  É possível usar [exibições do sistema](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) Hana em conjunto com o Hana Studio para entender o consumo de recursos do sistema, que permite o dimensionamento correto para impulsionar a eficiência dos gastos.

### <a name="storage"></a>Armazenamento 
O desempenho do armazenamento é um dos fatores que impactam a experiência do usuário do aplicativo SAP.  Base em uma determinada SKU de VM, há um layout de armazenamento mínimo publicado [SAP Hana configurações de armazenamento de máquina virtual do Azure](./hana-vm-operations-storage.md). É recomendável revisar essas especificações mínimas e comparar com as estatísticas de sistema de HLI existentes para garantir a capacidade e o desempenho de e/s adequado para a nova VM do HANA.

Se você configurar o PPG para a nova VM do HANA e seus servidores associados, envie um tíquete de suporte para inspecionar e garantir a colocalização do armazenamento e da VM. Como sua solução de backup pode precisar ser alterada, o custo de armazenamento também deve ser revisitado para evitar surpresas de gastos operacionais.

### <a name="storage-replication-for-disaster-recovery"></a>Replicação de armazenamento para recuperação de desastre
Com a HLI, a replicação de armazenamento foi oferecida como a opção padrão para a recuperação de desastre. Esse recurso não é a opção padrão para SAP HANA na VM do Azure. Considere o HSR, o backup/restauração ou outras soluções com suporte que atendem às suas necessidades de negócios.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Conjuntos de disponibilidade, Zonas de Disponibilidade e grupos de posicionamento de proximidade 
Para reduzir a distância entre a camada de aplicativo e SAP HANA para manter a latência de rede no mínimo, a nova VM de banco de dados e os servidores de aplicativos SAP atuais devem ser colocados em um PPG. Consulte [grupo de posicionamento de proximidade](./sap-proximity-placement-scenarios.md) para saber como o conjunto de disponibilidade do Azure e zonas de disponibilidade funcionam com o PPG para implantações do SAP.
Se os membros do sistema HANA de destino forem implantados em mais de uma zona do Azure, os clientes deverão ter uma exibição clara do perfil de latência das zonas escolhidas. O posicionamento dos componentes do sistema SAP é ideal em relação à distância proximal entre o aplicativo SAP e o banco de dados.  A ferramenta de [teste de latência de zona de disponibilidade](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) de domínio público ajuda a facilitar a medição.  


### <a name="backup-strategy"></a>Estratégia de backup
Muitos clientes já estão usando soluções de backup de terceiros para SAP HANA em HLI.  Nesse caso, apenas uma VM protegida adicional e os bancos de dados HANA precisam ser configurados.  Os trabalhos de backup de HLI contínuos agora podem ser desagendados se o computador estiver sendo encerrado após a migração.
O backup do Azure para SAP HANA na VM já está disponível para o público em geral.  Consulte estes links para obter informações detalhadas sobre: [backup](../../../backup/backup-azure-sap-hana-database.md), [restauração](../../../backup/sap-hana-db-restore.md), [gerenciar](../../../backup/sap-hana-db-manage.md) SAP Hana backup em VMs do Azure.

### <a name="dr-strategy"></a>Estratégia de DR
Se seus objetivos de nível de serviço acomodam um tempo de recuperação mais longo, um backup simples para armazenamento de BLOBs e restauração em vigor ou restauração para uma nova VM é a estratégia de DR mais simples e menos dispendiosa.  
Como a plataforma de instância grande em que o HANA DR normalmente é feito com o HSR; Na VM do Azure, o HSR também é a solução de recuperação de desastre mais natural e nativa SAP HANA.  Independentemente de a implantação de origem ser de instância única ou clusterizada, uma réplica da infraestrutura de origem é necessária na região de DR.
Essa réplica de DR será configurada depois que a migração de HLI primário para VM for concluída.  O BD HANA do DR será registrado no SAP HANA primário na instância de VM como um site de replicação secundário.  

### <a name="sap-application-server-connectivity-destination-change"></a>Alteração de destino de conectividade do servidor de aplicativos SAP
A migração do HSR resulta em um novo host do BD HANA e, portanto, um novo nome de host do BD para a camada de aplicativo, os perfis do SAP precisam ser modificados para refletir o novo nome do host.  Se a alternância for feita pela resolução de nomes preservando o nome do host, nenhuma alteração de perfil será necessária.

### <a name="operating-system"></a>Sistema operacional
As imagens do sistema operacional para HLI e VM, apesar de estarem no mesmo nível de versão, o SLES 12 SP4, por exemplo, não é idêntico. Os clientes devem validar os pacotes necessários, hot fixes, patches, kernel e correções de segurança no HLI para instalar os mesmos pacotes no destino.  Há suporte para usar o HSR para replicar de um sistema operacional mais antigo para uma VM com uma versão mais recente do sistema operacional.  Verifique as versões específicas com suporte examinando a [Observação do SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nova solicitação de licença SAP
Uma chamada simples para solicitar uma nova licença SAP para o novo sistema HANA agora que ele foi migrado para VMs.

### <a name="service-level-agreement-sla-differences"></a>Diferenças de SLA (contrato de nível de serviço)
Os autores gostam de destacar a diferença do SLA de disponibilidade entre o HLI e a VM do Azure.  Por exemplo, pares clusterizados HLIs HA oferecem 99,99% de disponibilidade. Para obter o mesmo SLA, é necessário implantar VMs em zonas de disponibilidade. Este [artigo](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) descreve a disponibilidade com arquiteturas de implantação associadas para que os clientes possam planejar sua infraestrutura de destino adequadamente.


## <a name="migration-strategy"></a>Estratégia de migração
Neste documento, abordamos apenas a abordagem de replicação do sistema HANA para a migração do HLI para a VM do Azure.  Depende da solução de armazenamento de destino implantada, o processo difere ligeiramente. As etapas de alto nível são descritas abaixo.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM com Premium/ultra-discos para dados
Para VMs que são implantadas com Premium ou ultra discos, a configuração de replicação do sistema SAP HANA padrão é aplicável para configurar o HSR.  O [artigo de ajuda do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) fornece uma visão geral das etapas envolvidas na configuração da replicação do sistema, assumindo um sistema secundário, fazendo failback para o primário e desabilitando a replicação do sistema.  Para fins de migração, precisaremos apenas da configuração, assumindo e desabilitando as etapas de replicação.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM com seja para volumes de dados e de log
Em um alto nível, os instantâneos de armazenamento de HLI mais recentes dos volumes de dados e de log completos precisam ser copiados para o armazenamento do Azure, onde podem ser acessados e recuperáveis pela VM HANA de destino.  O processo de cópia pode ser feito com qualquer ferramenta de cópia nativa do Linux.  

> [!IMPORTANT]
> A cópia e a transferência de dados podem levar horas depende do tamanho e da largura de banda da rede do HANA.  A maior parte do processo de cópia deve ser feita antes do tempo de inatividade principal do HANA DB.

### <a name="mcos-to-mdc-conversion"></a>Conversão de MCOS para MDC
O modelo de implantação vários componentes em um sistema (MCOS) foi usado por alguns dos nossos clientes do HLI.  A motivação era burlar a limitação do instantâneo de armazenamento de vários contêineres de bancos de dados (MDC) das versões anteriores do SAP HANA.  No modelo MCOS, várias instâncias SAP HANA independentes são empilhadas em uma folha HLI.  O uso de HSR para a migração funcionaria bem, mas resultando em várias VMs do HANA com um banco de todos os locatários.  Esse estado final torna um cenário mais ocupado do que um cliente pode ter trabalhado.  Com a implantação padrão do SAP HANA 2,0 sendo MDC, uma alternativa viável é executar a [movimentação do locatário do Hana](https://launchpad.support.sap.com/#/notes/2472478) após a migração do HSR.  Esse processo "consolida" esses bancos de dados do HANA independentes em colocatários em um único contêiner do HANA.  

### <a name="application-layer-consideration"></a>Consideração da camada de aplicativo
O servidor de BD é exibido como o centro de um sistema SAP.  Todos os servidores de aplicativos devem estar localizados perto do SAP HANA DB.  Em alguns casos, quando o novo uso de PPG é desejado, a realocação de servidores de aplicativos existentes para o PPG em que a VM do HANA pode ser necessária.  A criação de novos servidores de aplicativos pode ser considerada mais fácil se você já tiver modelos de implantação úteis.  
Se os servidores de aplicativos existentes e a nova VM HANA estiverem localizados de maneira ideal, nenhum novo servidor de aplicativos precisará ser criado, a menos que seja necessária capacidade adicional.  
Se uma nova infraestrutura for criada para melhorar a disponibilidade do serviço, os servidores de aplicativos existentes poderão se tornar desnecessários e devem ser desligados e excluídos.
Se o nome de host da VM de destino for alterado e for diferente do nome do host do HLI, os perfis do servidor de aplicativos SAP precisarão ser ajustados para apontarem para o novo host.  Se apenas o endereço IP do BD HANA tiver sido alterado, uma atualização de registro DNS será necessária para conduzir as conexões de entrada para a nova VM HANA.

### <a name="acceptance-test"></a>Teste de aceitação
Embora a migração de HLI para VM não faça nenhuma alteração de material no conteúdo do banco de dados em comparação com uma migração heterogênea, ainda é recomendável validar as funcionalidades principais e o aspecto de desempenho da nova instalação.  

### <a name="cutover-plan"></a>Plano de transição
Embora essa migração seja direta, ela envolve o encerramento de um BD existente.  O planejamento cuidadoso para preservar o sistema de origem com seu conteúdo associado e suas imagens de backup são críticos no caso de fallback ser necessário.  Um bom planejamento oferece uma reversão de Speedier.   


## <a name="post-migration"></a>Após a migração
O trabalho de migração não é feito até que tenhamos dissociado com segurança qualquer serviço dependente de HLI ou conectividade para garantir que a integridade dos dados seja preservada.  Além disso, desligue os serviços desnecessários.  Esta seção chama alguns itens de primeira vista.

### <a name="decommissioning-the-hli"></a>Encerrando o HLI
Após uma migração bem-sucedida do BD HANA para a VM do Azure, certifique-se de que nenhuma transação comercial produtiva seja executada no banco de BD de HLI.  No entanto, manter a HLI em execução por um período de tempo igual a sua janela de retenção de backup local é uma prática segura garantindo a recuperação do Speedier, se necessário.  Somente depois a lâmina HLI deve ser encerrada.  Os clientes devem concluir de acordo com seus compromissos de HLI com a Microsoft entrando em contato com seus representantes da Microsoft.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Remover qualquer proxy (ex: iptables, BIGIP) configurado para HLI 
Se um serviço de proxy como o IPTables for usado para rotear o tráfego local para e da HLI, ele não será mais necessário após a migração bem-sucedida para a VM.  No entanto, esse serviço de conectividade deve ser mantido enquanto a lâmina HLI ainda estiver em pé.  Desligue o serviço somente depois que a folha HLI estiver completamente descomissionada.

### <a name="remove-global-reach-for-hli"></a>Remover Alcance Global para HLI 
Alcance Global é usado para conectar o gateway de ExpressRoute do cliente com o gateway de ExpressRoute do HLI.  Ele permite que o tráfego local dos clientes atinja o locatário de HLI diretamente sem o uso de um serviço de proxy.  Essa conexão não é mais necessária na ausência da unidade HLI após a migração.  Como o caso do serviço de proxy IPTables, o GlobalReach também deve ser mantido até que a folha HLI seja completamente descomissionada.

### <a name="operating-system-subscription--movereuse"></a>Assinatura do sistema operacional – mover/reutilizar
À medida que os servidores de VM estão acima e as folhas de HLI são encerradas, as assinaturas de sistema operacional podem ser substituídas ou reutilizadas para evitar o pagamento duplo de licenças de sistema operacional.



## <a name="next-steps"></a>Próximas etapas
Consulte estes artigos:
- [SAP Hana configurações de infraestrutura e operações no Azure](./hana-vm-operations.md).
- [Cargas de trabalho do SAP no Azure: lista de verificação de planejamento e implantação](./sap-deployment-checklist.md).
