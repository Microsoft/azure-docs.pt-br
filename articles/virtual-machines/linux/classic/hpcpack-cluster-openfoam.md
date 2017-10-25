---
title: Executar o OpenFOAM com o HPC Pack nas VMs do Linux | Microsoft Docs
description: "Implante um cluster do Microsoft HPC Pack no Azure e execute um trabalho OpenFOAM em vários nós de computação do Linux em uma rede RDMA."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: c0bb1637-bb19-48f1-adaa-491808d3441f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep
ms.openlocfilehash: ef124a8983fa112d499252460bff9ed2fcccc02b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Executar o OpenFoam com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure
Este artigo mostra uma maneira de executar OpenFoam em máquinas virtuais do Azure. Aqui, você implanta um cluster do Microsoft HPC Pack com nós de computação do Linux no Azure e executa um trabalho [OpenFoam](http://openfoam.com/) com Intel MPI. Você pode usar VMs do Azure compatíveis com RDMA para os nós de computação, para que eles se comuniquem pela rede RDMA do Azure. Outras opções para executar o OpenFoam no Azure incluem imagens comerciais totalmente configuradas disponíveis no Marketplace, como [OpenFoam 2.3 no CentOS 6](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/) da UberCloud e executando no [Lote do Azure](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (Operação e Manipulação de Campo Aberto) é um pacote de software com CFD (dinâmica de fluido computacional) aberto, amplamente utilizado na Engenharia e nas Ciências em organizações comerciais e acadêmicas. Ele inclui ferramentas para criar malhas, especialmente o snappyHexMesh, um mesher em paralelo para geometrias CAD complexas e de pré e pós-processamento. Quase todos os processos são executados em paralelo, permitindo que os usuários se beneficiam do hardware do computador à sua disposição.  

O Microsoft HPC Pack fornece recursos para executar aplicativos de HPC e paralelos em larga escala, incluindo aplicativos MPI, em clusters de máquinas virtuais do Microsoft Azure. O HPC Pack também dá suporte a aplicativos de HPC no Linux em VMs com nós de computação do Linux implantadas em um cluster do HPC Pack. Consulte a [Introdução aos nós de computação do Linux em um cluster do HPC Pack no Azure](hpcpack-cluster.md) para saber como começar a usar os nós de computação do Linux com o HPC Pack.

> [!NOTE]
> Este artigo ilustra como executar uma carga de trabalho do Linux MPI com o HPC Pack. Ele pressupõe que você tem alguma familiaridade com a administração do sistema Linux e com a execução das cargas de trabalho MPI nos clusters do Linux. Se você usar versões do MPI e do OpenFOAM diferentes daquelas mostradas neste artigo, você talvez precise modificar algumas etapas de instalação e configuração. 
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* **Cluster HPC Pack com nós de computação do Linux compatíveis com RDMA** – Implante um cluster HPC Pack com nós de computação do Linux do tamanho A8, A9, H16r ou H16rm usando um [modelo do Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou um [script do Azure PowerShell](hpcpack-cluster-powershell-script.md). Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](hpcpack-cluster.md) para encontrar os pré-requisitos e etapas de cada opção. Se você escolher a opção de implantação de script do PowerShell, consulte o arquivo de configuração de exemplo nos arquivos de exemplo no final deste artigo. Use esta configuração para implantar um cluster de HPC Pack com base no Azure consistindo em um nó principal do tamanho do A8 Windows Server 2012 R2 e dois nós de computação do tamanho do A8 SUSE Linux Enterprise Server 12. Substitua os valores apropriados por sua assinatura e nomes de serviço. 
  
  **Informações adicionais importantes**
  
  * Para pré-requisitos de rede RDMA Linux no Azure, consulte [Tamanhos de VM de computação de alto desempenho](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  * Se você usar a opção de implantação de script do Powershell, implante todos os nós de computação Linux em um serviço de nuvem para usar a conexão de rede RDMA.
  * Depois de implantar os nós do Linux, conecte-se via SSH para executar tarefas administrativas adicionais. Encontre os detalhes da conexão SSH para cada VM do Linux no portal do Azure.  
* **Intel MPI** – Para executar o OpenFOAM em nós de computação SLES 12 HPC no Azure, é necessário instalar o tempo de execução do Intel MPI Library 5 no site [Intel.com](https://software.intel.com/en-us/intel-mpi-library/). (O Intel MPI 5 está pré-instalado em imagens do HPC baseado em CentOS.)  Em uma etapa posterior, se necessário, instale o Intel MPI em seus nós de computação do Linux. Para se preparar para essa etapa, depois de se registrar na Intel, siga o link no email de confirmação para a página da Web relacionada. Em seguida, copie o link de download do arquivo .tgz para a versão apropriada do Intel MPI. Este artigo se baseia no Intel MPI versão 5.0.3.048.
* **OpenFOAM Source Pack** - baixe o software OpenFOAM Source Pack do Linux no [site OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). Este artigo baseia-se no Source Pack versão 2.3.1, disponível para download como OpenFOAM-2.3.1.tgz. Siga as instruções neste artigo para descompactar e compilar o OpenFOAM nos nós de computação do Linux.
* **EnSight** (opcional): para ver os resultados da simulação do OpenFOAM, baixe e instale o programa de análise e visualização [EnSight](https://www.ceisoftware.com/download/) . As informações de licenciamento e download estão no site EnSight.

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurar a relação de confiança mútua entre os nós de computação
Executar um trabalho de nós cruzados em vários nós do Linux requer que os nós tenham uma relação de confiança entre si (por **rsh** ou **ssh**). Quando você cria o cluster do HPC Pack com o script de implantação de IaaS do Microsoft HPC Pack, o script configura automaticamente uma relação de confiança mútua permanente para a conta de administrador que você especificar. Para usuários que não sejam administradores criados no domínio do cluster, é necessário configurar uma relação de confiança mútua temporária entre os nós quando um trabalho é alocado para eles e destruir a relação depois que o trabalho for concluído. Para estabelecer confiança para cada usuário, forneça um par de chaves RSA para o cluster usado pelo HPC Pack para a relação de confiança.

### <a name="generate-an-rsa-key-pair"></a>Gerar um par de chaves RSA
É fácil gerar um par de chaves RSA, que contém uma chave pública e uma chave privada, executando o comando **ssh-keygen** do Linux.

1. Faça logon em um computador com Linux.
2. Execute o comando a seguir:
   
   ```
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Pressione **Enter** para usar as configurações padrão até que o comando seja concluído. Não insira uma frase secreta aqui. Quando for solicitada uma senha, basta pressionar **Enter**.
   > 
   > 
   
   ![Gerar um par de chaves RSA][keygen]
3. Altere o diretório para o diretório ~/.ssh. A chave privada é armazenada em id_rsa e a chave pública em id_rsa.pub.
   
   ![Chaves públicas e privadas][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Adicionar o par de chaves ao cluster do HPC Pack
1. Faça uma conexão da Área de Trabalho Remota com o nó principal com sua conta de administrador do HPC Pack (a conta de administrador criada quando você executou o script de implantação).
2. Use os procedimentos padrão do Windows Server para criar uma conta de usuário de domínio no domínio do Active Directory do cluster. Por exemplo, use o Usuário do Active Directory e a ferramenta Computers no nó principal. Com os exemplos neste artigo, supomos que você criará um usuário de domínio chamado hpclab\hpcuser.
3. Crie um arquivo chamado C:\cred.xml e copie os dados da chave RSA nele. Um arquivo cred.xml de exemplo está no final deste artigo.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
4. Abra um Prompt de Comando e digite o seguinte comando para definir os dados de credenciais para a conta de hpclab\hpcuser. Use o parâmetro **extendeddata** para transmitir o nome do arquivo C:\cred.xml que você criou para os dados da chave.
   
   ```
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Esse comando é concluído com êxito sem resultados. Depois de definir as credenciais para as contas de usuário, você precisa executar os trabalhos, armazenar o arquivo cred.xml em um local seguro ou excluí-lo.
5. Se você gerou o par de chaves RSA em um dos nós do Linux, lembre-se de excluir as chaves após terminar de usá-las. Se o HPC Pack encontrar um arquivo id_rsa ou id_rsa.pub existente, ele não definirá uma relação de confiança mútua.

> [!IMPORTANT]
> Não recomendamos executar um trabalho do Linux como um administrador de cluster em um cluster compartilhado, já que um trabalho enviado por um administrador é executado na conta raiz nos nós do Linux. No entanto, um trabalho enviado por um usuário não administrador é executado sob uma conta de usuário local do Linux com o mesmo nome que o usuário do trabalho. Nesse caso, HPC Pack define a relação de confiança mútua para este usuário Linux em todos os nós alocados para o trabalho. Você pode configurar o usuário do Linux manualmente nos nós do Linux antes de executar o trabalho, ou o HPC Pack criará o usuário automaticamente quando o trabalho for enviado. Se o HPC Pack criar o usuário, ele o excluirá depois que o trabalho for concluído. Para reduzir ameaças de segurança, o HPC Pack remove as chaves após a conclusão do trabalho.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurar um compartilhamento de arquivos para nós do Linux
Agora, configure um compartilhamento SMB padrão em uma pasta no nó principal. Para permitir que os nós do Linux acessem os arquivos do aplicativo com um caminho comum, monte a pasta compartilhada nos nós do Linux. Se desejar, você pode usar outra opção de compartilhamento de arquivos, como o compartilhamento de Arquivos do Azure - recomendado para muitos cenários - ou um compartilhamento NFS. Consulte as informações de compartilhamento arquivos e as etapas detalhadas em [Introdução aos nós de computação Linux em um Cluster do HPC Pack no Azure](hpcpack-cluster.md).

1. Crie uma pasta no nó principal e compartilhe-a com todos, configurando privilégios de leitura/gravação. Por exemplo, compartilhe C:\OpenFOAM no nó principal como \\\\SUSE12RDMA-HN\OpenFOAM. Aqui, *SUSE12RDMA-HN* é o nome de host do nó principal.
2. Abra uma janela do Windows PowerShell e execute os seguintes comandos:
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

O primeiro comando cria uma pasta chamada /openfoam em todos os nós no grupo LinuxNodes. O segundo comando monta a pasta compartilhada //SUSE12RDMA-HN/OpenFOAM nos nós do Linux com os bits dir_mode e file_mode definidos como 777. O *nome de usuário* e a *senha* no comando devem ser as credenciais de um usuário no nó principal.

> [!NOTE]
> O símbolo "\`" no segundo comando é um símbolo de escape para o PowerShell. "\`," significa que "," (uma vírgula) é uma parte do comando.
> 
> 

## <a name="install-mpi-and-openfoam"></a>Instalar o MPI e o OpenFOAM
Para executar o OpenFOAM como um trabalho MPI na rede RDMA, você precisa compilar o OpenFOAM com as bibliotecas do Intel MPI. 

Primeiro, execute vários comandos **clusrun** para instalar as bibliotecas do Intel MPI (se ainda não estiverem instaladas) e o OpenFOAM nos seus nós do Linux. Use o compartilhamento de nós principais configurado anteriormente para compartilhar os arquivos de instalação entre os nós do Linux.

> [!IMPORTANT]
> Essas etapas de instalação e compilação são exemplos. Você precisa de algum conhecimento de administração do sistema Linux para garantir a instalação correta dos compiladores dependentes e das bibliotecas. Talvez você precise modificar determinadas variáveis de ambiente ou outras configurações para as versões do Intel MPI e do OpenFOAM. Para obter detalhes, consulte [Guia de instalação do Intel MPI Library para Linux](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) e [Instalação do OpenFOAM Source Pack](http://openfoam.org/download/2-3-1-source/) para seu ambiente.
> 
> 

### <a name="install-intel-mpi"></a>Instalar o Intel MPI
Salve o pacote de instalação baixado para o Intel MPI (l_mpi_p_5.0.3.048.tgz neste exemplo) em C:\OpenFoam no nó principal para que os nós do Linux possam acessar esse arquivo em /openfoam. Em seguida, execute **clusrun** para instalar a biblioteca do Intel MPI em todos os nós do Linux.

1. Os comandos a seguir copiam o pacote de instalação e extrai-o para /opt/intel em cada nó.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. Para instalar a Intel MPI Library silenciosamente, use um arquivo silent.cfg. Você pode encontrar um exemplo nos arquivos de exemplo ao final deste artigo. Coloque esse arquivo na pasta compartilhada /openfoam. Para obter detalhes sobre o arquivo silent.cfg, consulte [Intel MPI Library para o Guia de Instalação do Linux - Instalação Silenciosa](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).
   
   > [!TIP]
   > Salve o arquivo silent.cfg como um arquivo de texto com as terminações de linha do Linux (LF apenas, não CR LF). Esta etapa garante que ele seja executado corretamente nos nós do Linux.
   > 
   > 
3. Instale a Intel MPI Library no modo silencioso.
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>Configurar o MPI
Para testar, adicione as seguintes linhas a /etc/security/limits.conf em cada um dos nós do Linux:

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Depois de atualizar o arquivo limits.conf, reinicie os nós do Linux. Por exemplo, use o comando **clusrun** :

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Depois de reiniciar, verifique se a pasta compartilhada está montada como /openfoam.

### <a name="compile-and-install-openfoam"></a>Compilar e instalar o OpenFOAM
Salve o pacote de instalação baixado para o OpenFOAM Source Pack (OpenFOAM-2.3.1.tgz neste exemplo) em C:\OpenFoam no nó principal para que os nós do Linux possam acessar esse arquivo em /openfoam. Em seguida, execute comandos **clusrun** para compilar o OpenFOAM em todos os nós do Linux.

1. Crie uma pasta /opt/OpenFOAM em cada nó do Linux, copie o pacote de origem para essa pasta e extraia-o nesse local.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. Para compilar o OpenFOAM com a Intel MPI Library, primeiro configure algumas variáveis de ambiente para o Intel MPI e o OpenFOAM. Use um script bash chamado settings.sh para definir as variáveis. Você pode encontrar um exemplo nos arquivos de exemplo ao final deste artigo. Coloque esse arquivo (salvo com as terminações de linha do Linux) na pasta compartilhada /openfoam. Esse arquivo também contém configurações para os tempos de execução MPI e OpenFOAM que você usará posteriormente para executar um trabalho do OpenFOAM.
3. Instale os pacotes dependentes necessários para compilar o OpenFOAM. Dependendo de sua distribuição do Linux, você precisará primeiro adicionar um repositório. Execute comandos **clusrun** semelhantes ao seguinte:
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    Se necessário, use SSH em cada nó do Linux para executar os comandos a fim de confirmar se são executados corretamente.
4. Execute o comando a seguir para compilar o OpenFOAM. O processo de compilação leva algum tempo para ser concluído e gera uma grande quantidade de informações de log na saída padrão. Portanto, use a opção **/interleaved** para exibir a saída intercalada.
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > O símbolo "\`" no comando é um símbolo de escape para o PowerShell. "\`&" significa que o "&" é uma parte do comando.
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>Preparar para executar um trabalho do OpenFOAM
Agora, prepare-se executar um trabalho MPI chamado sloshingTank3D, que é um dos exemplos de OpenFoam, em dois nós do Linux. 

### <a name="set-up-the-runtime-environment"></a>Configurar o ambiente do tempo de execução
Para configurar os ambientes do tempo de execução para o MPI e o OpenFOAM em todos os nós do Linux, execute o seguinte comando em uma janela do Windows PowerShell no nó principal. (Este comando é válido apenas para o Linux SUSE)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Preparar os dados de exemplo
Use o compartilhamento de nós principais configurado anteriormente para compartilhar os arquivos entre os nós do Linux (montados as /openfoam).

1. SSH para um dos nós de computação do Linux.
2. Execute o seguinte comando para configurar o ambiente do tempo de execução do OpenFOAM, se você ainda não fez isso.
   
   ```
   $ source /openfoam/settings.sh
   ```
3. Copie o exemplo sloshingTank3D para a pasta compartilhada e navegue até ele.
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. Quando você usa os parâmetros padrão desse exemplo, pode levar vários minutos para ele ser executado, portanto, convém modificar alguns parâmetros para que ele seja executado mais rapidamente. Uma opção simples é modificar as variáveis da etapa de tempo deltaT e writeInterval no arquivo de sistema/controlDict. Esse arquivo armazena todos os dados de entrada relacionados ao controle de tempo e leitura e gravação de dados da solução. Por exemplo, você pode alterar o valor de deltaT de 0,05 para 0,5 e o valor de writeInterval de 0,05 para 0,5.
   
   ![Modificar as variáveis da etapa][step_variables]
5. Especifique os valores desejados para as variáveis no arquivo system/decomposeParDict. Este exemplo usa dois nós do Linux, cada um com oito núcleos, portanto, defina numberOfSubdomains para 16 e n de hierarchicalCoeffs para (1 1 16), que significa executar o OpenFOAM em paralelo com 16 processos. Para obter mais informações, consulte [OpenFOAM User Guide: 3.4 Running applications in parallel](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4)(Guia do usuário OpenFOAM: 3.4 Execução de aplicativos em paralelo).
   
   ![Decompor processos][decompose]
6. Execute os seguintes comandos no diretório sloshingTank3D para preparar os dados de exemplo.
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. No nó principal, você deve ver que os arquivos de dados de exemplo são copiados para C:\OpenFoam\sloshingTank3D. (C:\OpenFoam é a pasta compartilhada no nó principal.)
   
   ![Arquivos de dados no nó principal][data_files]

### <a name="host-file-for-mpirun"></a>Arquivo de host para mpirun
Nesta etapa, crie um arquivo de host (uma lista de nós de computação) que o comando **mpirun** usa.

1. Em um dos nós do Linux, crie um arquivo chamado hostfile em /openfoam, para que esse arquivo possa ser obtido em /openfoam/hostfile em todos os nós do Linux.
2. Grave os nomes dos nós do Linux nesse arquivo. Neste exemplo, o arquivo contém os seguintes nomes:
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > Você também pode criar esse arquivo em C:\OpenFoam\hostfile no nó principal. Se você escolher esta opção, salve-o como um arquivo de texto com as terminações de linha do Linux (LF apenas, não CR LF). Isso garante que ele seja executado corretamente nos nós do Linux.
   > 
   > 
   
   **Wrapper de script bash**
   
   Se você tiver vários nós do Linux e desejar que seu trabalho seja executado somente em alguns deles, não será uma boa ideia usar um arquivo de host fixo porque você não sabe quais nós serão alocados para seu trabalho. Neste caso, escreva um wrapper de script bash para **mpirun** a fim de criar o arquivo de host automaticamente. Você pode encontrar um wrapper de script bash de exemplo chamado hpcimpirun.sh no final deste artigo e salvá-lo como /openfoam/hpcimpirun.sh. Esse script de exemplo faz o seguinte:
   
   1. Define as variáveis de ambiente para **mpirun**e alguns parâmetros do comando de adição para executar o trabalho MPI por meio da rede RDMA. Nesse caso, ele define as seguintes variáveis:
      
      * I_MPI_FABRICS=shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION=0
   2. Cria um arquivo de host de acordo com a variável de ambiente $CCP_NODES_CORES, que é definida pelo nó principal do HPC quando o trabalho é ativado.
      
      O formato $CCP_NODES_CORES segue este padrão:
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      onde
      
      * `<Number of nodes>` : o número de nós alocados para esse trabalho.  
      * `<Name of node_n_...>` : o nome de cada nó alocado para esse trabalho.
      * `<Cores of node_n_...>`: o número de núcleos no nó alocado para esse trabalho.
      
      Por exemplo, se o trabalho precisar de dois núcleos para ser executado, $CCP_NODES_CORES será semelhante a
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. Chama o comando **mpirun** e acrescenta dois parâmetros à linha de comando.
      
      * `--hostfile <hostfilepath>: <hostfilepath>` - o caminho do arquivo de host que o script cria
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` - uma variável de ambiente definida pelo nó principal do HPC Pack, que armazena o número de núcleos totais alocados para esse trabalho. Nesse caso, especifica o número de processos para **mpirun**.

## <a name="submit-an-openfoam-job"></a>Enviar um trabalho do OpenFOAM
Agora, você pode enviar um trabalho no Gerenciador de Cluster de HPC. Você precisa passar o script hpcimpirun.sh nas linhas de comando para algumas das tarefas de trabalho.

1. Conecte-se ao nó principal do cluster e inicie o Gerenciador de Cluster do HPC.
2. Em **Gerenciamento de Recursos**, verifique se os nós de computação do Linux estão no estado **Online**. Se não estiverem, selecione-os e clique em **Colocar Online**.
3. Em **Gerenciamento de Trabalhos**, clique em **Novo Trabalho**.
4. Insira um nome para o trabalho, como *sloshingTank3D*.
   
   ![Detalhes do trabalho][job_details]
5. Em **Recursos de trabalho**, selecione o tipo de recurso como “Nó” e defina o Mínimo para 2. Essa configuração executa o trabalho em dois nós do Linux, cada um deles tem oito núcleos neste exemplo.
   
   ![Recursos de trabalho][job_resources]
6. Clique em **Editar Tarefas** na navegação esquerda e clique em **Adicionar** para adicionar uma tarefa ao trabalho. Adicione quatro tarefas ao trabalho com as seguintes linhas de comando e configurações para as tarefas.
   
   > [!NOTE]
   > A execução `source /openfoam/settings.sh` configura os ambientes do tempo de execução OpenFOAM e MPI para que cada uma das tarefas a seguir chame antes do comando OpenFOAM.
   > 
   > 
   
   * **Tarefa 1**. Execute **decomposePar** para gerar arquivos de dados a fim de executar o **interDyMFoam** em paralelo.
     
     * Atribuir um nó à tarefa
     * **Linha de comando** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **Diretório de trabalho** - /openfoam/sloshingTank3D
     
     Consulte a figura a seguir. Você pode configurar as tarefas restantes da mesma forma.
     
     ![Detalhes da tarefa 1][task_details1]
   * **Tarefa 2**. Execute **interDyMFoam** em paralelo para calcular o exemplo.
     
     * Atribuir dois nós à tarefa
     * **Linha de comando** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **Diretório de trabalho** - /openfoam/sloshingTank3D
   * **Tarefa 3**. Execute **reconstructPar** para mesclar os conjuntos de diretórios de tempo de cada diretório processor_N_ em um único conjunto.
     
     * Atribuir um nó à tarefa
     * **Linha de comando** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **Diretório de trabalho** - /openfoam/sloshingTank3D
   * **Tarefa 4**. Execute **foamToEnsight** em paralelo para converter os arquivos de resultado do OpenFOAM no formato EnSight e colocar os arquivos EnSight em um diretório chamado Ensight no diretório de caso.
     
     * Atribuir dois nós à tarefa
     * **Linha de comando** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **Diretório de trabalho** - /openfoam/sloshingTank3D
7. Adicione dependências a essas tarefas na  ordem de tarefas crescente.
   
   ![Dependências da tarefa][task_dependencies]
8. Clique em **Enviar** para executar este trabalho.
   
   Por padrão, o HPC Pack envia o trabalho como a sua atual conta de usuário conectado. Depois de clicar em **Enviar**, uma caixa de diálogo pode solicitar que você insira o nome de usuário e a senha.
   
   ![Credenciais de trabalho][creds]
   
   Em algumas condições, o HPC Pack memoriza as informações do usuário inseridas anteriormente e não mostra esta caixa de diálogo. Para fazer o HPC Pack mostrar novamente, digite o seguinte comando em um prompt de comando e, então, envie o trabalho.
   
   ```
   hpccred delcreds
   ```
9. O trabalho leva de alguns minutos a várias horas de acordo com os parâmetros que você definiu para o exemplo. No mapa de calor, você vê o trabalho em execução em dois nós do Linux. 
   
   ![Mapa de calor][heat_map]
   
   Em cada nó, oito processos são iniciados.
   
   ![Processos do Linux][linux_processes]
10. Quando o trabalho for concluído, localize os resultados do trabalho nas pastas em C:\OpenFoam\sloshingTank3D e os arquivos de log em C:\OpenFoam.

## <a name="view-results-in-ensight"></a>Exibir resultados no EnSight
Opcionalmente, use o [EnSight](https://www.ceisoftware.com/) para visualizar e analisar os resultados do trabalho do OpenFOAM. Para obter mais informações sobre a visualização e a animação no EnSight, consulte o [guia em vídeo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1. Depois de instalar o EnSight no nó principal, inicie-o.
2. Abra o C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.
   
   Você verá um tanque no visualizador.
   
   ![Tanque no EnSight][tank]
3. Crie um **Isosurface** em **internalMesh**, em seguida, escolha a variável **alpha_water**.
   
   ![Criar um isosurface][isosurface]
4. Defina a cor do **Isosurface_part** criado na etapa anterior. Por exemplo, defina-a para água azul.
   
   ![Editar cor do isosurface][isosurface_color]
5. Crie um **volume Iso** em **paredes** selecionando **paredes** no painel **Partes** e clique no botão **Isosurfaces** na barra de ferramentas.
6. Na caixa de diálogo, selecione **Tipo** como **Isovolume** e defina o Mín. do **Intervalo Isovolume** para 0,5. Para criar o isovolume, clique em **Criar com partes selecionadas**.
7. Defina a cor de **Iso_volume_part** criado na etapa anterior. Por exemplo, defina para água azul profundo.
8. Defina a cor de **paredes**. Por exemplo, defina para branco transparente.
9. Agora, clique em **Reproduzir** para ver os resultados da simulação.
   
    ![Resultado do tanque][tank_result]

## <a name="sample-files"></a>Arquivos de exemplo
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Exemplo de arquivo de configuração XML para implantação de cluster pelo script do PowerShell
 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>Exemplo de arquivo cred.xml
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>Arquivo silent.cfg de exemplo para instalar MPI
```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Script settings.sh de exemplo
```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


### <a name="sample-hpcimpirunsh-script"></a>Script hpcimpirun.sh de exemplo
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]:media/hpcpack-cluster-openfoam/keygen.png
[keys]:media/hpcpack-cluster-openfoam/keys.png
[step_variables]:media/hpcpack-cluster-openfoam/step_variables.png
[data_files]:media/hpcpack-cluster-openfoam/data_files.png
[decompose]:media/hpcpack-cluster-openfoam/decompose.png
[job_details]:media/hpcpack-cluster-openfoam/job_details.png
[job_resources]:media/hpcpack-cluster-openfoam/job_resources.png
[task_details1]:media/hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]:media/hpcpack-cluster-openfoam/task_dependencies.png
[creds]:media/hpcpack-cluster-openfoam/creds.png
[heat_map]:media/hpcpack-cluster-openfoam/heat_map.png
[tank]:media/hpcpack-cluster-openfoam/tank.png
[tank_result]:media/hpcpack-cluster-openfoam/tank_result.png
[isosurface]:media/hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]:media/hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]:media/hpcpack-cluster-openfoam/linux_processes.png
