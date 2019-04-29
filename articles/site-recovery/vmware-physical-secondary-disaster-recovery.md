---
title: Configurar a recuperação de desastre de VMs VMware ou de servidores físicos para um site secundário com o Azure Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação de desastre de VMs VMware ou de servidores físicos Windows e Linux para um site secundário com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 1a61a825ce223282e85c9262c613814530ba130f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565884"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configurar a recuperação de desastre de máquinas virtuais VMware locais ou de servidores físicos para um site secundário

O InMage Scout no [Azure Site Recovery](site-recovery-overview.md) fornece replicação em tempo real entre os sites locais do VMware. O InMage Scout está incluído nas assinaturas para o serviço Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Anúncio do fim do suporte

O cenário do Azure Site Recovery para replicação entre o VMware no local ou em data centers físicos está alcançando o fim do suporte.

-   A partir de agosto de 2018, o cenário não pode ser configurado no cofre de Serviços de Recuperação e o software InMage Scout não pode ser baixado do cofre. As implantações existentes terão suporte. 
-   De 31 de dezembro de 2020, o cenário não terá suporte.
- Parceiros existentes podem integrar o cenário de novos clientes até o término do suporte.

Durante a 2018 e 2019, duas atualizações serão lançadas: 

-   Atualização 7: Corrige problemas de configuração e conformidade de rede e dá suporte a TLS 1.2.
-   Atualização 8: Adiciona suporte para sistemas operacionais Linux RHEL/CentOS 7.3/7.4/7.5 e para SUSE 12

Após a Atualização 8, nenhuma outra atualização será lançada. Haverá suporte de hotfix limitado para os sistemas operacionais adicionados na Atualização 8 e correções de bugs com base no melhor esforço.

O Azure Site Recovery continua a inovar, fornecendo aos clientes VMware e Hyper-V uma solução de DRaaS contínua e solução DRaaS simples e melhor da classe com o Azure como o site de recuperação de desastre. A Microsoft recomenda que clientes existentes InMage / ASR Scout considerem o uso VMware do Azure Site Recovery para o cenário do Azure para as necessidades de continuidade de seus negócios. A VMware do Azure Site Recovery para o cenário do Azure é uma solução de recuperação de Desastre de nível corporativo para aplicativos de VMware, que oferece um RPO e RTO de minutos, suporte para replicação de aplicativo de várias VMs e a recuperação, integração contínua, monitoramento abrangente, e vantagem significativa de TCO.

### <a name="scenario-migration"></a>Migração do cenário
Como alternativa, é recomendável configurar a recuperação de desastre para as VMs do VMware locais e máquinas físicas replicando-os para o Azure. Faça isso da seguinte forma:

1.  Examine a comparação rápida abaixo. Antes de poder replicar máquinas locais, você precisa de verificação de que eles atendam aos [requisitos](./vmware-physical-azure-support-matrix.md#replicated-machines) para replicação no Azure. Se você estiver replicando VMs VMware, recomendamos que você examine [diretrizes de planejamento de capacidade](./site-recovery-plan-capacity-vmware.md)e execute a [ferramenta Planejador de Implantações do Azure Site Recovery](./site-recovery-deployment-planner.md) para requisitos de capacidade de identidade e verificar a conformidade.
2.  Depois de executar o Planejador de Implantação, você pode configurar a replicação: o para VMs do VMware, siga estes tutoriais para [preparar o Azure](./tutorial-prepare-azure.md), [preparar seu ambiente de VMware local](./vmware-azure-tutorial-prepare-on-premises.md), e [configurar recuperação de desastres](./vmware-azure-tutorial-prepare-on-premises.md).
o  Para máquinas físicas, siga este [tutorial](./physical-azure-disaster-recovery.md).
3.  Após a replicação de máquinas para o Azure, você pode executar uma [simulação de recuperação de desastres](./site-recovery-test-failover-to-azure.md) para verificar se tudo está funcionando conforme o esperado.

### <a name="quick-comparison"></a>Comparação rápida

**Recurso** | **Replicação para o Azure** |**Replicação entre os datacenters do VMware**
--|--|--
**Componentes necessários** |Serviço de mobilidade em máquinas replicadas. Servidor de configuração no local, servidor de processo, servidor de destino master. Servidor no processo temporário no Azure para failback.|Servidor de mobilidade, Servidor de Processo, Servidor de Configuração e Destino Master
**Configuração e orquestração** |Cofre de Serviços de Recuperação no portal do Azure | Usando o vContinuum 
**Replicado** |Disco (Windows e Linux) |Volume – Windows<br> Disco-Linux
**Cluster de disco compartilhado** |Sem suporte|Com suporte
**Limites de variação de dados (em média)** |Dados de 10 MB/s por disco<br> Dados de 25MB/s por VM<br> [Saiba mais](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > Dados de 10 MB/s por disco  <br> > Dados de 25MB/s por VM
**Monitoramento** |Do portal do Azure|Do CX (Servidor de Configuração)
**Matriz de suporte** | [Clique aqui para obter detalhes](./vmware-physical-azure-support-matrix.md)|[Baixar a matriz compatível do ASR Scout](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial:

- [Examine](vmware-physical-secondary-support-matrix.md) os requisitos de suporte de todos os componentes.
- Verifique se as máquinas que você deseja replicar estão em conformidade com o [suporte ao computador replicado](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Faça o download e instale atualizações de componentes

 Examine e instale as [atualizações](#updates) mais recentes. As atualizações devem ser instaladas nos servidores na seguinte ordem:

1. Servidor RX (caso se aplique)
2. Servidores de configuração
3. Servidores de processo
4. Servidores de destino mestre
5. Servidores vContinuum
6. Servidor de origem (servidores Linux e Windows)

Instale as atualizações da seguinte maneira:

> [!NOTE]
>A versão de atualização do arquivo de todos os componentes Scout pode não ser a mesma no arquivo .zip da atualização. A versão mais antiga indica que não há nenhuma alteração no componente desde a atualização anterior até essa atualização.

Baixe o arquivo .zip de [atualização](https://aka.ms/asr-scout-update7) e os arquivos de configuração [de atualização MySQL e PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade). O arquivo .zip de atualização contém todos os binários base e os binários de atualização cumulativa dos seguintes componentes: 
- InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
- RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release.tar.gz
  1. Extraia os arquivos .zip.
  2. **Servidor RX**: Copie **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** para o servidor RX e o extraia. Na pasta extraída, execute **/Install**.
  3. **Servidor de configuração e servidor de processo**: Copie **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** para o servidor de configuração e o servidor de processo. Clique duas vezes para executá-lo.<br>
  4. **Servidor de destino mestre do Windows**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor. Clique duas vezes nele para executá-lo. O mesmo arquivo também pode ser usado para uma nova instalação. A mesma atualização de agente unificado também se aplica ao servidor de origem.
  A atualização não precisa ser aplicada ao destino Mestre preparado com **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe**, pois esse é o novo instalador GA com todas as últimas alterações.
  5. **Servidor vContinuum**:  Copie **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor.  Verifique se que você fechou o assistente vContinuum. Clique duas vezes no arquivo para executá-lo.
  6. **Servidor de destino mestre do Linux**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** para o servidor de Destino Mestre do Linux e o extraia. Na pasta extraída, execute **/Install**.
  7. **Servidor de origem do Windows**: Para atualizar o agente unificado, copie **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** para o servidor de origem. Clique duas vezes no arquivo para executá-lo. 
  8. **Servidor de origem Linux**: Para atualizar o agente unificado, copie a versão correspondente do arquivo do agente unificado no servidor Linux e extraia-o. Na pasta extraída, execute **/Install**.  Exemplo: Para o servidor RHEL 6.7 de 64 bits, copie **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** para o servidor e o extraia. Na pasta extraída, execute **/Install**.
  9. Depois de atualizar o Servidor de Configuração, o Servidor de Processo e o servidor RX com os instaladores mencionados acima, as bibliotecas PHP e MySQL precisarão ser atualizadas manualmente com as etapas mencionadas na seção 7.4 do [guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Habilitar a replicação

1. Configure a replicação entre os sites do VMware de origem e de destino.
2. Consulte os documentos a seguir para saber mais sobre a instalação, proteção e recuperação:

   * [Notas de versão](https://aka.ms/asr-scout-release-notes)
   * [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
   * [Guia do usuário](https://aka.ms/asr-scout-user-guide)
   * [Guia do usuário do RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)
   * [Atualizando as bibliotecas MySQL e PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Atualizações

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 Atualização 7 
Atualizado: 31 de dezembro de 2018 Baixe o [Scout atualização 7](https://aka.ms/asr-scout-update7).
O Scout Atualização 7 é um instalador completo que pode ser usado para uma nova instalação, bem como para atualizar MT/agentes existentes que estão em atualizações anteriores (da Atualização 1 à Atualização 6). Ele contém todas as correções da Atualização 1 à Atualização 6, além das novas correções e melhorias descritas abaixo.
 
#### <a name="new-features"></a>Novos recursos
* Conformidade com PCI
* Suporte ao TLS v1.2

#### <a name="bug-and-security-fixes"></a>Bugs e correções de segurança
* Corrigido: Computadores Autônomos/Cluster do Windows têm uma configuração incorreta de IP após a recuperação/Análise de DR.
* Corrigido: Às vezes, a operação Adicionar disco falha para o cluster V2V.
* Correção: o Assistente do vContinuum trava durante a fase de recuperação se o Destino Mestre é o Windows Server 2016
* Corrigido: Os problemas de segurança do MySQL são atenuados com a atualização do MySQL para a versão 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Atualização manual do PHP e do MySQL no CS, no PS e no RX
A plataforma de script PHP deve ser atualizada para a versão 7.2.10 no Servidor de Configuração, no Servidor de Processo e no Servidor RX.
O sistema de gerenciamento de banco de dados MySQL deve ser atualizado para a versão 5.7.23 no Servidor de Configuração, no Servidor de Processo e no Servidor RX.
Siga as etapas manuais fornecidas na [guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide) atualizar versões do PHP e MySQL.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Atualização 6 
Atualizado: 12 de outubro de 2017

Faça o download [atualização Scout 6](https://aka.ms/asr-scout-update6).

A Atualização 6 do Scout é uma atualização cumulativa. Ela contém todas as correções desde a Atualização 1 até a Atualização 5 e as novas correções e aprimoramentos descritos abaixo. 

#### <a name="new-platform-support"></a>Novo suporte à plataforma
* Foi adicionado suporte para o Windows Server 2016 de origem
* Foi adicionado suporte para os seguintes sistemas operacionais Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Foi adicionado suporte para o VMware Center 6.5

Instale as atualizações da seguinte maneira:

> [!NOTE]
>A versão de atualização do arquivo de todos os componentes Scout pode não ser a mesma no arquivo .zip da atualização. A versão mais antiga indica que não há nenhuma alteração no componente desde a atualização anterior até essa atualização.

Baixe o arquivo .zip de [atualização](https://aka.ms/asr-scout-update6). O arquivo contém os seguintes componentes: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA update4 bits para RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_\<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Extraia os arquivos .zip.
  2. **Servidor RX**: Copie **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** no servidor RX e extraia-o. Na pasta extraída, execute **/Install**.
  3. **Servidor de configuração e servidor de processo**: Copie **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** no servidor de configuração e no servidor de processo. Clique duas vezes para executá-lo.<br>
  4. **Servidor de destino mestre do Windows**: Para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** no servidor. Clique duas vezes nele para executá-lo. A mesma atualização de agente unificado também se aplica ao servidor de origem. Se o de origem ainda não foi atualizado para a Atualização 4, você deve atualizar o agente unificado.
  A atualização não precisa ser aplicada ao destino mestre preparado com **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**, pois esse é o novo instalador GA com todas as alterações mais recentes.
  5. **Servidor vContinuum**:  Copie **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** no servidor.  Verifique se que você fechou o assistente vContinuum. Clique duas vezes no arquivo para executá-lo.
  A atualização não precisa ser aplicada ao destino mestre preparado com **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**, pois esse é o novo instalador GA com todas as alterações mais recentes.
  6. **Servidor de destino mestre do Linux**: Para atualizar o agente unificado, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** no servidor de destino mestre e extraia-o. Na pasta extraída, execute **/Install**.
  7. **Servidor de origem do Windows**: Para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor de origem. Clique duas vezes no arquivo para executá-lo. 
  Você não precisará instalar o agente de Atualização 5 no servidor de origem, se ele já tiver sido atualizado para a Atualização 4, ou se o agente de origem tiver sido instalado com o instalador base mais recente **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Servidor de origem Linux**: Para atualizar o agente unificado, copie a versão correspondente do arquivo do agente unificado no servidor Linux e extraia-o. Na pasta extraída, execute **/Install**.  Exemplo: Para o servidor RHEL 6.7 de 64 bits, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** no servidor e extraia-o. Na pasta extraída, execute **/Install**.


> [!NOTE]
> * O instalador de UA (Agente Unificado) Base para Windows foi atualizado para dar suporte ao Windows Server 2016. O novo instalador **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** vem com o pacote base da GA Scout (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O mesmo instalador será usado para todas as versões com suporte do Windows. 
> * O instalador base Windows vContinuum & Destino Mestre foi atualizado para dar suporte ao Windows Server 2016. O novo instalador **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** vem com o pacote base da GA Scout (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O mesmo instalador será usado para implantar o Destino Mestre do Windows 2016 e o Destino Mestre do Windows 2012 R2.
> * O Windows Server 2016 no servidor físico não tem suporte pelo ASR Scout. Ele dá suporte apenas para VM de VMware do Windows Server 2016. 
>

#### <a name="bug-fixes-and-enhancements"></a>Correções de bug e melhorias
- Falha de proteção de failback para a VM do Linux com lista de discos a serem replicados vazia no final da configuração.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Atualização 5
A Atualização 5 do Scout é uma atualização cumulativa. Ela contém todas as correções desde a Atualização 1 até a Atualização 4 e as novas correções descritas abaixo.
- As correções da Atualização 4 até a Atualização 5 do Site Recovery Scout são específicas para os componentes de destino mestre e do vContinuum.
- Se os servidores de origem, os servidores de destino mestre, de configuração, de processo e RX já estiverem executando a Atualização 4, aplique-a somente ao servidor de destino mestre. 

#### <a name="new-platform-support"></a>Novo suporte à plataforma
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* O SLES 11 SP4 de 64 bits  **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** é empacotado com o pacote base do Scout GA (**InMage_Scout_Standard_8.0.1 GA.zip**). Baixe o pacote GA do portal, conforme é descrito em criar um cofre.


#### <a name="bug-fixes-and-enhancements"></a>Correções de bug e melhorias

* Correções para aumentar a confiabilidade do suporte de cluster do Windows:
    * Corrigido – Alguns dos discos de cluster do MSCS P2V tornam-se brutos após a recuperação.
    * Corrigido – A recuperação de cluster do MSCS P2V falha devido à incompatibilidade de ordem do disco.
    * Corrigido – A operação de adicionar discos do cluster do MSCS falha com um erro de incompatibilidade de tamanho de disco.
    * Corrigido – A verificação de preparação do cluster do MSCS de origem com mapeamento de LUNs de RDM falha na verificação de tamanho.
    * Corrigido – A proteção de cluster de nó único falha devido a um problema de incompatibilidade de SCSI. 
    * Corrigido – A nova proteção do servidor de cluster do Windows P2V falha quando há discos de cluster de destino presentes. 
    
* Corrigido: Durante a proteção de failback, se o servidor de destino mestre selecionado não estiver no mesmo servidor ESXi que o computador de origem protegido (durante a proteção direta), o vContinuum escolherá o servidor de destino mestre errado durante a recuperação de failback e a operação de recuperação falhará.

> [!NOTE]
> * As correções do cluster P2V são aplicáveis somente a clusters físicos do MSCS recém-protegidos com o Site Recovery Scout Atualização 5. Para instalar as correções de cluster nos clusters do MSCS P2V protegidos com as atualizações mais antigas, siga as etapas de atualização mencionadas na seção 12 das [Site Recovery Scout Release Notes](https://aka.ms/asr-scout-release-notes) (Notas de versão do Site Recovery Scout).
> * Se no momento da nova proteção, o mesmo conjunto de discos estiver ativo em cada um dos nós do cluster como estavam quando foram protegidos inicialmente, a nova proteção de um cluster do MSCS físico só poderá reutilizar os discos de destino existentes. Se não, use as etapas manuais na seção 12 das [Site Recovery Scout Release Notes](https://aka.ms/asr-scout-release-notes) (Notas de versão do Site Recovery Scout), para mover os discos do lado do destino para o caminho do armazenamento de dados correto, para reutilização durante a nova proteção. Se você proteger novamente o cluster do MSCS no modo P2V sem seguir as etapas de atualização, será criado um novo disco no servidor ESXi de destino. Você precisará excluir manualmente os discos antigos do armazenamento de dados.
> * Quando um servidor SLES11 de origem ou SLES11 (com qualquer service pack) for reiniciado normalmente, marque manualmente os pares de replicação de disco **raiz** para ressincronização. Não há nenhuma notificação na interface do CX. Se você não marcar o disco raiz para ressincronização, poderão ocorrer problemas de integridade de dados.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Atualização 4
Scout Atualização 4 é uma atualização cumulativa. Ela inclui todas as correções desde a Atualização 1 até a Atualização 3 e as novas correções descritas abaixo.

#### <a name="new-platform-support"></a>Novo suporte à plataforma

* Foi adicionado suporte para o vCenter/vSphere 6.0, 6.1 e 6.2
* Foi adicionado suporte para estes sistemas operacionais de Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 e 7.2
  * CentOS 7.0, 7.1 e 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> O RHEL/CentOS 7 de 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** é empacotado com o pacote **InMage_Scout_Standard_8.0.1 GA.zip** base do Scout GA. Baixe o pacote do Scout GA do portal, conforme é descrito em criar um cofre.

#### <a name="bug-fixes-and-enhancements"></a>Correções de bug e melhorias

* Melhoria da manipulação de desligamento para os seguintes sistemas operacionais Linux e clones, para evitar problemas de ressincronização indesejados:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Para o Linux, todas as permissões de acesso de pasta no diretório de instalação do agente unificado agora são restritas somente ao usuário local.
* No Windows, uma correção para um problema de tempo limite que ocorria ao emitir indicadores de consistência distribuídos comuns, em aplicativos distribuídos muito carregados, como clusters do SQL Server e do Share Point.
* Uma correção relacionada a log no instalador base do servidor de configuração.
* Um link de download para o VMware vCLI 6.0 foi adicionado ao instalador base de destino mestre do Windows.
* Logs e verificações adicionais foram adicionados, para alterações de configuração de rede durante as simulações de recuperação de desastre e failover.
* Uma correção para um problema que fazia com que as informações de retenção não fossem relatadas ao servidor de configuração.  
* Para clusters físicos, uma correção para um problema que causava falha do redimensionamento de volume no assistente do vContinuum, ao reduzir o volume de origem.
* Uma correção para um problema de proteção de cluster que falhou com erro: "Falha ao localizar a assinatura do disco", quando o disco de cluster for um disco PRDM.
* Uma correção para uma falha de servidor de transporte cxps causado por uma exceção fora do intervalo.
* Os nome do servidor e as colunas de endereço IP agora são ajustáveis na página **Instalação por Push** do assistente do vContinuum.
* Melhorias da API RX:
  * Os cinco pontos de consistência comuns mais recentes disponíveis agora estão disponíveis (apenas marcas garantidas).
  * Os detalhes de espaço livre e de capacidade são exibidos para todos os dispositivos protegidos.
  * O estado do driver do Scout no servidor de origem está disponível.

> [!NOTE]
> * O pacote base **InMage_Scout_Standard_8.0.1_GA.zip** tem:
>     * Um instalador base do servidor de configuração atualizado (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Um instalador de base de destino mestre do Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * Para todas as novas instalações, use o novo servidor de configuração e os bits de GA do destino mestre do Windows.
> * A Atualização 4 pode ser aplicada diretamente no GA 8.0.1.
> * As atualizações do servidor de configuração e do RX não poderão ser revertidas depois que forem aplicadas.


### <a name="azure-site-recovery-scout-801-update-3"></a>Atualização 3 do Azure Site Recovery Scout 8.0.1

Todas as atualizações de Recuperação de Site são cumulativas. A Atualização 3 contém todas as correções da Atualização 1 e da Atualização 2. A Atualização 3 pode ser aplicada diretamente a 8.0.1 GA. As atualizações do servidor de configuração e do RX não poderão ser revertidas depois que forem aplicadas.

#### <a name="bug-fixes-and-enhancements"></a>Correções de bug e melhorias
A Atualização 3 corrige os problemas a seguir:

* O servidor de configuração e o RX não são registrados no cofre quando estão atrás do proxy.
* O número de horas em que o RPO (objetivo de ponto de recuperação) não foi atingido não é atualizado no relatório de integridade.
* O servidor de configuração não sincroniza com RX quando os detalhes de hardware do ESX ou os detalhes da rede contêm caracteres UTF-8.
* Os controladores de domínio do Windows Server 2008 R2 não iniciam após a recuperação.
* A sincronização offline não está funcionando conforme o esperado.
* Após o failover da VM, a exclusão do par de replicação não progride no console do servidor de configuração por um longo tempo. Os usuários não conseguem concluir o failback ou retomar as operações.
* As operações de instantâneo gerais que são feitas pelo trabalho de consistência foram otimizadas para ajudar a reduzir desconexões de aplicativo, como clientes do SQL Server.
* O desempenho da ferramenta de consistência (VACP.exe) melhorou. O uso de memória necessário para a criação de instantâneos no Windows foi reduzido.
* O serviço de instalação por push falha quando a senha tem mais de 16 caracteres.
* O vContinuum não verifica e solicita novas credenciais do vCenter quando as credenciais são modificadas.
* No Linux, o gerenciador de cache de destino mestre (cachemgr) não baixa arquivos do servidor de processo. Isso resulta na limitação do par de replicação.
* Quando a ordem de disco de MSCS (cluster de failover físico) não é a mesma em todos os nós, a replicação não é definida para alguns dos volumes de cluster. O cluster deve ser protegido novamente para usufruir dessa correção.  
* A funcionalidade de SMTP não funciona como esperado depois que o RX é atualizado do Scout 7.1 para o Scout 8.0.1.
* Foram adicionadas mais estatísticas no log para que a operação de reversão acompanhe o tempo gasto para concluí-la.
* Foi adicionado suporte para sistemas operacionais Linux no servidor de origem:
  * Red Hat Enterprise Linux (RHEL) 6 atualização 7
  * CentOS 6 Atualização 7
* Os consoles do servidor de configuração e do RX agora mostram notificações para o par que entra no modo de bitmap.
* As seguintes correções de segurança foram adicionadas no RX:
    * Bypass de autorização via adulteração de parâmetros: Acesso restringido a usuários não aplicáveis.
    * Solicitação intersite forjada: O conceito de token da página foi implementado e é gerado aleatoriamente para cada página. Isso significa que há apenas uma única instância de entrada única para o mesmo usuário e a atualização de página não funciona. Nessa caso, ela redireciona para o painel.
    * Upload de arquivo mal-intencionado: Os arquivos estão restritos a extensões específicas: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e zip.
    * Script intersite persistente: Validações de entrada foram adicionadas.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Atualização 2 (atualização de 3 de dezembro de 2015)

As correções na Atualização 2 incluem:

* **Servidor de configuração**: Problemas que impediram que o recurso de medição gratuita de 31 dias funcionasse conforme esperado, quando o servidor de configuração foi registrado no cofre do Azure Site Recovery.
* **Agente unificado**: Correção para um problema na Atualização 1 que resultou na atualização não sendo instalada no servidor de destino mestre durante upgrade da versão 8.0 para 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Atualização 1
A atualização 1 inclui as seguintes correções de bugs e novos recursos:

* 31 dias de proteção gratuita por instância de servidor. Isso permite testar a funcionalidade ou definir uma prova de conceito.
* Todas as operações no servidor, inclusive failover e failback, são gratuitas nos primeiros 31 dias. O tempo começa quando um servidor é protegido pela primeira vez com o Site Recovery Scout. A partir do 32º dia, todos os servidores protegidos são cobrados na taxa padrão de instância pela proteção do Azure Site Recovery para um site de propriedade do cliente.
* O número de servidores protegidos que estão sendo cobrados no momento está sempre disponível no **Painel** no cofre.
* Foi adicionado suporte para vCLI (Interface de Linha de Comando do vSphere) na 5.5 Atualização 2.
* Foi adicionado suporte para estes sistemas operacionais Linux no servidor de origem:
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* Correções de bugs para resolver estes problemas:
  * Falha no registro do cofre para o servidor de configuração ou o servidor RX.
  * Os volumes de cluster não aparecem conforme o esperado quando as VMs clusterizadas são protegidas novamente para que possam retomar.
  * O failback falha quando o servidor de destino mestre é hospedado em um outro servidor ESXi das VMs locais de produção.
  * As permissões de arquivo de configuração são alteradas quando você atualiza para 8.0.1. Esta alteração afeta a proteção e as operações.
  * O limite de ressincronização não é imposto conforme o esperado, causando um comportamento de replicação inconsistente.
  * As configurações de RPO não são exibidas corretamente no console do servidor de configuração. O valor de dados não compactados mostra incorretamente o valor compactado.
  * A operação de remoção não exclui conforme esperado no assistente do vContinuum e a replicação não é excluída no console do servidor de configuração.
  * No assistente do vContinuum, o disco é desmarcado automaticamente quando você clica em **Detalhes** na exibição do disco durante a proteção de VMs MSCS.
  * No cenário de P2V (físico para virtual), os serviços HP necessários (como CIMnotify e CqMgHost) não são movidos para manual na recuperação de VM. Esse problema resulta em mais tempo de inicialização.
  * Falha na proteção de VM do Linux quando há mais de 26 discos no servidor de destino mestre.

