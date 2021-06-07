---
title: Configurar a MPI (interface de transmissão de mensagens) para HPC-máquinas virtuais do Azure | Microsoft Docs
description: Saiba como configurar o MPI para HPC no Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 66de34c43ab1b3a6b4245f77196793bf9ad8530c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606633"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurar a interface de passagem de mensagens para HPC

A [interface de transmissão de mensagens (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) é uma biblioteca aberta e um padrão de fato para a paralelização de memória distribuída. Normalmente, ele é usado em muitas cargas de trabalho do HPC. As cargas de trabalho do HPC nas VMs [da série a e da](../../sizes-hpc.md) série [N](../../sizes-gpu.md) [compatíveis com RDMA](../../sizes-hpc.md#rdma-capable-instances) podem usar MPI para se comunicarem pela rede InfiniBand de baixa latência e alta largura de banda.
- Os tamanhos de VM habilitados para SR-IOV no Azure permitem que quase qualquer tipo de MPI seja usado com o Mellanox OFED.
- Em VMs não habilitadas para SR-IOV, as implementações MPI com suporte usam a interface do Microsoft Network Direct (ND) para se comunicar entre as VMs. Portanto, somente as versões do Microsoft MPI (MS-MPI) 2012 R2 ou posterior e do Intel MPI 5. x têm suporte. Versões posteriores (2017, 2018) da biblioteca de tempo de execução do Intel MPI podem ou não ser compatíveis com os drivers RDMA do Azure.

Para [VMs compatíveis](../../sizes-hpc.md#rdma-capable-instances)com o RDMA de Sr-IOV, as [imagens de VM CentOS-HPC](configure.md#centos-hpc-vm-images) versão 7,6 e posteriores são adequadas. Essas imagens de VM são otimizadas e pré-carregadas com os drivers OFED para RDMA e várias bibliotecas MPI comumente usadas e pacotes de computação científica e são a maneira mais fácil de começar.

Embora os exemplos aqui sejam para RHEL/CentOS, mas as etapas são gerais e podem ser usadas para qualquer sistema operacional Linux compatível, como Ubuntu (16, 4, 18, 4 19, 4, 20, 4) e SLES (12 SP4 e 15). Mais exemplos para configurar outras implementações de MPI em outros distribuições estão no [repositório azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> A execução de trabalhos MPI em VMs habilitadas para SR-IOV com determinadas bibliotecas MPI (como a plataforma MPI) pode exigir a configuração de chaves de partição (chaves p) em um locatário para isolamento e segurança. Siga as etapas na seção [descobrir chaves de partição](#discover-partition-keys) para obter detalhes sobre como determinar os valores de chave p e defini-los corretamente para um trabalho MPI com essa biblioteca MPI.

> [!NOTE]
> Os trechos de código a seguir são exemplos. É recomendável usar as versões estáveis mais recentes dos pacotes ou referir-se ao [repositório azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

## <a name="ucx"></a>UCX

A [comunicação unificada X (UCX)](https://github.com/openucx/ucx) é uma estrutura de APIs de comunicação para o HPC. Ele é otimizado para comunicação MPI sobre InfiniBand e funciona com muitas implementações de MPI, como OpenMP e MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> As compilações recentes do UCX corrigiram um [problema](https://github.com/openucx/ucx/pull/5965) no qual a interface InfiniBand correta é escolhida na presença de várias interfaces NIC. Mais detalhes [aqui](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) sobre a execução de MPI sobre InfiniBand quando a rede acelerada está habilitada na VM.

## <a name="hpc-x"></a>HPC-X

O [HPC-X Software Toolkit](https://www.mellanox.com/products/hpc-x-toolkit) contém UCX e HCOLL e pode ser criado em relação a UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Executar HPC-X

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>Otimizando os coletivas MPI

Os primitivos de comunicação coletiva MPI oferecem uma maneira flexível e portátil de implementar operações de comunicação de grupo. Eles são amplamente usados em vários aplicativos paralelos científicos e têm um impacto significativo no desempenho geral do aplicativo. Consulte o [artigo TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) para obter detalhes sobre parâmetros de configuração para otimizar o desempenho de comunicação coletiva usando a biblioteca do HPC-X e do HCOLL para comunicação coletiva.

> [!NOTE] 
> Com o HPC-X 2.7.4 +, pode ser necessário transmitir explicitamente LD_LIBRARY_PATH se a versão UCX em MOFED vs. em HPC-X for diferente.

## <a name="openmpi"></a>OpenMPi

Instale o UCX conforme descrito acima. O HCOLL faz parte do [HPC-X kit de ferramentas de software](https://www.mellanox.com/products/hpc-x-toolkit) e não requer instalação especial.

O OpenMPi pode ser instalado a partir dos pacotes disponíveis no repositório.

```bash
sudo yum install –y openmpi
```

É recomendável criar uma versão mais recente e estável do OpenMPi com UCX.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Para obter um desempenho ideal, execute o OpenMPi com `ucx` e `hcoll` .

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Verifique sua chave de partição, conforme mencionado acima.

## <a name="intel-mpi"></a>MPI Intel

Baixe sua escolha da versão do [Intel MPI](https://software.intel.com/mpi-library/choose-download). Altere a variável de ambiente I_MPI_FABRICS dependendo da versão.
- Intel MPI 2019 e 2021: use `I_MPI_FABRICS=shm:ofi` , `I_MPI_OFI_PROVIDER=mlx` . O `mlx` provedor usa UCX. O uso de verbos foi considerado instável e com menos desempenho. Consulte o [artigo TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) para obter mais detalhes.
- Intel MPI 2018: usar `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: usar `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

### <a name="non-sr-iov-vms"></a>VMs não SR-IOV
Para VMs não SR-IOV, um exemplo de download da [versão de avaliação gratuita](https://registrationcenter.intel.com/en/forms/?productid=1740) do tempo de execução 5. x é o seguinte:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Para saber as etapas de instalação, consulte o [Guia de instalação da Intel MPI Library](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).
Opcionalmente, talvez você queira habilitar o ptrace para processos não raiz de não-depurador (necessário para as versões mais recentes do Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Para SUSE Linux Enterprise Server versões de imagem de VM-SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium), SLES 12 SP4 e SLES 15, os drivers RDMA são instalados e os pacotes MPI da Intel são distribuídos na VM. Instale o Intel MPI executando o seguinte comando:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich2"></a>MVAPICH2

Criar MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Executando MVAPICH2.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi"></a>MPI da plataforma

Instale os pacotes necessários para a plataforma MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Siga o processo de instalação.

Os comandos a seguir são exemplos de como executar MPI pingpong e onreduza usando a plataforma MPI em VMs HBv3 usando imagens de VM CentOS-HPC 7,6, 7,8 e 8,1.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Instale o UCX conforme descrito acima. Criar MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Executando MPICH.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Verifique sua chave de partição, conforme mencionado acima.

## <a name="osu-mpi-benchmarks"></a>Benchmarks OSU MPI

Baixe os benchmarks e descompactar do [OSU MPI](http://mvapich.cse.ohio-state.edu/benchmarks/) .

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Crie parâmetros de comparação usando uma biblioteca MPI específica:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Os benchmarks MPI estão sob a `mpi/` pasta.


## <a name="discover-partition-keys"></a>Descobrir chaves de partição

Descobrir chaves de partição (chaves p) para se comunicar com outras VMs dentro do mesmo locatário (conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

O maior dos dois é a chave de locatário que deve ser usada com MPI. Exemplo: se as seguintes são as chaves p, 0x800b deve ser usado com MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Use a partição que não seja a chave de partição padrão (0x7FFF). UCX exige que o MSB da chave p seja limpo. Por exemplo, defina UCX_IB_PKEY como 0x000b para 0x800b.

Observe também que, desde que o locatário (conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais) exista, o PKEYs permanecerá o mesmo. Isso é verdadeiro mesmo quando os nós são adicionados/excluídos. Novos locatários obtêm PKEYs diferentes.


## <a name="set-up-user-limits-for-mpi"></a>Configurar os limites de usuário para MPI

Configure os limites de usuário para MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>Configurar chaves SSH para MPI

Configure as chaves SSH para tipos MPI que o exigem.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

A sintaxe acima pressupõe um diretório base compartilhado, senão, o diretório ssh deve ser copiado para cada nó.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as VMs da série H e da série [N](../../sizes-gpu.md) [habilitada](../../sizes-hpc.md#rdma-capable-instances) [para](../../sizes-hpc.md) InfiniBand
- Examine as visão geral da série [HBv3](hbv3-series-overview.md) e [HC-Series](hc-series-overview.md).
- Leia sobre os comunicados mais recentes, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs da comunidade técnica de computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
