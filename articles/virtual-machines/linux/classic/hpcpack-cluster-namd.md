---
title: NAMD com Microsoft HPC Pack em VMs do Linux | Microsoft Docs
description: "Implante um cluster do Microsoft HPC Pack no Azure e executar uma simulação do NAMD com charmrun em vários nós de computação do Linux."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 0c0b9875b4153edcc0ec0096577d041d394a842f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Executar o NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure
Este artigo mostra uma maneira de executar uma carga de trabalho de computação de alto desempenho (HPC) do Linux em máquinas virtuais do Azure. Aqui, você configura um cluster do [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) no Azure com nós de computação Linux e executa uma simulação do [NAMD](http://www.ks.uiuc.edu/Research/namd/) para calcular e visualizar a estrutura de um sistema biomolecular grande.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* O **NAMD** (para o programa Nanoscale Molecular Dynamics) é um pacote de dinâmica molecular paralela criado para a simulação de alto desempenho de sistemas biomoleculares grandes que contêm milhões de átomos. Vírus, estruturas de célula e grande proteínas são exemplos desses sistemas. O NAMD é dimensionado para centenas de núcleos de simulações típicas e para mais de 500.000 núcleos para as simulações maiores.
* O **Microsoft HPC Pack** fornece recursos para executar aplicativos de HPC e paralelos em larga escala em clusters de computadores locais ou nas máquinas virtuais do Azure. Originalmente desenvolvido como uma solução para cargas de trabalho HPC, o HPC Pack agora permite a execução de aplicativos HPC Linux em VMs do nó de computação do Linux implantadas em um cluster do HPC Pack. Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](hpcpack-cluster.md) para ver uma introdução.

## <a name="prerequisites"></a>Pré-requisitos
* **Cluster HPC Pack com nós de computação do Linux**: implante um cluster HPC Pack com nós de computação do Linux no Azure usando um [modelo do Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou um [script do Azure PowerShell](hpcpack-cluster-powershell-script.md). Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](hpcpack-cluster.md) para encontrar os pré-requisitos e etapas de cada opção. Se você escolher a opção de implantação de script do PowerShell, consulte o arquivo de configuração de exemplo nos arquivos de exemplo no final deste artigo. Este arquivo configura um cluster de HPC Pack com base no Azure, que consiste em um nó principal do Windows Server 2012 R2 e quatro nós de computação grandes do CentOS 6.6. Personalize este arquivo conforme a necessidade para seu ambiente.
* **Arquivos do tutorial e software do NAMD** : baixe o software do NAMD para o Linux no site do [NAMD](http://www.ks.uiuc.edu/Research/namd/) (o registro é obrigatório). Este artigo se baseia na versão 2.10 do NAMD e usa o arquivamento do [Linux-x86_64 (64 bits Intel/AMD com Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310). Baixe também os [Arquivos do tutorial do NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Os downloads são arquivos .tar e você precisa de uma ferramenta do Windows para extrair os arquivos no nó principal do cluster. Para extrair os arquivos, siga as instruções mais adiante neste artigo. 
* **VMD** (opcional) – Para ver os resultados do trabalho do NAMD, baixe e instale o programa de visualização molecular [VMD](http://www.ks.uiuc.edu/Research/vmd/) em um computador de sua escolha. A versão atual é 1.9.2. Visite o site de download do VMD para começar.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurar a relação de confiança mútua entre os nós de computação
Executar um trabalho de nós cruzados em vários nós do Linux requer que os nós tenham uma relação de confiança entre si (por **rsh** ou **ssh**). Quando você cria o cluster do HPC Pack com o script de implantação de IaaS do Microsoft HPC Pack, o script configura automaticamente uma relação de confiança mútua permanente para a conta de administrador que você especificar. Para usuários que não sejam administradores criados no domínio do cluster, é necessário configurar uma relação de confiança mútua temporária entre os nós quando um trabalho é alocado para eles. Em seguida, destrua a relação depois que o trabalho for concluído. Para fazer isso para cada usuário, forneça um par de chaves RSA para o cluster usado pelo HPC Pack para estabelecer a relação de confiança. Siga as instruções.

### <a name="generate-an-rsa-key-pair"></a>Gerar um par de chaves RSA
É fácil gerar um par de chaves RSA, que contém uma chave pública e uma chave privada, executando o comando **ssh-keygen** do Linux.

1. Faça logon em um computador com Linux.
2. Execute o comando a seguir:
   
   ```bash
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
1. [Conecte-se por meio da Área de Trabalho Remota](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à VM do nó principal usando as credenciais de domínio fornecidas quando você implantou o cluster (por exemplo, hpc\clusteradmin). Você gerencia o cluster do nó principal.
2. Use os procedimentos padrão do Windows Server para criar uma conta de usuário de domínio no domínio do Active Directory do cluster. Por exemplo, use o Usuário do Active Directory e a ferramenta Computers no nó principal. Com os exemplos neste artigo, supomos que você criará um usuário de domínio chamado hpcuser no domínio hpclab (hpclab\hpcuser).
3. Adicione o usuário do domínio ao cluster HPC Pack como um usuário do cluster. Para obter instruções, consulte [Adicionar ou remover usuários de cluster](https://technet.microsoft.com/library/ff919330.aspx).
4. Crie um arquivo chamado C:\cred.xml e copie os dados da chave RSA nele. Você pode encontrar um exemplo nos arquivos de exemplo ao final deste artigo.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Abra um Prompt de Comando e digite o seguinte comando para definir os dados de credenciais para a conta de hpclab\hpcuser. Use o parâmetro **extendeddata** para transmitir o nome do arquivo C:\cred.xml que você criou para os dados da chave.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Esse comando é concluído com êxito sem resultados. Depois de definir as credenciais para as contas de usuário, você precisa executar os trabalhos, armazenar o arquivo cred.xml em um local seguro ou excluí-lo.
6. Se você gerou o par de chaves RSA em um dos nós do Linux, lembre-se de excluir as chaves após terminar de usá-las. O HPC Pack não define a relação de confiança mútua se ele encontrar um arquivo id_rsa ou id_rsa.pub existente.

> [!IMPORTANT]
> Não recomendamos executar um trabalho do Linux como um administrador de cluster em um cluster compartilhado, já que um trabalho enviado por um administrador é executado na conta raiz nos nós do Linux. Um trabalho enviado por um usuário não administrador é executado sob uma conta de usuário local do Linux com o mesmo nome que o usuário do trabalho. Nesse caso, o HPC Pack define a relação de confiança mútua para este usuário Linux em todos os nós alocados para o trabalho. Você pode configurar o usuário do Linux manualmente nos nós do Linux antes de executar o trabalho, ou o HPC Pack criará o usuário automaticamente quando o trabalho for enviado. Se o HPC Pack criar o usuário, ele o excluirá depois que o trabalho for concluído. Para reduzir a ameaça à segurança, as chaves são removidas após a conclusão do trabalho em nós.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurar um compartilhamento de arquivos para nós do Linux
Agora, configure um compartilhamento de arquivo SMB e monte a pasta compartilhada em todos os nós do Linux para permitir que os nós do Linux acessem os arquivos NAMD com um caminho comum. A seguir, as etapas para montar uma pasta compartilhada no nó principal. Um compartilhamento é recomendado para distribuições, como CentOS 6.6, que atualmente não têm suporte para o serviço de Arquivos do Azure. Se os nós do Linux derem suporte a compartilhamento de Arquivo do Azure, confira [Como usar o armazenamento de Arquivos do Azure com Linux](../../../storage/files/storage-how-to-use-files-linux.md). Para opções de compartilhamento de arquivos adicionais com o HPC Pack, e as etapas em [Introdução aos nós de computação do Linux em um cluster do HPC Pack no Azure](hpcpack-cluster.md).

1. Crie uma pasta no nó principal e compartilhe-a com todos, configurando privilégios de leitura/gravação. Neste exemplo, \\\\CentOS66HN\Namd é o nome da pasta, em que CentOS66HN é o nome de host do nó principal.
2. Crie uma subpasta chamada namd2 na pasta compartilhada. No namd2, crie outra subpasta chamada namdsample.
3. Extraia os arquivos do NAMD na pasta usando uma versão do Windows de **tar** ou outro utilitário do Windows que funciona em arquivos .tar. 
   
   * Extraia o arquivo tar do NAMD para \\\\CentOS66HN\Namd\namd2.
   * Extraia os arquivos do tutorial em \\\\CentOS66HN\Namd\namd2\namdsample.
4. Abra uma janela do Windows PowerShell e execute os seguintes comandos para montar a pasta compartilhada nos nós do Linux.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

O primeiro comando cria uma pasta chamada /namd2 em todos os nós do grupo LinuxNodes. O segundo comando monta a pasta compartilhada //CentOS66HN/Namd/namd2 para a pasta com os bits de dir_mode e file_mode definidos como 777. O *nome de usuário* e a *senha* no comando devem ser as credenciais de um usuário no nó principal.

> [!NOTE]
> O símbolo "\`" no segundo comando é um símbolo de escape para o PowerShell. "\`," significa que "," (uma vírgula) é uma parte do comando.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Criar um script Bash para executar um trabalho NAMD
Seu trabalho NAMD requer um arquivo *nodelist* para **charmrun** a fim de determinar o número de nós a ser usado ao iniciar processos do NAMD. Você usa um script Bash que gera o arquivo nodelist e executa **charmrun** com esse arquivo nodelist. Depois, você pode enviar um trabalho do NAMD no Gerenciador de Cluster do HPC que chama esse script.

Usando um editor de texto de sua escolha, crie o script Bash na pasta /namd2 que contém os arquivos do programa NAMD e nomeie-o hpccharmrun.sh. Para uma rápida prova de conceito, copie o script hpccharmrun.sh de exemplo no final deste artigo e vá para [Enviar um trabalho do NAMD](#submit-a-namd-job).

> [!TIP]
> Salve o script como um arquivo de texto com as terminações de linha do Linux (somente LF, não CR LF). Isso garante que ele seja executado corretamente nos nós do Linux.
> 
> 

Veja a seguir os detalhes sobre a função desse script bash. 

1. Defina algumas variáveis.
   
   ```bash
   #!/bin/bash
   
   # The path of this script
   SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
   # Charmrun command
   CHARMRUN=${SCRIPT_PATH}/charmrun
   # Argument of ++nodelist
   NODELIST_OPT="++nodelist"
   # Argument of ++p
   NUMPROCESS="+p"
   ```
2. Obtenha informações sobre o nó das variáveis de ambiente. $NODESCORES armazena uma lista de palavras de divisão de $CCP_NODES_CORES. $COUNT é o tamanho de $NODESCORES.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   O formato para a variável $CCP_NODES_CORES é o seguinte:
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Essa variável lista o número total de nós, os nomes dos nós e o número de núcleos em cada nó que estão alocados para o trabalho. Por exemplo, se o trabalho precisar de 10 núcleos para ser executado, o valor de $CCP_NODES_CORES será semelhante a:
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Se a variável $CCP_NODES_CORES não estiver definida, inicie **charmrun** diretamente. (Isso só deve ocorrer quando você executa esse script diretamente nos nós do Linux.)
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. Outra opção é criar um arquivo nodelist para **charmrun**.
   
   ```
   else
     # Create the nodelist file
     NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$
   
     # Write the head line
     echo "group main" > ${NODELIST_PATH}
   
     # Get every node name and number of cores and write into the nodelist file
     I=1
     while [ ${I} -lt ${COUNT} ]
     do
         echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
         let "I=${I}+2"
     done
   ```
5. Execute **charmrun** com o arquivo nodelist, obtenha seu status de retorno e remova o arquivo nodelist ao final.
   
   ${CCP_NUMCPUS} é outra variável de ambiente definida pelo nó principal do HPC Pack. Ela armazena o número total de núcleos alocados para esse trabalho. Usamos essa variável para especificar o número de processos para charmrun.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Saia com o status de retorno de **charmrun** .
   
   ```
   exit ${RTNSTS}
   ```

Veja abaixo as informações no arquivo nodelist, que são geradas pelo script:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Por exemplo:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Enviar um trabalho NAMD
Agora você está pronto para enviar um trabalho do NAMD no Gerenciador de Cluster do HPC.

1. Conecte-se ao nó principal do cluster e inicie o Gerenciador de Cluster do HPC.
2. Em **Gerenciamento de Recursos**, verifique se os nós de computação do Linux estão no estado **Online**. Se não estiverem, selecione-os e clique em **Colocar Online**.
3. Em **Gerenciamento de Trabalhos**, clique em **Novo Trabalho**.
4. Insira um nome para o trabalho como *hpccharmrun*.
   
   ![Novo trabalho do HPC][namd_job]
5. Na página **Detalhes do Trabalho** em **Recursos de Trabalho**, selecione o tipo de recurso como **Nó** e defina o **Mínimo** para 3. , executamos o trabalho em três nós do Linux e cada nó tem quatro núcleos.
   
   ![Recursos de trabalho][job_resources]
6. Clique em **Editar Tarefas** na navegação esquerda e clique em **Adicionar** para adicionar uma tarefa ao trabalho.    
7. Na página **Detalhes da Tarefa e Redirecionamento de E/S**, defina os valores a seguir:
   
   * **Linha de comando** -
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > A linha de comando anterior é um único comando sem quebras de linha. Ele é encapsulado para aparecer em várias linhas em **Linha de comando**.
     > 
     > 
   * **Diretório de trabalho** - /namd2
   * **Mínimo** - 3
     
     ![Detalhes de tarefa][task_details]
     
     > [!NOTE]
     > Defina o diretório de trabalho aqui, pois **charmrun** tentará navegar até o mesmo diretório de trabalho em cada nó. Se o diretório de trabalho não for definido, o HPC Pack iniciará o comando em uma pasta nomeada aleatoriamente, criada em um dos nós do Linux. Isso causa o seguinte erro nos outros nós: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Para evitar esse problema, especifique um caminho de pasta que possa ser acessado por todos os nós como o diretório de trabalho.
     > 
     > 
8. Clique em **OK** e depois em **Enviar** para executar este trabalho.
   
   Por padrão, o HPC Pack envia o trabalho como a sua atual conta de usuário conectado. Uma caixa de diálogo pode solicitar que você insira o nome de usuário e a senha depois de clicar em **Enviar**.
   
   ![Credenciais de trabalho][creds]
   
   Em algumas condições, o HPC Pack memoriza as informações do usuário inseridas anteriormente e não mostra esta caixa de diálogo. Para fazer o HPC Pack mostrá-la novamente, digite o seguinte comando em um Prompt de Comando e, então, envie o trabalho.
   
   ```command
   hpccred delcreds
   ```
9. O trabalho leva vários minutos para ser concluído.
10. Encontre o log do trabalho em \\<headnodeName>\Namd\namd2\namd2_hpccharmrun.log e os arquivos de saída em \\<headnodeName>\Namd\namd2\namdsample\1-2-sphere\.
11. Opcionalmente, inicie o VMD para exibir os resultados do trabalho. As etapas para visualizar os arquivos de saída do NAMD (neste caso, uma molécula da proteína ubiquitina em uma esfera de água) estão além do escopo deste artigo. Veja [Tutorial do NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) para obter mais detalhes.
    
    ![Resultados de trabalho][vmd_view]

## <a name="sample-files"></a>Arquivos de exemplo
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Exemplo de arquivo de configuração XML para implantação de cluster pelo script do PowerShell
```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
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

### <a name="sample-hpccharmrunsh-script"></a>Exemplo de script hpccharmrun.sh
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png
