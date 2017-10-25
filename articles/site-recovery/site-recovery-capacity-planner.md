---
title: "Estimar a capacidade de replicação no Azure | Microsoft Docs"
description: Use este artigo para estimar a capacidade ao replicar com o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: nisoneji
ms.openlocfilehash: 134e17ebda3105be2b53d072fdef7aeda4a98bde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>Planejar a capacidade de proteger máquinas virtuais e o servidor físico no Azure Site Recovery

A ferramenta Planejador de Capacidade do Azure Site Recovery o ajuda a descobrir os requisitos de capacidade ao replicar VMs Hyper-V, VMs VMware e servidores físicos Windows/Linux com o Azure Site Recovery.

Use o Planejador de Capacidade do Site Recovery para analisar o ambiente de origem e as cargas de trabalho, estimar as necessidades de largura de banda e recursos do servidor necessários no local de origem, bem como os recursos (máquinas virtuais e armazenamento, etc.) necessários no local de destino.

Você pode executar a ferramenta em vários modos:

* **Planejamento rápido**: execute a ferramenta nesse modo para obter as projeções de rede e de servidor baseadas no número médio de VMs, de discos, de armazenamento e da taxa de alteração.
* **Planejamento detalhado**: execute a ferramenta nesse modo e forneça detalhes de cada carga de trabalho no nível da VM. Analise a compatibilidade da VM e obtenha as projeções de rede e de servidor.

## <a name="before-you-start"></a>Antes de começar


1. Reúna informações sobre seu ambiente, inclusive VMs, discos por VM e armazenamento por disco.
2. Identifique sua taxa de alteração (variação) diária de dados replicados. Para fazer isso:

   * Se você estiver replicando VMs Hyper-V, baixe a [ferramenta de planejamento de capacidade do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de alteração. [Saiba mais](site-recovery-capacity-planning-for-hyper-v-replication.md) sobre essa ferramenta. Recomendamos que você execute essa ferramenta durante uma semana para capturar as médias.
   * Se você estiver replicando máquinas virtuais VMware, use o [Planejador de Implantação do Azure Site Recovery](./site-recovery-deployment-planner.md) para calcular a taxa de variação.
   * Se você estiver replicando servidores físicos, precisará estimá-la manualmente.

## <a name="run-the-quick-planner"></a>Execute o Planejador Rápido
1. Baixe e abra a ferramenta [Planejador de Capacidade do Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel) . Você precisa executar macros; portanto, habilite a edição e o conteúdo quando solicitado.
2. Em **Selecionar um tipo de planejador**, selecione **Planejador Rápido** na caixa de listagem.

   ![Introdução](./media/site-recovery-capacity-planner/getting-started.png)
3. Na planilha **Planejador de Capacidade**, insira as informações necessárias. Preencha todos os campos envoltos em vermelho na captura de tela abaixo.

   * Em **Selecionar seu cenário**, escolha **Hyper-V para Azure** ou **VMware/físico para Azure**.
   * Em **Taxa média diária de alteração de dados (%)**, insira as informações coletadas usando a [ferramenta de planejamento de capacidade do Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou o [Planejador de Implantação do Azure Site Recovery](./site-recovery-deployment-planner.md).  
   * **Compactação** se aplica somente à compactação oferecida ao replicar VMs ou servidores físicos da VMware para o Azure. Estimamos 30% ou mais, mas você pode modificar a configuração, conforme necessário. Para replicar VMs Hyper-V para compactação do Azure, você pode usar uma solução de terceiros, como o Riverbed.
   * Em **Entradas de Retenção**, especifique por quanto tempo as réplicas devem ser retidas. Se você estiver replicando VMware ou servidores físicos, insira o valor em dias. Se você estiver replicando Hyper-V, especifique o tempo em horas.
   * Em **Número de horas em que a replicação inicial para o lote de máquinas virtuais deve ser concluída** e em **Número de máquinas virtuais por lote de replicação inicial**, insira as configurações usadas para calcular os requisitos iniciais de replicação.  Quando o Site Recovery é implantado, o conjunto inteiro de dados inicial deve ser carregado.

   ![Entradas](./media/site-recovery-capacity-planner/inputs.png)
4. Depois de inserir os valores para o ambiente de origem, a saída exibida incluirá:

   * **Largura de banda necessária para a replicação delta** (MB/s). A largura de banda de rede para a replicação delta é calculada segundo a taxa média diária de alteração de dados.
   * **Largura de banda necessária para a replicação inicial** (MB/s). A largura de banda de rede para a replicação inicial é calculada segundo os valores de replicação inicial inseridos.
   * **Armazenamento necessário (em GB)** é o armazenamento do Azure total necessário.
   * **IOPS total nas contas de armazenamento padrão** é calculado de acordo com o tamanho da unidade de IOPS de 8.000 no total de contas de armazenamento standard.  Para o Planejador Rápido, o número é calculado com base em todos os discos de VMs de origem e na taxa diária de alteração dos dados. Para o Planejador Detalhado, o número é calculado com base no número total de VMs mapeadas para as VMs standard do Azure e na taxa diária de alteração dessas VMs.
   * **Número de contas de armazenamento standard** fornece o número total de contas de armazenamento standard necessárias para proteger as VMs. Uma conta de armazenamento standard pode conter até 20 mil IOPS em todas as VMs em um armazenamento standard e há suporte de, no máximo, 500 IOPS por disco.
   * **Número de discos de blob necessários** retorna o número de discos que serão criados no armazenamento do Azure.
   * **Número de contas de armazenamento premium necessárias** fornece o número total de contas de armazenamento premium necessárias para proteger as VMs. Uma VM de origem com IOPS alto (acima de 20 mil) precisa de uma conta de armazenamento premium. Uma conta de armazenamento premium pode armazenar até 80 mil IOPS.
   * **IOPS total no armazenamento premium** é calculado de acordo com o tamanho da unidade de IOPS de 256.000 no total de contas de armazenamento premium.  Para o Planejador Rápido, o número é calculado com base em todos os discos de VMs de origem e na taxa diária de alteração dos dados. Para o Planejador Detalhado, o número é calculado com base no número total de VMs mapeadas para as VMs premium do Azure (séries DS e GS) e na taxa diária de alteração dessas VMs.
   * **Número de servidores de configuração necessários** mostra quantos servidores de configuração são necessários para a implantação.
   * **Número de servidores em processo adicionais necessários** mostra se servidores em processo adicionais são necessários, além do servidor em processo em execução no servidor de configuração, por padrão.
   * **Armazenamento adicional de 100% na origem** mostra se o armazenamento adicional é necessário no local de origem.

   ![Saída](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Executar o Planejador Detalhado

1. Baixe e abra a ferramenta [Planejador de Capacidade do Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel) . Você precisa executar macros; portanto, habilite a edição e o conteúdo quando solicitado.
2. Em **Selecionar um tipo de planejador**, selecione **Planejador Detalhado** na caixa de listagem.

   ![Introdução](./media/site-recovery-capacity-planner/getting-started-2.png)
3. Na planilha **Qualificação de Carga de Trabalho**, insira as informações necessárias. Preencha todos os campos marcados.

   * Em **Núcleos de processador**, especifique o número total de núcleos em um servidor de origem.
   * Em **Alocação de memória em MB**, especifique o tamanho da RAM de um servidor de origem.
   * O **Número de NICs** especifica o número de adaptadores de rede em um servidor de origem.
   * Em **Total de armazenamento (em GB)**, especifique o tamanho total do armazenamento da VM. Por exemplo, se o servidor de origem tiver três discos, cada um com 500 GB, o tamanho total de armazenamento será de 1.500 GB.
   * Em **Número de discos anexados**, especifique o número total de discos de um servidor de origem.
   * Em **Utilização da capacidade do disco**, especifique a utilização média.
   * Em **Taxa de alteração diária (%)**, especifique a taxa diária de alteração de dados de um servidor de origem.
   * Em **Mapeando o tamanho do Azure**, insira o tamanho da VM do Azure que você deseja mapear. Se você não desejar fazer isso manualmente, clique em **VMs de IaaS de Computação**. Se você inserir uma configuração manual e clicar em VMs de IaaS de Computação, a configuração manual poderá ser substituída, pois o processo de computação identifica automaticamente a melhor correspondência no tamanho da VM do Azure.

   ![Qualificação de Carga de Trabalho](./media/site-recovery-capacity-planner/workload-qualification.png)
4. Se você clicar em **VMs IaaS de Computação** , isso é o que essa opção faz:

   * Valida as entradas obrigatórias.
   * Calcula o IOPS e sugere a melhor correspondência de VM do Azure para cada VMs que é qualificada para a replicação no Azure. Se não for possível detectar um tamanho adequado de VM do Azure, um erro será emitido. Por exemplo, se o número de discos anexados for 65, um erro será emitido, já que o maior tamanho de VM do Azure é 64.
   * Sugere uma conta de armazenamento que pode ser usada para uma VM do Azure.
   * Calcula quantas contas de armazenamento standard e premium são necessárias para a carga de trabalho. Role para baixo para exibir o tipo de armazenamento do Azure e a conta de armazenamento que pode ser usada para um servidor de origem.
   * Conclui e classifica o restante da tabela baseado no tipo de armazenamento necessário (standard ou premium) atribuído a uma máquina virtual e no número de discos anexados. Para todas as VMs que atendem aos requisitos do Azure, a coluna **A VM está qualificada?** mostra **Sim**. Se não for possível fazer backup de uma VM no Azure, um erro será mostrado.

As colunas AA a AE são saídas e fornecem informações de cada VM.

![Qualificação de Carga de Trabalho](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exemplo
Como exemplo, para seis VMs com os valores mostrados na tabela, a ferramenta calcula e atribui a melhor correspondência de VM do Azure e os requisitos de armazenamento do Azure.

![Qualificação de Carga de Trabalho](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Na saída de exemplo, observe o seguinte:

  * A primeira coluna é uma coluna de validação para VMs, discos e variação.
  * São necessárias duas contas de armazenamento padrão e uma conta de armazenamento premium para cinco VMs.
  * A VM3 não se qualifica para proteção, pois um ou mais discos tem mais de 1 TB.
  * A VM1 e a VM2 podem usar a primeira conta de armazenamento padrão
  * A VM4 pode usar a segunda conta de armazenamento padrão.
  * A VM5 e a VM6 precisam de uma conta de armazenamento premium e podem usar a mesma conta.

    > [!NOTE]
    > O IOPS no armazenamento standard e premium é calculado no nível da VM e não no disco. Uma máquina virtual padrão pode manipular até 500 IOPS por disco. Se o IOPS de um disco for maior que 500, você precisará do armazenamento premium. No entanto, se o IOPS de um disco for superior a 500, mas o IOPS do total de discos de VM estiver dentro dos limites de VM standard do Azure com suporte (tamanho da VM, número de discos, número de adaptadores, CPU e memória), o planejador escolherá uma VM standard e não uma da série DS ou GS. Você precisa atualizar manualmente a célula de tamanho do Azure de mapeamento com a VM da série DS ou GS apropriada.


Depois que todos os detalhes estiverem prontos, clique em **Enviar dados para a ferramenta de planejamento** para abrir o **Planejador de Capacidade**. As cargas de trabalho são realçadas para mostrar se estão qualificadas ou não para proteção.

### <a name="submit-data-in-the-capacity-planner"></a>Enviar dados no Planejador de Capacidade
1. Ao abrir o **Planejador de Capacidade** , a planilha é preenchida com base nas configurações que você especificou. As palavras “Carga de trabalho” são exibidas na célula **Fonte de entradas de infraestrutura**, para mostrar que a entrada é a planilha **Qualificação da Carga de Trabalho**.
2. Se você desejar fazer alterações, será necessário modificar a planilha **Qualificação da Carga de Trabalho** e clicar em **Enviar dados para a ferramenta do planejador** novamente.  

   ![Planejador de Capacidade](./media/site-recovery-capacity-planner/capacity-planner.png)
