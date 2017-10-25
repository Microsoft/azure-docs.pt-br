---
title: Executar a ferramenta Planejador de capacidade do Hyper-V para o Site Recovery | Microsoft Docs
description: Este artigo descreve como executar a ferramenta Planejador de capacidade do Hyper-V para o Azure Site Recovery
services: site-recovery
documentationcenter: na
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 2bc3832f-4d6e-458d-bf0c-f00567200ca0
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: nisoneji
ms.openlocfilehash: 272b5abb5e6451164ca7900dda399b6aac65f986
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-the-hyper-v-capacity-planner-tool-for-site-recovery"></a>Execute a ferramenta de planejador de capacidade do Hyper-V para Site Recovery

Como parte da implantação do Azure Site Recovery, você precisa descobrir seus requisitos de replicação e largura de banda. A ferramenta Planejador de capacidade do Hyper-V para o Site Recovery ajuda a fazer isso, para a replicação de máquina virtual do Hyper-V.

Este artigo descreve como executar a ferramenta Planejador de Capacidade do Hyper-V. Essa ferramenta deve ser usada junto com as informações no [planejamento de capacidade para o Site Recovery](site-recovery-capacity-planner.md).

## <a name="before-you-start"></a>Antes de começar
Execute a ferramenta em um nó de cluster ou de servidor do Hyper-V no site primário. Para executar a ferramenta, os servidores de host Hyper-V precisam do seguinte:

* Sistema operacional: Windows Server 2012 ou 2012 R2
* Memória: 20 MB (mínimo)
* CPU: sobrecarga de 5% (mínimo)
* Espaço em disco: 5 MB (mínimo)

Antes de executar a ferramenta, é necessário preparar o site primário. Se você estiver replicando entre dois sites locais e desejar verificar a largura de banda, também será necessário preparar um servidor de réplica.

## <a name="step-1-prepare-the-primary-site"></a>Etapa 1: Preparar o site primário

1. No site primário, faça uma lista de todas as VMs Hyper-V que você deseja replicar e os hosts/clusters do Hyper-V nos quais elas estão localizadas. A ferramenta pode ser executada individualmente para vários hosts independentes ou para um único cluster, mas não para os dois juntos. Também é necessário executá-la separadamente para cada sistema operacional. Portanto, você deve coletar informações sobre os servidores Hyper-V da seguinte maneira:

   * Servidores independentes Windows Server 2012
   * Clusters do Windows Server 2012
   * Servidores independentes Windows Server 2012 R2
   * Clusters do Windows Server 2012 R2
2. Habilite o acesso remoto ao WMI em todos os hosts e clusters Hyper-V. Execute este comando em cada servidor/cluster para ter certeza de que as regras de firewall e as permissões de usuário estão definidas:

        netsh firewall set service RemoteAdmin enable
3. Habilite o monitoramento do desempenho nos servidores e clusters da seguinte maneira:

   * Abra o Firewall do Windows com o snap-in **Segurança Avançada** e habilite as seguintes regras de entrada: **Acesso à Rede COM+ (DCOM-IN)** e todas as regras no **grupo Gerenciamento Remoto do Log de Eventos**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Etapa 2: Preparar um servidor de réplica (replicação local para o local)
Não será necessário fazer isso se você estiver replicando para o Azure.

Recomendamos a configuração de um único host Hyper-V como servidor de recuperação, para que uma VM fictícia possa ser replicada nele a fim de verificar a largura de banda.  Você poderá ignorar essa etapa, mas só será possível medir a largura de banda se fizer isso.

1. Se você quiser usar um nó de cluster como a réplica, configure o agente da Réplica do Hyper-V:

   * Em **Gerenciador do Servidor**, abra **Gerenciador de Cluster de Failover**.
   * Conecte-se ao cluster, realce o nome do cluster e clique em **Ações** > **Configurar Função** para abrir o assistente para Alta Disponibilidade.
   * Em **Selecionar Função**, clique em **Agente de Réplica do Hyper-V**. No assistente, forneça um **Nome NetBIOS** e o **Endereço IP** que serão usados como o ponto de conexão para o cluster (chamado de ponto de acesso do cliente). O **Agente de Réplica do Hyper-V** será configurado, resultando em um nome de ponto de acesso de cliente. Anote esse nome.
   * Verifique se a função de Agente de Réplica do Hyper-V fica online com êxito e se pode executar failover entre todos os nós do cluster. Para fazer isso, clique com o botão direito do mouse na função, aponte para **Mover** e clique em **Selecionar Nó**. Selecione um nó > **OK**.
   * Se você estiver usando a autenticação baseada em certificado, verifique se cada nó do cluster e o ponto de acesso do cliente têm os certificados instalados.
2. Habilite um servidor de réplica:

   * Para um cluster, abra o Gerenciador de Cluster de Failover, conecte-se ao cluster e clique em **Funções** > selecionar função > **Configurações de Replicação** > **Habilitar este cluster como um servidor de Réplica**. Se você usar um cluster como a réplica, também será necessário ter a função do Agente de Réplica do Hyper-V no cluster no site primário.
   * Para um servidor autônomo, abra o Gerenciador Hyper-V. No painel **Ações**, clique em **Configurações do Hyper-V** para o servidor que você deseja habilitar e, em **Configuração de Replicação**, clique em **Habilitar este computador como um servidor de Réplica**.
3. Configure a autenticação:

   * Em **Autenticação e portas**, selecione como autenticar o servidor primário e as portas de autenticação. Se você usar um certificado, clique em **Selecionar Certificado** para selecionar um. Use o Kerberos se os hosts primário e de recuperação do Hyper-V estiverem no mesmo domínio ou em domínios confiáveis. Use certificados para domínios diferentes ou para uma implantação de grupo de trabalho.
   * Na seção **Autorização e Armazenamento**, permita que **qualquer** servidor autenticado (primário) envie dados de replicação para este servidor de réplica.

     ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)
   * Execute **netsh http show servicestate** para verificar se o ouvinte está em execução para o protocolo/porta especificado:  
4. Configure os firewalls. Durante a instalação do Hyper-V, as regras de firewall são criadas para permitir o tráfego nas portas padrão (HTTPS na 443 e Kerberos na 80). Habilite essas regras da seguinte maneira:
  - Autenticação de certificado no cluster (443): ``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}``
  - Autenticação Kerberos no cluster (80): ``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}``
  - Autenticação de certificado no servidor independente: ``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"``
  - Autenticação Kerberos no servidor independente: ``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"``

## <a name="step-3-run-the-capacity-planner-tool"></a>Etapa 3: Executar a ferramenta de planejamento de capacidade
Após a preparação do site primário e a configuração de um servidor de recuperação, você poderá executar a ferramenta.

1. [Baixe](https://www.microsoft.com/download/details.aspx?id=39057) a ferramenta do Centro de Download da Microsoft.
2. Execute a ferramenta em um dos servidores primários (ou um de nós do cluster primário). Clique com o botão direito do mouse no arquivo .exe e selecione **Executar como administrador**.
3. Em **Antes de começar**, especifique por quanto tempo você deseja coletar dados. Recomendamos a execução da ferramenta durante as horas de produção para garantir que os dados sejam representativos. Se estiver apenas tentando validar a conectividade da rede, escolha coletar por apenas um minuto.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)
4. Em **Detalhes do site primário**, especifique o nome ou o FQDN do servidor para um host independente ou, para um cluster, especifique o FQDN do ponto de acesso do cliente, o nome do cluster ou qualquer nó no cluster e clique em **Avançar**. A ferramenta detecta automaticamente o nome do servidor no qual está em execução. A ferramenta seleciona as VMs que podem ser monitoradas para os servidores especificados.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)
5. Em **Detalhes do Site de Réplica**, se você estiver replicando para o Azure, ou se estiver replicando para um data center secundário e ainda não tiver configurado um servidor de réplica, selecione **Ignorar os testes que envolvem o site de réplica**. Se você estiver replicando para um data center secundário e tiver configurado um tipo de réplica, insira o FQDN do servidor independente ou o ponto de acesso de cliente para o cluster em **Nome do servidor (ou) CAP do Agente de Réplica do Hyper-V**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)
6. Em **Detalhes de Réplica Estendida**, habilite **Ignorar os testes que envolvem o site de Réplica Estendida**. Não há suporte para eles no Site Recovery.
7. Em **Escolher VMs para Replicar**, as ferramentas se conectam ao servidor ou cluster e exibem as VMs e os discos em execução no servidor primário, de acordo com as configurações especificadas na página **Detalhes do Site Primário**. As VMs já foram habilitadas para replicação, ou as que não estão em execução não serão exibidas. Selecione as VMs das quais você deseja coletar métricas. A seleção dos VHDs também coleta automaticamente os dados para as VMs.
8. Se você tiver configurado um servidor de réplica ou um cluster, em **Informações de rede**, especifique a largura de banda de WAN aproximada que você acha que usará entre os sites primário e de réplica e selecione os certificados se você tiver configurado a autenticação de certificado.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)
9. Em **Resumo**, verifique as configurações e clique em **Avançar** para começar a coletar as métricas. O progresso e o status da ferramenta são exibidos na página **Calcular Capacidade** . Após a conclusão da execução da ferramenta, clique em **Exibir Relatório** para exibir a saída. Por padrão, os relatórios e logs são armazenados em **%systemdrive%\Users\Public\Documents\Capacity Planner**.

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

## <a name="step-4-interpret-the-results"></a>Etapa 4: Interpretar os resultados

Estas são as métricas importantes. Você pode ignorar as métricas que não estão listadas aqui. Elas não são relevantes para a Recuperação de Site.

### <a name="on-premises-to-on-premises-replication"></a>Replicação de local para local

* Impacto da replicação na computação do host primário, memória
* Impacto da replicação no espaço do disco de armazenamento dos hosts primário e de recuperação, IOPS
* Largura de banda total necessária para a replicação delta (Mbps)
* Largura de banda de rede observada entre o host primário e o host de recuperação (Mbps)
* Sugestão para o número ideal de transferências paralelas ativas entre os dois hosts/clusters

### <a name="on-premises-to-azure-replication"></a>Replicação de local para o Azure

* Impacto da replicação na computação do host primário, memória
* Impacto da replicação no espaço do disco de armazenamento do host primário, IOPS
* Largura de banda total necessária para a replicação delta (Mbps)

## <a name="more-resources"></a>Mais recursos
* Para obter informações detalhadas sobre a ferramenta, leia o documento que acompanha o download da ferramenta.
* Assista a um passo a passo da ferramenta no [Blog TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx)de Keith Mayer.
* [Obtenha os resultados](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) de nosso teste de desempenho para replicação Hyper-V do local para o local

## <a name="next-steps"></a>Próximas etapas

Depois de concluir o planejamento de capacidade, você pode iniciar a implantação do Site Recovery:

* [Replicar VMs Hyper-V em nuvens VMM para o Azure](site-recovery-vmm-to-azure.md)
* [Replicar VMs do Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicar VMs do Hyper-V entre sites do VMM](site-recovery-vmm-to-vmm.md)
