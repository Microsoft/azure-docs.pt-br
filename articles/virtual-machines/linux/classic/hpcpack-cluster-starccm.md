---
title: Executar o STAR-CCM+ com o HPC Pack em VMs do Linux | Microsoft Docs
description: "Implante um cluster do Microsoft HPC Pack no Azure e execute um trabalho do STAR-CCM+ em vários nós de computação do Linux em uma rede RDMA."
services: virtual-machines-linux
documentationcenter: 
author: xpillons
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 75523406-d268-4623-ac3e-811c7b74de4b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/13/2016
ms.author: xpillons
ms.openlocfilehash: b45fcfb981287035da02fda62eaf5f9436ec2379
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Executar o STAR-CCM+ com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure
Este artigo mostra como implantar um cluster do Microsoft HPC Pack no Azure e executar um trabalho do [STAR-CCM+ da CD-adapco](http://www.cd-adapco.com/products/star-ccm%C2%AE) em vários nós de computação Linux interconectados com InfiniBand.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

O Microsoft HPC Pack fornece recursos para executar uma variedade de aplicativos de HPC e paralelos em larga escala, incluindo aplicativos MPI, em clusters de máquinas virtuais do Microsoft Azure. O HPC Pack também dá suporte à execução de aplicativos de HPC do Linux em VMs com nós de computação do Linux implantadas em um cluster do HPC Pack. Para saber como começar a usar os nós de computação do Linux com o HPC Pack, consulte a [Introdução a nós de computação Linux em um cluster HPC Pack no Azure](hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Configurar um cluster do HPC Pack
Baixe os scripts de implantação de IaaS do HPC Pack do [Centro de Download](https://www.microsoft.com/en-us/download/details.aspx?id=44949) e extraia-os localmente.

O Azure PowerShell é um pré-requisito. Se o PowerShell não estiver configurado no computador local, leia [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

No momento da redação deste artigo, as imagens do Linux do Azure Marketplace (que contém os drivers de InfiniBand para o Azure) são para o SLES 12, o CentOS 6.5 e o CentOS 7.1. Este artigo baseia-se no uso do SLES 12. Para recuperar o nome de todas as imagens do Linux que dão suporte ao HPC no Marketplace, você pode executar o seguinte comando do PowerShell:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

A saída lista o local em que essas imagens estão disponíveis e o nome da imagem (**ImageName**) a ser usado no modelo de implantação posteriormente.

Antes de implantar o cluster, você precisa criar um arquivo de modelo de implantação do HPC Pack. Como nosso alvo é um cluster pequeno, o nó principal será o controlador de domínio e hospedará um banco de dados SQL local.

O modelo a seguir implantará esse nó de cabeçalho, criará um arquivo XML chamado **MyCluster.xml** e substituirá os valores de **SubscriptionId**, **StorageAccount**, **Location**, **VMName** e **ServiceName** pelos seus.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Inicie a criação do nó principal executando o comando do PowerShell em um prompt de comandos com privilégios elevados:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Após 20 a 30 minutos, o nó principal deverá estar pronto. Você pode se conectar a ele do portal do Azure clicando no ícone **Conectar** da máquina virtual.

Eventualmente, talvez você precise consertar o encaminhador DNS. Para fazer isso, inicie o Gerenciador DNS.

1. Clique com o botão direito do mouse no nome do servidor no Gerenciador DNS, selecione **Propriedades** e clique na guia **Encaminhadores**.
2. Clique no botão **Editar** para remover quaisquer encaminhadores e clique em **OK**.
3. Certifique-se de que a caixa de seleção **Usar dicas de raiz se encaminhadores não estiverem disponíveis** esteja marcada e clique em **OK**.

## <a name="set-up-linux-compute-nodes"></a>Configurar nós de computação do Linux
Você implanta os nós de computação do Linux usando o mesmo modelo de implantação que usou para criar o nó principal.

Copie o arquivo **MyCluster.xml** do computador local para o nó de cabeçalho e atualize a marcação **NodeCount** com o número de nós que você deseja implantar (<=20). Tenha cuidado para ter núcleos suficientes disponíveis na sua cota do Azure, porque cada instância A9 consumirá 16 núcleos de sua assinatura. Você poderá usar instâncias A8 (8 núcleos) em vez de A9 se quiser usar mais VMs no mesmo orçamento.

No nó principal, copie os scripts de implantação de IaaS do HPC Pack.

Execute os seguintes comandos do Azure PowerShell em um prompt de comandos com privilégios elevados:

1. Execute **Add-AzureAccount** para se conectar à sua assinatura do Azure.
2. Se tiver várias assinaturas, execute **Get-AzureSubscription** para listá-las.
3. Defina uma assinatura padrão executando o comando **Select-AzureSubscription -SubscriptionName xxxx -Default** .
4. Execute **.\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml** para iniciar a implantação dos nós de computação do Linux.
   
   ![Implantação do nó principal em ação][hndeploy]

Abra a ferramenta de Gerenciador de Cluster do HPC Pack. Após alguns minutos, os nós de computação do Linux aparecerão regularmente na lista de nós de computação do cluster. Com o modo de implantação clássico, as VMs de IaaS são criadas em sequência. Sendo assim, se o número de nós for importante, fazer com que todas elas sejam implantadas poderá levar muito tempo.

![Nós do Linux no Gerenciador de Cluster do HPC Pack][clustermanager]

Agora que todos os nós estão em funcionamento, há configurações de infraestrutura adicionais a serem definidas.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Configurar um compartilhamento de arquivos do Azure para nós do Windows e Linux
Você pode usar o serviço de arquivos do Azure para armazenar scripts, pacotes de aplicativos e arquivos de dados. O arquivo do Azure fornece funcionalidades CIFS sobre o Armazenamento de Blobs do Azure como um repositório persistente. Lembre-se de que essa não é a solução mais escalonável, mas é a mais simples e não requer VMs dedicadas.

Crie um compartilhamento de arquivos do Azure seguindo as instruções no artigo [Introdução ao Armazenamento de Arquivos do Azure no Windows](../../../storage/files/storage-dotnet-how-to-use-files.md).

Mantenha o nome da sua conta de armazenamento como **saname**, o nome do compartilhamento de arquivos como **sharename** e a chave de conta de armazenamento **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Montar o compartilhamento de arquivos do Azure no nó principal
Abra um prompt de comandos com privilégios elevados e execute o seguinte comando para armazenar as credenciais no cofre do computador local:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Em seguida, para montar o compartilhamento de arquivos do Azure, execute:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Montar o compartilhamento de arquivos do Azure em nós de computação do Linux
Uma ferramenta útil que é fornecida com o HPC Pack é a ferramenta clusrun. Você pode usar essa ferramenta de linha de comando para executar o mesmo comando simultaneamente em um conjunto de nós de computação. Em nosso caso, ela é usada para montar o compartilhamento de arquivos do Azure e fazer com que ele persista e sobreviva a reinicializações.
Em um prompt de comandos com privilégios elevados no nó principal, execute os comandos a seguir.

Para criar o diretório de montagem:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Para montar o compartilhamento de arquivos do Azure:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Para fazer com que o compartilhamento de montagem persista:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Instalar o STAR-CCM+
As instâncias de VM do Azure A8 e A9 dão suporte ao InfiniBand e funcionalidades de RDMA. Os drivers de kernel que habilitam essas funcionalidades estão disponíveis para imagens do Windows Server 2012 R2, SUSE 12, CentOS 6.5 e CentOS 7.1 no Azure Marketplace. O Microsoft MPI e Intel MPI (versão 5. x) são as duas bibliotecas de MPI que dão suporte a esses drivers no Azure.

O STAR-CCM+ da CD-adapco versão 11.x e posterior é fornecido com o Intel MPI versão 5.x, portanto o suporte do InfiniBand para Azure está incluído.

Obter o pacote Linux64 do STAR-CCM+ no [portal do CD-adapco](https://steve.cd-adapco.com). Em nosso caso, usamos a versão 11.02.010 com precisão mista.

No nó de cabeçalho, no compartilhamento de arquivos do Azure **/hpcdata**, crie um script de shell chamado **setupstarccm.sh** com o conteúdo a seguir. Esse script será executado em cada nó de computação para configurar o STAR-CCM+ localmente.

#### <a name="sample-setupstarcmsh-script"></a>Script setupstarcm.sh de exemplo
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Agora, para instalar o STAR-CCM+ em todos os seus nós de computação do Linux, abra um prompt de comandos com privilégios elevados e execute o seguinte comando:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Enquanto o comando é executado, você pode monitorar o uso da CPU com o mapa de calor do Gerenciador de Cluster. Depois de alguns minutos, todos os nós devem estar instalados corretamente.

## <a name="run-star-ccm-jobs"></a>Executar trabalhos do STAR-CCM+
O HPC Pack é usado por suas funcionalidades de agendador de trabalho para executar trabalhos do STAR-CCM+. Para fazer isso, precisamos do suporte de alguns scripts que são usados para iniciar o trabalho e executar o STAR-CCM+. Os dados de entrada são mantidos no compartilhamento de arquivos do Azure primeiro por questões de simplicidade.

O script do PowerShell a seguir é usado para enfileirar um trabalho do STAR-CCM+. Ele usa três argumentos:

* O nome do modelo
* O número de nós a ser usado
* O número de núcleos em cada nó a ser usado

Como o STAR-CCM+ pode ocupar a largura de banda da memória, normalmente é melhor usar menos núcleos por nó de computação e adicionar novos nós. O número exato de núcleos por nó dependerá da família de processadores e da velocidade da interconexão.

Os nós são alocados exclusivamente para o trabalho e não podem ser compartilhados com outros trabalhos. O trabalho não será iniciado como um trabalho de MPI diretamente. O script de shell **runstarccm.sh** iniciará o iniciador de MPI.

O modelo de entrada e o script **runstarccm.sh** são armazenados no compartilhamento **/hpcdata** que foi montado anteriormente.

Os arquivos de log são nomeados com a ID do trabalho e são armazenados no **compartilhamento /hpcdata**, assim como os arquivos de saída do STAR-CCM+.

#### <a name="sample-submitstarccmjobps1-script"></a>Script SubmitStarccmJob.ps1 de exemplo
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job     
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Substitua **runner.java** por seu código de registro em log e código do iniciador de modelo Java do STAR-CCM+ preferidos.

#### <a name="sample-runstarccmsh-script"></a>Script runstarccm.sh de exemplo
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

Em nosso teste, usamos um token de licença do tipo Power-On-Demand. Para esse token, você precisa definir a variável de ambiente **$CDLMD_LICENSE_FILE** como **1999@flex.cd-adapco.com** e a chave na opção **-podkey** da linha de comando.

Após a inicialização, o script extrai – das variáveis de ambiente definidas pelo HPC Pack **$CCP_NODES_CORES** – a lista de nós para criar um arquivo de host usado pelo iniciador de MPI. Esse arquivo de host conterá a lista de nomes dos nós de computação que são usados para o trabalho, um nome por linha.

O formato **$CCP_NODES_CORES** segue este padrão:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Em que:

* `<Number of nodes>` é o número de nós alocados para esse trabalho.
* `<Name of node_n_...>` é o nome de cada nó alocado para esse trabalho.
* `<Cores of node_n_...>` é o número de núcleos no nó alocado para esse trabalho.

O número de núcleos (**$NBCORES**) também é calculado com base no número de nós (**$NBNODES**) e no número de núcleos por nó (fornecido como o parâmetro **$NBCORESPERNODE**).

Quanto às opções de MPI, as que são usadas com o Intel MPI no Azure são:

* `-mpi intel` para especificar o Intel MPI.
* `-fabric UDAPL` para usar verbos do InfiniBand do Azure.
* `-cpubind bandwidth,v` para otimizar a largura de banda para o MPI com o STAR-CCM+.
* `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"` para fazer com que o Intel MPI funcione com o Azure InfiniBand e para definir o número necessário de núcleos por nó.
* `-batch` para iniciar o STAR-CCM+ no modo de lote sem nenhuma interface do usuário.

Por fim, para iniciar um trabalho, certifique-se de que os nós estejam em funcionamento e estejam online no Gerenciador de Cluster. Em seguida, de um prompt de comando do PowerShell execute:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Parar os nós
Posteriormente, depois de concluir seus testes, você pode usar os seguintes comandos do PowerShell do HPC Pack para parar e iniciar os nós:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Próximas etapas
Tentar executar outras cargas de trabalho do Linux. Por exemplo, consulte:

* [Executar o NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure](hpcpack-cluster-namd.md)
* [Executar o OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](hpcpack-cluster-openfoam.md)

<!--Image references-->
[hndeploy]:media/hpcpack-cluster-starccm/hndeploy.png
[clustermanager]:media/hpcpack-cluster-starccm/ClusterManager.png
