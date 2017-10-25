---
title: Configurar um cluster de RDMA do Linux para executar aplicativos MPI | Microsoft Docs
description: Criar um cluster do Linux de tamanho H16r, H16mr, A8 ou A9 para usar a rede RDMA do Azure e executar aplicativos MPI
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: 4b2ceb64b1737918458f6d5c692fc2bfbc0f12ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Configurar um cluster de RDMA do Linux para executar aplicativos MPI
Saiba como configurar um cluster RDMA do Linux no Azure com [tamanhos de VM de computação de alto desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para executar aplicativos MPI (Interface de Transmissão de Mensagens) paralelos. Este artigo fornece etapas para preparar uma imagem do HPC Linux para executar o Intel MPI em um cluster. Após a preparação, você implanta um cluster de VMs usando essa imagem e um dos tamanhos de VM do Azure compatíveis com RDMA (atualmente H16r, H16mr, A8 ou A9). Use o cluster para executar aplicativos MPI que se comunicam com eficiência por uma rede de alta taxa de transferência e baixa latência baseada na tecnologia RDMA (acesso remoto direto à memória).

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager](../../../resource-manager-deployment-model.md) e clássico. Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

## <a name="cluster-deployment-options"></a>Opções de implantação de cluster
Estes são métodos que você pode usar para criar um cluster de RDMA do Linux com ou sem um agendador de trabalhos.

* **Scripts de CLI do Azure**: conforme mostrado mais adiante neste artigo, use a CLI [(Interface de linha de comando) do Azure](../../../cli-install-nodejs.md) para o script de implantação de um cluster de VMs compatíveis com RDMA. A CLI no modo Gerenciamento de Serviços cria os nós do cluster em série no modelo de implantação clássico, então implantar muitos nós de computação pode demorar vários minutos. Para habilitar a conexão de rede RDMA ao usar o modelo de implantação clássico, implante as VMs no mesmo serviço de nuvem.
* **Modelos do Azure Resource Manager**: você também pode usar o modelo de implantação do Resource Manager para implantar um cluster de VMs compatíveis com RDMA que se conecte à rede RDMA. É possível [criar seu próprio modelo](../../../resource-group-authoring-templates.md) ou verificar os [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) para obter modelos com os quais a Microsoft ou a comunidade contribuíram para implantar a solução desejada. Os modelos do Gerenciador de Recursos podem fornecer uma maneira rápida e confiável para implantar um cluster do Linux. Para habilitar a conexão de rede RDMA ao usar o modelo de implantação do Resource Manager, implante as VMs no mesmo conjunto de disponibilidade.
* **HPC Pack**: crie um cluster Microsoft HPC Pack no Azure e adicione nós de computação compatíveis com RDMA que executem uma distribuição Linux com suporte para acesso à rede RDMA. Para saber mais, confira [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Etapas da implantação de exemplo no modelo clássico
As etapas a seguir ajudarão você a usar a CLI do Azure para implantar uma VM do HPC do SLES (SUSE Linux Enterprise Server) 12 SP1 do Azure Marketplace, personalizá-la e criar uma imagem de máquina virtual personalizada. Em seguida, você pode usar a imagem para o script de implantação de um cluster de VMs compatíveis com RDMA.

> [!TIP]
> Use etapas semelhantes para implantar um cluster de VMs compatíveis com RDMA baseado em imagens do HPC baseadas em CentOS no Azure Marketplace. Algumas etapas são ligeiramente diferentes, conforme observado. 
>
>

### <a name="prerequisites"></a>Pré-requisitos
* **Computador cliente**: você precisa de um computador cliente Windows, Linux ou Mac para se comunicar com o Azure. Essas etapas pressupõem que você esteja usando um cliente Linux.
* **Assinatura do Azure**: se não tiver uma assinatura, você poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Para clusters maiores, considere uma assinatura pré-paga ou outras opções de compra.
* **Disponibilidade de tamanho da VM**: os seguintes tamanhos de instância são compatíveis com RDMA: H16r, H16mr, A8 e A9. Confira [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/) para ver a disponibilidade nas regiões do Azure.
* **Cota de núcleos**: talvez seja necessário aumentar a cota de núcleos para implantar um cluster de VMs que consome muita computação. Por exemplo, você precisa de pelo menos 128 núcleos para implantar 8 VMs A9, conforme mostrado neste artigo. Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho de VM, incluindo a série de H. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../../../azure-supportability/how-to-create-azure-support-request.md) gratuitamente.
* **CLI do Azure**: [instale](../../../cli-install-nodejs.md) a CLI do Azure e [conecte-se à sua assinatura do Azure](../../../xplat-cli-connect.md) no computador cliente.

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>Provisionar uma VM do HPC para SLES 12 SP1
Depois de entrar no Azure com a CLI do Azure, execute `azure config list` para confirmar que a saída mostra o modo Gerenciamento de Serviços do Azure. Se não mostrar, defina o modo executando este comando:

    azure config mode asm


Digite o seguinte para listar todas as assinaturas que você está autorizado a usar:

    azure account list

A assinatura ativa atual é identificada com `Current` definido como `true`. Se esta não for a assinatura que você quer usar para criar o cluster, defina a ID de assinatura apropriada como a assinatura ativa:

    azure account set <subscription-Id>

Para ver as imagens do HPC para SLES 12 SP1 publicamente disponíveis no Azure, execute um comando semelhante ao seguinte, supondo que o ambiente de shell dê suporte a **grep**:

    azure vm image list | grep "suse.*hpc"

Provisione uma VM compatível com RDMA com uma imagem do HPC para SLES 12 SP1 executando um comando semelhante ao seguinte:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

Em que:

* O tamanho (A9 neste exemplo) é um dos tamanhos de VM compatíveis com RDMA.
* o número da porta SSH externa (22 neste exemplo, que é o padrão SSH) é qualquer número de porta válido. O número da porta de SSH interna é definido como 22.
* Um novo serviço de nuvem é criado na região do Azure especificada pelo local. Especifique uma localização em que o tamanho de VM escolhido está disponível.
* Para suporte à prioridade SUSE (que incorre em cobranças adicionais), o nome da imagem do SLES 12 SP1, no momento, pode ser uma destas duas opções: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>Personalizar a VM
Depois que a VM concluir o provisionamento, faça SSH nela usando o endereço IP externo da VM (ou o nome DNS) e o número da porta externa configurada; em seguida, personalize-a. Para obter detalhes de conexão, confira [Como fazer logon em uma máquina virtual executando o Linux](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Execute comandos como o usuário configurado na VM, a menos que o acesso à raiz seja necessário para concluir uma etapa.

> [!IMPORTANT]
> O Microsoft Azure não oferece acesso à raiz para VMs do Linux. Para obter acesso administrativo quando estiver conectado como um usuário à VM, execute comandos usando `sudo`.
>
>

* **Atualizações**: instale atualizações usando o zypper. Talvez você também queira instalar utilitários NFS.

  > [!IMPORTANT]
  > Na VM do HPC do SLES 12 SP1, é recomendável que você não aplique as atualizações de kernel, que podem causar problemas com os drivers de RDMA do Linux.
  >
  >
* **Intel MPI**: conclua a instalação do Intel MPI na VM do HPC para SLES 12 SP1 executando o seguinte comando:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **Bloquear memória**: para os códigos do MPI bloquearem a memória disponível para RDMA, adicione ou altere as configurações a seguir no arquivo /etc/security/limits.conf. Você precisa de acesso à raiz para editar esse arquivo.

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > Para fins de teste, também é possível definir memlock como ilimitado. Por exemplo: `<User or group name>    hard    memlock unlimited`. Para saber mais, confira [Best known methods for setting locked memory size](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size) (Métodos mais conhecidos para definir o tamanho da memória bloqueada).
  >
  >
* **Chaves SSH para VMs SLES**: gere chaves SSH para estabelecer confiança para sua conta de usuário entre os nós de computação no cluster do SLES ao executar trabalhos MPI. Se você tiver implantado uma VM do HPC baseado em CentOS, não execute esta etapa. Veja as instruções mais adiante neste artigo para configurar a confiança de SSH sem senha entre os nós de cluster depois de capturar a imagem e implantar o cluster.

    Para criar chaves SSH, execute o comando a seguir. Quando for solicitado, selecione **Enter** para gerar as chaves no local padrão, sem definir uma senha.

        ssh-keygen

    Acrescente a chave pública ao arquivo authorized_keys para chaves públicas conhecidas.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    No diretório ~/.ssh, edite ou crie o arquivo config. Forneça o intervalo de endereços IP da rede privada que você pretende usar no Azure (neste exemplo, 10.32.0.0/16):

        host 10.32.0.*
        StrictHostKeyChecking no

    Como alternativa, liste o endereço IP da rede privada de cada VM no cluster da seguinte maneira:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > A configuração de `StrictHostKeyChecking no` pode criar um risco de segurança potencial quando um determinado endereço IP ou intervalo não for especificado.
  >
  >
* **Aplicativos**: instale todos os aplicativos necessários ou execute outras personalizações antes de capturar a imagem.

### <a name="capture-the-image"></a>Capturar a imagem
Para capturar a imagem, primeiramente execute o comando a seguir na VM do Linux. Esse comando desprovisiona a VM, mas mantém as contas de usuário e chaves SSH que você configurou.

```
sudo waagent -deprovision
```

No computador cliente, execute os comandos da CLI do Azure a seguir para capturar a imagem. Para saber mais, confira [Como capturar uma máquina virtual clássica do Linux como uma imagem](capture-image.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Depois de executar esses comandos, a imagem da VM é capturada para seu uso e a VM é excluída. Agora você tem a imagem personalizada pronta para implantar um cluster.

### <a name="deploy-a-cluster-with-the-image"></a>Implantar um cluster com a imagem
Modifique o script Bash a seguir com os valores apropriados para seu ambiente e execute-o no computador cliente. Como o Azure implanta as VMs serialmente no modelo de implantação clássico, a implantação das oito VMs A9 sugeridas neste script demora um pouco.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Considerações para um cluster de HPC do CentOS
Se você quiser configurar um cluster com base em uma das imagens do HPC baseado em CentOS no Azure Marketplace, em vez do SLES 12 para HPC, siga as etapas gerais na seção anterior. Quando você provisiona e configura a VM, observe as seguintes diferenças:

- O Intel MPI já está instalado em uma VM provisionada de uma imagem do HPC baseado em CentOS.
- As configurações de memória de bloqueio já foram adicionadas no arquivo /etc/security/limits.conf da VM.
- Não gere chaves SSH na VM provisionada para captura. Em vez disso, é recomendável configurar a autenticação baseada em usuário depois de implantar o cluster. Para saber mais, veja a seção a seguir.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Configurar relação de confiança SSH sem senha no cluster
Em um cluster do HPC baseado em CentOS, há dois métodos para estabelecer a relação de confiança entre os nós de computação: autenticação baseada em host e autenticação baseada no usuário. A autenticação baseada em host está fora do escopo deste artigo e geralmente deve ser feita por meio de um script de extensão durante a implantação. A autenticação baseada em usuário é conveniente para estabelecer a relação de confiança após a implantação e requer a geração e o compartilhamento de chaves SSH entre os nós de computação no cluster. Esse método é conhecido como logon SSH sem senha e é necessário na execução de trabalhos MPI.

Um exemplo de script da comunidade está disponível no [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) para facilitar a autenticação do usuário em um cluster HPC baseado em CentOS. Baixe e use este script usando as etapas a seguir. Você também pode modificar esse script ou usar qualquer outro método para estabelecer a autenticação SSH sem senha entre os nós de computação do cluster.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh

Para executar o script, você precisa saber o prefixo para seus endereços IP de sub-rede. Obtenha o prefixo executando o comando a seguir em um dos nós do cluster. A saída deverá ser semelhante a 10.1.3.5, e o prefixo é a parte 10.1.3.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Agora execute o script usando três parâmetros: o nome de usuário comum nos nós de computação, a senha comum desse usuário nos nós de computação e o prefixo de sub-rede que foi retornado pelo comando anterior.

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Esse script faz o seguinte:

* Cria um diretório no nó do host chamado .ssh, que é necessário para o logon sem senha.
* Cria um arquivo de configuração no diretório .ssh, que instrui o logon sem senha a permitir o logon de qualquer nó no cluster.
* Cria arquivos com os nomes de nó e os endereços IP de nó para todos os nós no cluster. Esses arquivos são deixados após a execução do script para posterior referência.
* Cria um par de chaves pública e privada para cada nó de cluster (incluindo o nó de host), bem como entradas no arquivo authorized_keys.

> [!WARNING]
> A execução desse script pode criar um potencial risco de segurança. Certifique-se de que as informações da chave pública em ~/.ssh não sejam distribuídas.
>
>

## <a name="configure-intel-mpi"></a>Configurar o Intel MPI
Para executar aplicativos MPI em RDMA do Linux no Azure, é preciso configurar determinadas variáveis de ambiente específicas para o Intel MPI. Veja um script Bash de exemplo para configurar as variáveis necessárias para executar um aplicativo. Altere o caminho para mpivars.sh como necessário para a instalação da MPI Intel.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

O formato do arquivo de host é o seguinte. Adicione uma linha para cada nó no cluster. Especifique os endereços IP privados da rede virtual definida anteriormente, não os nomes DNS. Por exemplo, para dois hosts com endereços IP 10.32.0.1 e 10.32.0.2, o arquivo contém o seguinte:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Executar MPI em um cluster de dois nós básico
Se ainda não tiver feito isso, primeiramente configure o ambiente para o Intel MPI.

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Executar um comando MPI
Execute um comando MPI em um dos nós de computação para mostrar que o MPI está instalado corretamente e pode se comunicar entre pelo menos dois nós de computação. O comando **mpirun** a seguir executa o comando **hostname** em dois nós.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
A saída deve listar os nomes de todos os nós passados como entrada para `-hosts`. Por exemplo, um comando **mpirun** com dois nós retorna uma saída semelhante à seguinte:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Executar um parâmetro de comparação de MPI
O comando Intel MPI a seguir executa um parâmetro de comparação de pingpong para verificar a configuração do cluster e a conexão com a rede RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Em um cluster ativo com dois nós, você verá uma saída semelhante ao que se segue. Na rede RDMA do Azure, espere latência em três microssegundos ou menos para até 512 bytes de tamanhos de mensagem.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Próximas etapas
* Implantar e executar os aplicativos MPI do Linux no cluster do Linux.
* Consulte a [Documentação do Intel MPI Library](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) para obter diretrizes sobre o Intel MPI.
* Experimente um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) para criar um cluster Intel Lustre usando uma imagem HPC baseado em CentOS. Para obter detalhes, confira [Deploying Intel Cloud Edition for Lustre on Microsoft Azure](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/) (Implantando o Intel Cloud Edition for Lustre no Microsoft Azure).
