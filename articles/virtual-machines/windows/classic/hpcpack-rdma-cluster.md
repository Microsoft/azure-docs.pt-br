---
title: Configurar um cluster de RDMA do Windows para executar aplicativos MPI | Microsoft Docs
description: Saiba como criar um cluster do Windows HPC Pack com VMs de tamanho H16r, H16mr, A8 ou A9 para usar a rede RDMA do Azure e executar aplicativos MPI.
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 19be1d693fe13af0f6c1ab0cb6f7bc829b9fad5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI
Configure um cluster do Windows RDMA no Azure com [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) e [Tamanhos de VM de computação de alto desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para executar aplicativos MPI (Message Passing Interface). Quando você configura nós compatíveis com RDMA baseados no Windows Server em um cluster de Pacote HPC, os aplicativos MPI se comunicam de modo eficiente por uma rede de baixa latência e alta taxa de transferência baseada na tecnologia RDMA (acesso remoto direto à memória).

Se quiser executar cargas de trabalho MPI em VMs Linux que acessam a rede RDMA do Azure, consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](../../linux/classic/rdma-cluster.md).

## <a name="hpc-pack-cluster-deployment-options"></a>Opções de implantação do cluster do Pacote HPC
O Microsoft HPC Pack é uma ferramenta fornecida sem custo adicional para criar clusters HPC locais ou no Azure para executar aplicativos HPC do Windows ou Linux. O HPC Pack inclui um ambiente de tempo de execução para a implementação por parte da Microsoft da MS-MPI (Interface de Transmissão de Mensagem para Windows). Quando usado com instâncias compatíveis com RDMA executando um sistema operacional do Windows Server com suporte, o HPC Pack oferece uma opção eficiente para executar aplicativos MPI do Windows que acessam a rede RDMA do Azure. 

Este artigo apresenta dois cenários e links para diretrizes detalhadas de como configurar um cluster RDMA do Windows com o Microsoft HPC Pack. 

* Cenário 1. Implantar instâncias de função de trabalho de computação intensiva (PaaS)
* Cenário 2: Implantar nós de computação em VMs de computação intensiva (IaaS)

Para pré-requisitos gerais para usar instâncias com uso intenso de computação com o Windows, consulte [Tamanhos de VM de computação de alto desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Cenário 1: Implantar instâncias de função de trabalho de computação intensiva (PaaS)
Em um cluster existente do HPC Pack, adicione recursos de computação extras nas instâncias de função de trabalho do Azure (nós do Azure) em execução em um serviço de nuvem (PaaS). Esse recurso, também chamado de "disparo no Azure" do HPC Pack, dá suporte a uma variedade de tamanhos para as instâncias de função de trabalho. Ao adicionar os nós do Azure, especifique um dos tamanhos compatíveis com RDMA.

Veja a seguir as considerações e etapas para disparar as instâncias compatíveis com RDMA do Azure de um cluster existente (geralmente locais). Use procedimentos semelhantes para adicionar instâncias de função de trabalho a um nó de cabeçalho do HPC Pack que é implantado em uma VM do Azure.

> [!NOTE]
> Para ver um tutorial de como disparar no Azure com o HPC Pack, consulte [Configurar um cluster híbrido com o HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Observe que as considerações nas etapas que se seguem se aplicam especificamente aos nós do Azure compatíveis com RDMA.
> 
> 

![Disparo no Azure][burst]

### <a name="steps"></a>Etapas
1. **Implantar e configurar um nó de cabeçalho do HPC Pack 2012 R2**
   
    Baixe o pacote de instalação mais recente do HPC Pack do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Para ver requisitos e instruções de como se preparar para uma implantação de disparo do Azure, confira [Burst to Azure Worker Instances with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx) (Aumente a capacidade das instâncias de trabalho do Azure com o Microsoft HPC Pack).
2. **Configurar um certificado de gerenciamento na assinatura do Azure**
   
    Configure um certificado para proteger a conexão entre o nó de cabeçalho e o Azure. Para ver opções e procedimentos, consulte [Cenários para configurar o certificado de gerenciamento do Azure para HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Para implantações de teste, o Pacote HPC instala um Certificado Padrão de Gerenciamento do Microsoft HPC Azure que pode ser carregado rapidamente na sua assinatura do Azure.
3. **Criar um novo serviço de nuvem e uma conta de armazenamento**
   
    Use o portal do Azure para criar um serviço de nuvem e uma conta de armazenamento para a implantação em uma região em que as instâncias compatíveis com RDMA estão disponíveis.
4. **Criar um modelo de nó do Azure**
   
    Use o Assistente para Criar Modelo de Nó no Gerenciador de Cluster do HPC. Para ver as etapas, consulte [Criar um modelo de nó do Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) em "Etapas para implantar nós do Azure com o Microsoft HPC Pack".
   
    Para fazer testes iniciais, recomendamos configurar uma política de disponibilidade manual no modelo.
5. **Adicionar nós ao cluster**
   
    Use o Assistente para Adicionar Nós no Gerenciador de Cluster do HPC. Para saber mais, consulte [Adicionar nós do Azure ao cluster do Windows HPC](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    Ao especificar o tamanho dos nós, selecione um dos tamanhos de instância compatíveis com RDMA.
   
   > [!NOTE]
   > Em cada disparo na implantação do Azure com as instâncias de computação intensiva, o HPC Pack implanta automaticamente um mínimo de duas instâncias compatíveis com RDMA (por exemplo, A8) como nós de proxy, além das instâncias de função de trabalho do Azure que você especifica. Os nós de proxy usam núcleos que são alocados para a assinatura e incorrem em cobranças juntamente com as instâncias de função de trabalho do Azure.
   > 
   > 
6. **Iniciar (provisionar) os nós e ativá-los para executar trabalhos**
   
    Selecione os nós e use a ação **Iniciar** no Gerenciador de Cluster do HPC. Quando o provisionamento estiver concluído, selecione os nós e use a ação **Colocar Online** no Gerenciador de Cluster do HPC. Os nós estão prontos para executar trabalhos.
7. **Enviar trabalhos para o cluster**
   
   Use ferramentas de envio de trabalho do HPC Pack para executar trabalhos de cluster. Consulte [Microsoft HPC Pack: gerenciamento de trabalhos](http://technet.microsoft.com/library/jj899585.aspx).
8. **Parar (desprovisionar) os nós**
   
   Quando você terminar de executar trabalhos, coloque os nós offline e use a ação **Parar** no Gerenciador de Cluster do HPC.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Cenário 2: Implantar nós de computação em VMs de computação intensiva (IaaS)
Neste cenário, você implanta o nó de cabeçalho do HPC Pack e os nós de computação de cluster em VMs em uma rede virtual do Azure. O HPC Pack fornece várias [opções de implantação em VMs do Azure](../../linux/hpcpack-cluster-options.md), incluindo scripts de implantação automatizada e modelos de início rápido do Azure. Como exemplo, as considerações e etapas a seguir mostrarão a você como usar o [script de implantação IaaS do HPC Pack](hpcpack-cluster-powershell-script.md) para automatizar a implantação de um cluster HPC Pack 2012 R2 no Azure.

![Cluster em VMs do Azure][iaas]

### <a name="steps"></a>Etapas
1. **Criar um nó de cabeçalho de cluster e VM de nó de computação executando o script de implantação de IaaS do HPC Pack em um computador cliente**
   
    Baixe o pacote Script de Implantação de IaaS do HPC do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).
   
    Para preparar o computador cliente, criar o arquivo de configuração de script e executar o script, consulte [Criar um cluster de HPC com o script de implantação de IaaS do HPC Pack](hpcpack-cluster-powershell-script.md). 
   
    Para implantar os nós de computação compatíveis com RDMA, veja as considerações adicionais a seguir:
   
   * **Rede virtual**: especifique uma nova rede virtual em uma região na qual o tamanho da instância compatível com RDMA que você deseja usar esteja disponível.
   * **Sistema operacional Windows Server**: para dar suporte à conectividade RDMA, especifique um sistema operacional Windows Server 2012 R2 ou Windows Server 2012 para as VMs do nó de computação.
   * **Serviços de nuvem**: é recomendável implantar o nó de cabeçalho em um serviço de nuvem e seus nós de computação em outro serviço de nuvem.
   * **Tamanho do nó de cabeçalho**: para esse cenário, considere, no mínimo, um tamanho A4 (extra grande) para o nó de cabeçalho.
   * **Extensão HpcVmDrivers**: o script de implantação instala o agente de VM do Azure e a extensão HpcVmDrivers automaticamente quando você implanta os nós de computação A8 ou A9 com um sistema operacional Windows Server. A extensão HpcVmDrivers instala drivers nas VMs do nó de computação para que elas possam se conectar à rede RDMA. Em VMs da série H compatíveis com RDMA, você deve instalar a extensão HpcVmDrivers manualmente. Consulte [Tamanhos de VM de computação de alto desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Configuração de rede de cluster**: o script de implantação configura automaticamente o cluster HPC Pack na Topologia 5 (todos os nós na rede corporativa). Essa topologia é necessária para todas as implantações de cluster de HPC Pack em VMs. Não altere a topologia de rede do cluster posteriormente.
2. **Colocar os nós de computação online para executar trabalhos**
   
    Selecione os nós e use a ação **Colocar Online** no Gerenciador de Cluster do HPC. Os nós estão prontos para executar trabalhos.
3. **Enviar trabalhos para o cluster**
   
    Conecte-se ao nó de cabeçalho para enviar trabalhos ou configure um computador local para fazer isso. Para saber mais, consulte [Enviar trabalhos para um cluster do HPC no Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Colocar os nós offline e pará-los/desalocá-los**
   
    Quando você terminar a execução de trabalhos, coloque os nós offline no Gerenciador de Cluster do HPC. Em seguida, use as ferramentas de gerenciamento do Azure para desativá-los.

## <a name="run-mpi-applications-on-the-cluster"></a>Executar aplicativos MPI no cluster
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Exemplo: executar mpipingpong em um cluster do HPC Pack
Para verificar uma implantação do HPC Pack das instâncias compatíveis com RDMA, execute o comando **mpipingpong** do HPC Pack no cluster. **mpipingpong** envia pacotes de dados entre nós emparelhados repetidamente para calcular medições de latência e taxa de transferência, bem como estatísticas para a rede de aplicativo compatível com RDMA. Este exemplo mostra um padrão típico para executar um trabalho MPI (nesse caso, **mpipingpong**) usando o comando **mpiexec** do cluster.

Este exemplo pressupõe que você tenha adicionado nós do Azure em uma configuração "disparo para o Azure" ([cenário 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Se você implantou o HPC Pack em um cluster de VMs do Azure, será preciso modificar a sintaxe de comando a fim de especificar um grupo de nós diferente e definir variáveis de ambiente adicionais de modo a direcionar o tráfego de rede para a rede RDMA.

Para executar mpipingpong no cluster:

1. No nó de cabeçalho ou em um computador cliente configurado corretamente, abra um prompt de comando.
2. Para estimar a latência entre pares de nós em uma implantação de disparo do Azure de quatro nós, digite o seguinte comando a fim de enviar um trabalho para executar mpipingpong com um pacote pequeno e muitas iterações:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    O comando retorna a ID do trabalho enviado.
   
    Se você tiver implantado o cluster do HPC Pack implantado em VMs do Azure, especifique um grupo de nós que contenha VMs do nó de computação implantadas em um único serviço de nuvem e modifique o comando **mpiexec** como se segue:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Quando o trabalho for concluído, para exibir a saída (nesse caso, a saída da tarefa 1 do trabalho), digite o seguinte
   
    ```Command
    task view <JobID>.1
    ```
   
    em que &lt;*JobID*&gt; é a ID do trabalho enviado.
   
    A saída inclui resultados de latência semelhantes aos que se seguem.
   
    ![Latência ping pong][pingpong1]
4. Para estimar a taxa de transferência entre pares dos nós de disparo do Azure, digite o seguinte comando a fim de enviar um trabalho para execução de **mpipingpong** com um pacote grande e algumas iterações:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    O comando retorna a ID do trabalho enviado.
   
    Em um cluster do HPC Pack implantado em VMs do Azure, modifique o comando, como observado na etapa 2.
5. Quando o trabalho for concluído, para exibir a saída (nesse caso, a saída da tarefa 1 do trabalho), digite o seguinte:
   
    ```Command
    task view <JobID>.1
    ```
   
   A saída inclui resultados de taxa de transferência semelhantes aos que se seguem.
   
   ![Taxa de transferência de ping pong][pingpong2]

### <a name="mpi-application-considerations"></a>Considerações de aplicativos MPI
A seguir, as considerações para execução de aplicativos MPI com HPC Pack no Azure. Algumas se aplicam somente à implantações de nós do Azure (instâncias de função de trabalho adicionadas em uma configuração de "disparo no Azure").

* As instâncias de função de trabalho em um serviço de nuvem são reprovisionadas periodicamente sem aviso pelo Azure (por exemplo, para manutenção do sistema ou no caso de uma instância falhar). Se uma instância for reprovisionada enquanto estiver executando um trabalho MPI, ela perderá os seus dados e retornará para o estado em que estava quando foi implantada pela primeira vez, o que poderá fazer com que o trabalho MPI falhe. Quanto mais nós forem usados para um único trabalho MPI e quanto mais longa for a execução do trabalho, maior a probabilidade de que uma das instâncias seja reprovisionada enquanto um trabalho estiver em execução. Leve isso também em consideração se designar um único nó na implantação como um servidor de arquivos.
* Não é preciso usar as instâncias compatíveis com RDMA para executar trabalhos MPI no Azure. Você pode usar qualquer tamanho de instância que tenha o suporte do HPC Pack. No entanto, as instâncias compatíveis com RDMA são recomendadas para executar trabalhos MPI de escala relativamente grande que sejam sensíveis à latência e à largura de banda da rede que conecta os nós. Se você usa instâncias de outros tamanhos para executar trabalhos MPI sensíveis à latência e à largura de banda, é recomendável executar trabalhos pequenos, nos quais uma única tarefa é executada em apenas alguns nós.
* Os aplicativos implantados em instâncias do Azure estão sujeitos aos termos de licenciamento associados ao aplicativo. Verifique com o fornecedor de qualquer aplicativo comercial quanto ao licenciamento ou outras restrições de execução na nuvem. Nem todos os fornecedores oferecem licenciamento pré-pago.
* As instâncias do Azure precisam de outras configurações para acessar os nós, os compartilhamentos e os servidores de licenças locais. Por exemplo, para permitir que os nós do Azure acessem um servidor de licenças local, você pode configurar uma rede virtual site a site do Azure.
* Para executar aplicativos MPI em instâncias do Azure, registre cada aplicativo MPI no Firewall do Windows nas instâncias executando o comando **hpcfwutil** . Isso permite que as comunicações MPI ocorram em uma porta atribuída dinamicamente pelo firewall.
  
  > [!NOTE]
  > Para implantações de disparo no Azure, você também pode configurar um comando de exceção de firewall para ser executado automaticamente em todos os nós novos do Azure que são adicionados ao cluster. Depois de executar o comando **hpcfwutil** e verificar se seu aplicativo funciona, adicione o comando ao script de inicialização dos seus nós do Azure. Para obter mais informações, consulte [Usar um script de inicialização para nós do Azure](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* O HPC Pack usa a variável de ambiente do cluster CCP_MPI_NETMASK para especificar um intervalo de endereços aceitáveis para comunicação MPI. A partir do HPC Pack 2012 R2, a variável de ambiente do cluster CCP_MPI_NETMASK afeta apenas a comunicação MPI entre nós de computação de cluster que ingressaram no domínio (no local ou em VMs do Azure). A variável é ignorada pelos nós adicionados em uma configuração de disparo no Azure.
* Os trabalhos MPI não podem ser executados nas instâncias do Azure que estiverem implantadas em serviços de nuvem diferentes (por exemplo, em implantações de disparo no Azure com diferentes modelos de nó ou em nós de computação da VM do Azure implantados em vários serviços de nuvem). Se você tiver várias implantações de nó do Azure que são iniciadas com modelos de nó diferentes, o trabalho MPI deverá ser executado apenas em um conjunto de nós do Azure.
* Quando você adicionar nós do Azure ao cluster e colocá-los online, o Serviço do Agendador de Trabalho do HPC tentará imediatamente iniciar trabalhos nos nós. Se apenas uma parte da carga de trabalho puder ser executada no Azure, não se esqueça de atualizar ou criar modelos de trabalho para definir quais tipos de trabalho podem ser executados no Azure. Por exemplo, para garantir que trabalhos enviados com um modelo de trabalho sejam executados somente em nós do Azure, adicione a propriedade Grupos de Nós ao modelo de trabalho e selecione AzureNodes como o valor necessário. Para criar grupos personalizados para seus nós do Azure, use o cmdlet Add-HpcGroup HPC PowerShell.

## <a name="next-steps"></a>Próximas etapas
* Como alternativa ao uso do HPC Pack, desenvolva com o serviço de lote do Azure para executar aplicativos MPI em pools gerenciados de nós de computação no Azure. Veja [Usar tarefas de várias instâncias para executar aplicativos de MPI (Interface de Transmissão de Mensagens) no Lote do Azure](../../../batch/batch-mpi.md).
* Se quiser executar aplicativos MPI do Linux que acessam a rede RDMA do Azure, consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](../../linux/classic/rdma-cluster.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
