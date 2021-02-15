---
title: Planejar a capacidade de recuperação de desastres do Hyper-V com Azure Site Recovery
description: Use este artigo para estimar a capacidade durante a configuração de recuperação de desastre com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: a5764e44db31755110ac99a3e8e8e0984cdf9604
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87490567"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planejar a capacidade de recuperação de desastre de VM do Hyper-V 

O [Planejador de Implantações do Azure Site Recovery] (site-recovery-hyper-v-deployment-planner.md) para a implantação do Hyper-V para o Azure fornece o seguinte:

* Avaliação de qualificação de VM com base em número de discos, tamanho de disco, IOPS, variações e algumas características da VM
* Avaliação de largura de banda de rede necessária versus RPO
* Requisitos de infraestrutura do Azure
* Requisitos de infraestrutura local
* Diretriz de envio em lote da replicação inicial
* Custo estimado total de recuperação de desastre para Azure


O Planejador de Capacidade do Azure Site Recovery ajuda a determinar os requisitos de capacidade ao replicar VMs Hyper-V com o Azure Site Recovery.

Use o Planejador de Capacidade do Site Recovery para analisar o ambiente de origem e as cargas de trabalho. Ele ajuda a estimar necessidades de largura de banda, os recursos de servidor de que você precisa para o local de origem e os recursos (como VMs e armazenamento) de que precisa no local de destino.

Você pode executar a ferramenta em dois modos:

* **Planejamento rápido**: fornece projeções de rede e servidor com base no número médio de VMs, discos, armazenamento e taxa de alteração.
* **Planejamento detalhado**: fornece detalhes de cada carga de trabalho no nível da VM. Analise a compatibilidade da VM e obtenha as projeções de rede e de servidor.

## <a name="before-you-start"></a>Antes de começar

* Reúna informações sobre seu ambiente, inclusive VMs, discos por VM e armazenamento por disco.
* Identifique sua taxa de alteração (variação) diária de dados replicados. Baixe a [ferramenta de planejamento de capacidade do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de alteração. [Saiba mais](./hyper-v-deployment-planner-overview.md) sobre essa ferramenta. Recomendamos que você execute essa ferramenta durante uma semana para capturar as médias.


## <a name="run-the-quick-planner"></a>Execute o Planejador Rápido
1. Baixe e abra o [Planejador de Capacidade do Site Recovery ](https://aka.ms/asr-capacity-planner-excel). Você precisa executar macros. Quando você for solicitado, faça seleções para habilitar a edição e o conteúdo.

2. Na caixa de listagem **Selecionar um tipo de planejador**, selecione **Planejador Rápido**.

   ![Captura de tela da opção Selecionar um tipo de planejador, com o planejador rápido selecionado.](./media/site-recovery-capacity-planner/getting-started.png)

3. Na planilha **Planejador de Capacidade**, insira as informações necessárias. Preencha todos os campos circulados em vermelho na seguinte captura de tela:

   a. Em **Selecionar seu cenário**, escolha **Hyper-V para Azure** ou **VMware/físico para Azure**.

   b. Em **Taxa média diária de alteração de dados (%)**, insira as informações coletadas usando a [ferramenta de planejamento de capacidade do Hyper-V](./hyper-v-deployment-planner-overview.md) ou o [Planejador de Implantação do Site Recovery](./site-recovery-deployment-planner.md).

   c. A configuração **Compactação** não é usada quando você replica VMs do Hyper-V para o Azure. Para compactação, use um dispositivo de terceiros como o Riverbed.

   d. Em **Retenção em dias**, especifique, em dias, por quanto tempo manter réplicas.

   e. Em **Número de horas em que a replicação inicial para o lote de máquinas virtuais deve ser concluída** e em **Número de máquinas virtuais por lote de replicação inicial**, insira as configurações usadas para calcular os requisitos iniciais de replicação. Quando o Site Recovery é implantado, o conjunto inteiro de dados inicial é carregado.

   ![Captura de tela da planilha Planejador de Capacidade, mostrando as informações de entrada necessárias.](./media/site-recovery-capacity-planner/inputs.png)

4. Depois que você inserir os valores para o ambiente de origem, a saída exibida incluirá:

   * **Largura de banda necessária para replicação delta (em megabits/s)**: a largura de banda de rede para a replicação delta é calculada segundo a taxa média diária de alteração de dados.
   * **Largura de banda necessária para replicação inicial (em megabits/s)**: a largura de banda de rede para a replicação inicial é calculada com base nos valores de replicação inicial inseridos.
   * **Armazenamento necessário (em GB)**: é o armazenamento do Azure total necessário.
   * **IOPS total em armazenamento padrão**: o número é calculado de acordo com o tamanho da unidade de IOPS de 8.000 no total de contas de armazenamento standard. Para o Planejador Rápido, o número é calculado com base em todos os discos de VM de origem e na taxa diária de alteração dos dados. Para o Planejador Detalhado, o número é calculado com base no número total de VMs mapeadas para as VMs standard do Azure e na taxa diária de alteração dessas VMs.
   * **Número de contas de armazenamento Standard necessários**: o número total de contas de armazenamento standard necessárias para proteger as VMs. Uma conta de armazenamento standard pode conter até 20 mil IOPS em todas as VMs em um armazenamento standard. Há suporte para o máximo de 500 IOPS por disco.
   * **Número de discos BLOB necessários**: o número de discos criados no armazenamento do Azure.
   * **Número de contas premium necessárias**: o número total de contas de armazenamento premium necessárias para proteger as VMs. Uma VM de origem com IOPS alto (acima de 20.000 mil) precisa de uma conta de armazenamento premium. Uma conta de armazenamento premium pode armazenar até 80.000 mil IOPS.
   * **IOPS total no armazenamento Premium**: o número é calculado de acordo com o tamanho da unidade de IOPS de 256.000 no total de contas de armazenamento premium. Para o Planejador Rápido, o número é calculado com base em todos os discos de VM de origem e na taxa diária de alteração dos dados. Para o Planejador Detalhado, o número é calculado com base no número total de VMs mapeadas para as VMs premium do Azure (séries DS e GS) e na taxa diária de alteração dessas VMs.
   * **Número de servidores de configuração necessários**: mostra quantos servidores de configuração são necessários para a implantação.
   * **Número de servidores em processo adicionais necessários**: mostra se servidores em processo adicionais são necessários, além do servidor em processo em execução no servidor de configuração, por padrão.
   * **100% armazenamento adicional na origem**: mostra se o armazenamento adicional é necessário no local de origem.

      ![Captura de tela da saída exibida com base na entrada fornecida.](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Executar o Planejador Detalhado

1. Baixe e abra o [Planejador de Capacidade do Site Recovery ](https://aka.ms/asr-capacity-planner-excel). Você precisa executar macros. Quando você for solicitado, faça seleções para habilitar a edição e o conteúdo.

2. Em **Selecionar um tipo de planejador**, selecione **Planejador Detalhado** na caixa de listagem.

   ![Captura de tela da opção Selecionar um tipo de planejador, com o planejador detalhado selecionado.](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Na planilha **Qualificação de Carga de Trabalho**, insira as informações necessárias. Preencha todos os campos marcados.

   a. Em **núcleos de processador**, especifique o número total de núcleos em um servidor de origem.

   b. Em **Alocação de memória (em MBs)**, especifique o tamanho da RAM de um servidor de origem.

   c. Em **Número de NICs** especifica o número de adaptadores de rede em um servidor de origem.

   d. Em **armazenamento total (em GB)**, especifique o tamanho total do armazenamento da VM. Por exemplo, se o servidor de origem tiver três discos, cada um com 500 GB, o tamanho total de armazenamento será de 1.500 GB.

   e. Em **Número de discos anexados**, especifique o número total de discos de um servidor de origem.

   f. Em **Utilização da capacidade do disco (%)**, especifique a utilização média.

   g. Em **Taxa de alteração de dados diária (%)**, especifique a taxa diária de alteração de dados de um servidor de origem.

   h. Em **Mapeando o tamanho da VM do Azure**, insira o tamanho da VM do Azure que você deseja mapear. Se você não quiser fazer isso manualmente, selecione **Computar VMs IaaS**. Se você inserir uma configuração manual e selecionar **Computar VMs IaaS**, a configuração manual poderá ser substituída. O processo de computação identifica automaticamente a melhor correspondência no tamanho da VM do Azure.

   ![Captura de tela da planilha de qualificação de carga de trabalho, mostrando as informações de entrada necessárias.](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Se você selecionar **VMs IaaS de Computação**, isso é o que essa opção faz:

   * Valida as entradas obrigatórias.
   * Calcula o IOPS e sugere a melhor correspondência de tamanho de VM do Azure para cada VMs que é qualificada para a replicação no Azure. Se não for possível detectar um tamanho adequado de VM do Azure, um erro será exibido. Por exemplo, se o número de discos anexados for 65, um erro será exibido, já que o maior tamanho para uma VM do Azure é 64.
   * Sugere uma conta de armazenamento que pode ser usada para uma VM do Azure.
   * Calcula quantas contas de armazenamento standard e premium são necessárias para a carga de trabalho. Role para baixo para exibir o tipo de armazenamento do Azure e a conta de armazenamento que pode ser usada para um servidor de origem.
   * Conclui e classifica o restante da tabela baseado no tipo de armazenamento necessário (standard ou premium) atribuído a uma VM e no número de discos anexados. Para todas as VMs que atendem aos requisitos do Azure, a coluna **A VM está qualificada?** mostra **Sim**. Se não for possível fazer backup de uma VM no Azure, um erro será exibido.

As colunas AA a AE são saídas e fornecem informações de cada VM.

![Captura de tela mostrando as colunas de saída AA para AE.](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exemplo
Como exemplo, para seis VMs com os valores mostrados na tabela, a ferramenta calcula e atribui a melhor correspondência de VM do Azure e os requisitos de armazenamento do Azure.

![Captura de tela mostrando atribuições de qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Na saída de exemplo, observe o seguinte:

  * A primeira coluna é uma coluna de validação para VMs, discos e variação.
  * São necessárias duas contas de armazenamento padrão e uma conta de armazenamento premium para cinco VMs.
  * A VM3 não se qualifica para proteção, pois um ou mais discos tem mais de 1 TB.
  * A VM1 e a VM2 podem usar a primeira conta de armazenamento padrão
  * A VM4 pode usar a segunda conta de armazenamento padrão.
  * A VM5 e a VM6 precisam de uma conta de armazenamento premium e podem usar a mesma conta.

    > [!NOTE]
    > O IOPS no armazenamento standard e premium é calculado no nível da VM e não no nível do disco. Uma VM padrão pode manipular até 500 IOPS por disco. Se o IOPS de um disco for maior que 500, você precisará do armazenamento premium. Se o IOPS de um disco for superior a 500, mas o IOPS do total de discos de VM estiver dentro dos limites de VM standard do Azure com suporte, o planejador escolherá uma VM standard e não uma da série DS ou GS. (Os limites de VM do Azure são o tamanho da VM, o número de discos, o número de adaptadores, a CPU e a memória.) Você precisa atualizar manualmente a célula de tamanho do Azure de mapeamento com a VM da série DS ou GS apropriada.


Depois que todas as informações forem inseridas, selecione **Enviar dados para a ferramenta de planejador** para abrir o Planejador de Capacidade. As cargas de trabalho são realçadas para mostrar se elas estão qualificadas para proteção.

### <a name="submit-data-in-capacity-planner"></a>Enviar dados no Planejador de Capacidade
1. Ao abrir o **Planejador de Capacidade**, a planilha é preenchida com base nas configurações que você especificou. A palavra "carga de trabalho" aparece na célula de **origem de entradas** de infraestrutura para mostrar que a entrada é a planilha de qualificação de **carga de trabalho** .

2. Se você desejar fazer alterações, será necessário modificar a planilha **Qualificação da Carga de Trabalho**. Em seguida, selecione **Enviar dados para a ferramenta de planejador** novamente.

   ![Captura de tela mostrando as entradas modificadas e as saídas resultantes na planilha Planejador de Capacidade.](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Próximas etapas
[Saiba como executar](./hyper-v-deployment-planner-overview.md) a ferramenta de planejamento de capacidade.
