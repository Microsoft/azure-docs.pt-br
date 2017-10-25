---
title: "VMs de computação do Linux em um cluster de HPC Pack | Microsoft Docs"
description: "Saiba como criar e usar um cluster HPC Pack em cargas de trabalho HPC (computação de alto desempenho) no Azure para Linux"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 4d080fdd-5ffe-4f54-a78d-4c818f6eb3fb
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/12/2016
ms.author: danlep
ms.openlocfilehash: 809d3944311badf265117d353b65642e044d900c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Introdução a nós de computação Linux em um cluster de HPC Pack no Azure
Configure um cluster do [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) no Azure que contenha um nó de cabeçalho que executa o Windows Server e vários nós de computação que executam uma distribuição do Linux com suporte. Explore as opções para mover dados entre nós Linux e o nó principal do Windows do cluster. Saiba como enviar trabalhos do HPC Linux para o cluster.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Em um alto nível, o diagrama a seguir mostra o cluster HPC Pack que você criará e com que trabalhará.

![Cluster HPC Pack com nós Linux][scenario]

Para obter outras opções para executar cargas de trabalho HPC Linux no Azure, consulte [Recursos técnicos para computação em lote e de alto desempenho](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Implantar um cluster de HPC Pack com nós de computação Linux
Este artigo mostra duas opções para implantar um cluster do HPC Pack no Azure com nós de computação Linux . Os dois métodos usam uma imagem do Marketplace do Windows Server com HPC Pack para criar o nó principal. 

* **Modelo do Azure Resource Manager** – Use um modelo do Azure Marketplace ou um modelo de início rápido da comunidade para automatizar a criação do cluster no modelo de implantação do Resource Manager. Por exemplo, o modelo de [Cluster do HPC Pack para cargas de trabalho do Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) no Azure Marketplace cria uma infraestrutura completa de cluster de HPC Pack para cargas de trabalho do HPC no Linux.
* **Script do PowerShell** - [use o script de implantação de IaaS do Microsoft HPC Pack](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New-HpcIaaSCluster.ps1**) para automatizar uma implantação de cluster completa no modelo de implantação clássico. Esse script do Azure PowerShell usa uma imagem de VM do HPC Pack no Azure Marketplace para implantação rápida e fornece um conjunto abrangente de parâmetros de configuração para implantar nós de computação do Linux.

Para obter mais informações sobre as opções de implantação de cluster do HPC Pack, consulte [Opções para criar e gerenciar um cluster de HPC (computação de alto desempenho ) no Azure com o Microsoft HPC Pack](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Pré-requisitos
* **Assinatura do Azure** : você pode usar a assinatura no serviço Azure Global ou no Azure China. Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.
* **Cota para núcleos** – talvez seja necessário aumentar a cota de núcleos, especialmente se você optar por implantar vários nós de cluster com tamanhos de VM de vários núcleos. Para aumentar a cota, abra uma solicitação de atendimento ao cliente online gratuitamente.
* **Distribuições Linux** -atualmente, o HPC Pack dá suporte às distribuições Linux a seguir para nós de computação. Você pode usar as versões do Marketplace dessas distribuições quando disponíveis ou fornecer as suas próprias.
  
  * **Com base em centOS**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, HPC 6.5, HPC 7.1
  * **Red Hat Enterprise Linux**: 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12, SLES 12 (Premium), SLES 12 SP1, SLES 12 SP1 (Premium), SLES 12 para HPC, SLES 12 para HPC (Premium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Para usar a rede RDMA do Azure com um dos tamanhos de VM compatíveis com RDMA, especifique uma imagem de HPC com base em CentOS ou de HPC do SUSE Linux Enterprise Server 12 no Azure Marketplace. Para obter mais informações, consulte [Tamanhos de VM de computação de alto desempenho](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Pré-requisitos adicionais para implantar o cluster usando o script de implantação de IaaS do HPC Pack:

* **Computador cliente** – você precisa de um computador cliente com Windows para executar o script de implantação do cluster.
* **Azure PowerShell** - [Instale e configure o Azure PowerShell](/powershell/azure/overview) (versão 0.8.10 ou posterior) no computador cliente.
* **Script de implantação de IaaS do HPC Pack** – baixe e descompacte a versão mais recente do script no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `.\New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.4.1 ou posterior do script.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Opção de implantação 1. Use um modelo do Resource Manager
1. Vá para o modelo [cluster HPC Pack para cargas de trabalho do Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) no Azure Marketplace e clique em **Implantar**.
2. No portal do Azure, examine as informações e clique em **Criar**.
   
    ![Criação de portal][portal]
3. Na folha **Noções básicas** , insira um nome para o cluster, que também será o nome da VM do nó principal. Você pode escolher um grupo de recursos existente ou criar um novo grupo de recursos para a implantação em um local que está disponível para você. O local afeta a disponibilidade de determinados tamanhos de VM e outros serviços do Azure (consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/)).
4. Na folha **Configurações do nó de cabeçalho** , para uma primeira implantação, geralmente você aceitará as configurações padrão. 
   
   > [!NOTE]
   > A **URL do script pós-configuração** é uma configuração opcional para especificar um script do Windows PowerShell publicamente disponível que você deseja executar na VM do nó principal depois que ela estiver em execução. 
   > 
   > 
5. Na folha **Configurações de nós de computação** , selecione um padrão de nomenclatura para os nós, o número e o tamanho dos nós e a distribuição Linux a implantar.
6. Na folha **Configurações de infraestrutura** , insira nomes para a rede virtual e o domínio do Active Directory, para as credenciais de administrador do domínio e da VM e um padrão de nomenclatura para as contas de armazenamento.
   
   > [!NOTE]
   > O HPC Pack usa o domínio do Active Directory para autenticar usuários de cluster. 
   > 
   > 
7. Depois de executar os testes de validação e examinar os termos de uso, clique em **Comprar**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Opção de implantação 2. Usar o script de implantação de IaaS do HPC Pack
A seguir estão pré-requisitos adicionais para implantar o cluster usando o script de implantação de IaaS do HPC Pack:

* **Computador cliente** – você precisa de um computador cliente com Windows para executar o script de implantação do cluster.
* **Azure PowerShell** - [Instale e configure o Azure PowerShell](/powershell/azure/overview) (versão 0.8.10 ou posterior) no computador cliente.
* **Script de implantação de IaaS do HPC Pack** – baixe e descompacte a versão mais recente do script no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `.\New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.4.1 ou posterior do script.

**Arquivo de configuração XML**

O script de implantação do HPC Pack de IaaS usa um arquivo de configuração XML como entrada para descrever o cluster do HPC. O arquivo de configuração de exemplo a seguir especifica um pequeno cluster, que consiste em um nó principal do HPC Pack e em dois nós de computação do Linux CentOS 7.0 de tamanho A7. 

Modifique o arquivo como necessário para seu ambiente e a configuração de cluster desejada e salve-o com um nome como HPCDemoConfig.xml. Por exemplo, você precisa fornecer seu nome de assinatura e um nome de conta de armazenamento exclusivo e o nome do serviço de nuvem. Além disso, convém escolher uma imagem diferente do Linux com suporte para os nós de computação. Para saber mais sobre os elementos no arquivo de configuração, veja o arquivo Manual.rtf na pasta scripts e [Criar um cluster HPC com o script de implantação de IaaS do HPC Pack](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>ExtraLarge</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

**Para executar o script de implantação de IaaS do HPC Pack**

1. Abra o console do Windows PowerShell no computador cliente como administrador.
2. Altere o diretório para a pasta onde os scripts estão instalados (E:\IaaSClusterScript neste exemplo).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
3. Execute o comando a seguir para implantar o cluster HPC Pack. Este exemplo supõe que o arquivo de configuração esteja localizado em E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Como a **AdminPassword** não é especificada no comando anterior, você deve inserir a senha para o usuário *MyAdminName*.
   
    b. Em seguida, o script começa a validar o arquivo de configuração. Pode demorar dezenas de segundos a vários minutos dependendo da conexão de rede.
   
    ![Validação][validate]
   
    c. Depois de passarem as validações, o script lista os recursos de cluster a serem criados. Digite *y* para continuar.
   
    ![Recursos][resources]
   
    d. O script começa a implantar o cluster de HPC Pack e concluirá a configuração sem outras etapas manuais. O script pode ser executado por vários minutos.
   
    ![Implantar][deploy]
   
   > [!NOTE]
   > Neste exemplo, o script gera um arquivo de log automaticamente, pois o parâmetro **-LogFile** não está especificado. Os logs não são gravados em tempo real, mas são coletados no final da validação e na implantação. Se o processo do PowerShell for interrompido durante a execução do script, alguns logs serão perdidos.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Conectar-se ao nó principal
Depois de implantar o cluster do HPC Pack no Azure, [conecte-se por meio da Área de Trabalho Remota](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à VM do nó principal usando o domínio credenciais fornecidas quando você implantou o cluster (por exemplo, *hpc\\clusteradmin*). Você gerencia o cluster do nó principal.

No nó principal, inicie o Gerenciador de Cluster HPC para verificar o status do cluster HPC Pack. Você pode gerenciar e monitorar os nós de computação Linux da mesma maneira que faz trabalha com os nós de computação do Windows. Por exemplo, você verá os nós Linux listados no**Resource Management** (esses nós são implantados com o modelo **LinuxNode**).

![Gerenciamento de nós][management]

Você também verá os nós Linux na exibição do **Mapa de Calor** .

![Mapa de Calor][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Como mover dados em um cluster com nós do Linux
Você tem várias opções para mover dados entre nós Linux e o nó principal do Windows do cluster. Aqui estão três métodos comuns, descritos mais detalhadamente nas seções a seguir:

* **Arquivo do Azure** – expõe um compartilhamento de arquivos SMB gerenciado para armazenar arquivos de dados no armazenamento do Azure. Os nós do Windows e do Linux poderão montar um compartilhamento de Arquivos do Azure como uma unidade ou pasta ao mesmo tempo, mesmo se eles estiverem implantados em diferentes redes virtuais.
* **Compartilhamento do nó de cabeçalho SMB** – monta uma pasta compartilhada padrão do Windows do nó de cabeçalho em nós do Linux.
* **Servidor NFS do nó de cabeçalho** – fornece uma solução de compartilhamento de arquivos para um ambiente misto do Windows e do Linux.

### <a name="azure-file-storage"></a>Armazenamento de arquivos do Azure
O serviço [Arquivo do Azure](https://azure.microsoft.com/services/storage/files/) expõe os compartilhamentos de arquivos usando o protocolo SMB 2.1 padrão. As VMs e os serviços de nuvem podem compartilhar dados de arquivos entre componentes de aplicativos por meio de compartilhamentos montados, e aplicativos locais podem acessar dados de arquivos em um compartilhamento por meio da API de armazenamento de arquivos. 

Para obter etapas detalhadas para criar um compartilhamento de Arquivos do Azure e montá-lo no nó principal, veja [Introdução ao armazenamento de Arquivos do Azure no Windows](../../../storage/files/storage-how-to-use-files-windows.md). Para montar o compartilhamento de Arquivos do Azure em nós do Linux, consulte [Como usar o armazenamento de Arquivos do Azure com Linux](../../../storage/files/storage-how-to-use-files-linux.md). Para configurar as conexões persistentes, confira [Persisting connections to Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

No exemplo a seguir, crie um compartilhamento de arquivos do Azure em uma conta de armazenamento. Para montar o compartilhamento no nó de cabeçalho, abra um Prompt de Comando e insira os seguintes comandos:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

Neste exemplo, allvhdsje é o nome da conta de armazenamento, storageaccountkey é a chave de conta de armazenamento e o rdma é o nome do compartilhamento de arquivos do Azure. O compartilhamento de Arquivos do Azure é montado como Z: no nó principal.

Para montar o compartilhamento de Arquivo do Azure em nós do Linux, execute um comando **clusrun** no nó de cabeçalho. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** é uma ferramenta útil do HPC Pack para executar tarefas administrativas em vários nós. (Consulte também [Clusrun para nós Linux](#Clusrun-for-Linux-nodes) neste artigo.)

Abra uma janela do Windows PowerShell e digite os seguintes comandos:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

O primeiro comando cria uma pasta chamada /rdma em todos os nós do grupo LinuxNodes. O segundo comando monta a pasta allvhdsjw.file.core.windows.net/rdma de compartilhamento de Arquivos do Azure para a pasta /rdma com dir e bits de modo de arquivo definido como 777. No segundo comando, allvhdsje é o nome da sua conta de armazenamento e storageaccountkey é a chave da conta de armazenamento.

> [!NOTE]
> O símbolo "\`" no segundo comando é um símbolo de escape para o PowerShell. "\`," significa que "," (uma vírgula) é uma parte do comando.
> 
> 

### <a name="head-node-share"></a>Compartilhamento do nó principal
Como alternativa, monte uma pasta compartilhada do nó de cabeçalho em nós do Linux. Um compartilhamento é a maneira mais simples de compartilhar arquivos, mas o nó principal e todos os nós do Linux precisam ser implantados na mesma rede virtual. Siga estas etapas:

1. Crie uma pasta no nó principal e compartilhe-o com Todos com permissões de Leitura/Gravação. Por exemplo, compartilhe D:\OpenFOAM no nó de cabeçalho como \\CentOS7RDMA-HN\OpenFOAM. Nesse caso, o CentOS7RDMA-HN é o nome do host do nó de cabeçalho.
   
    ![Permissões de compartilhamento de arquivo][fileshareperms]
   
    ![Compartilhamento de arquivos][filesharing]
2. Abra uma janela do Windows PowerShell e execute os seguintes comandos:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

O primeiro comando cria uma pasta chamada /openfoam em todos os nós no grupo LinuxNodes. O segundo comando monta a pasta compartilhada //CentOS7RDMA-HN/OpenFOAM para a pasta com dir e bits de modo de arquivo definido como 777. O nome de usuário e a senha no comando devem ser o nome de usuário e a senha de um usuário de cluster no nó principal. (Confira [Add or remove cluster users](https://technet.microsoft.com/library/ff919330.aspx)).

> [!NOTE]
> O símbolo "\`" no segundo comando é um símbolo de escape para o PowerShell. "\`," significa que "," (uma vírgula) é uma parte do comando.
> 
> 

### <a name="nfs-server"></a>Servidor NFS
O serviço NFS permite que você compartilhe e migre arquivos entre computadores com o sistema operacional Windows Server 2012 usando o protocolo SMB e computadores Linux usando o protocolo NFS. O servidor NFS e todos os outros nós devem ser implantados na mesma rede virtual. Ele fornece a melhor compatibilidade com nós do Linux em comparação com um compartilhamento SMB. Por exemplo, ele dá suporte a links de arquivo.

1. Para instalar e configurar um servidor NFS, siga as etapas no [Servidor para primeiro compartilhamento do sistema de arquivos de rede ponta a ponta](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Por exemplo, crie um compartilhamento NFS chamado nfs com as seguintes propriedades:
   
    ![Autorização de NFS][nfsauth]
   
    ![Permissões de compartilhamento NFS][nfsshare]
   
    ![Permissões de compartilhamento NFS NTFS][nfsperm]
   
    ![Propriedades de gerenciamento de NFS][nfsmanage]
2. Abra uma janela do Windows PowerShell e execute os seguintes comandos:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   O primeiro comando cria uma pasta chamada /nfsshared em todos os nós do grupo LinuxNodes. O segundo comando monta o compartilhamento CentOS7RDMA-HN:/nfs para a pasta. Aqui, CentOS7RDMA-HN:/nfss é o caminho remoto do seu compartilhamento de NFS.

## <a name="how-to-submit-jobs"></a>Como enviar trabalhos
Há várias maneiras de enviar trabalhos ao cluster HPC Pack:

* Gerenciador de Cluster HPC ou GUI do Gerenciador de Trabalhos HPC
* Portal da Web do HPC
* API REST

O envio de trabalho para o cluster no Azure por meio de ferramentas de GUI do HPC Pack e o portal da Web do HPC são os mesmas para nós de computação do Windows. Veja [Gerenciador de trabalhos do HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) e [Como enviar trabalhos de um computador cliente local](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para enviar trabalhos por meio da API REST, confira [Creating and Submitting Jobs by Using the REST API in Microsoft HPC Pack (Windows HPC Server)](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Para enviar trabalhos de um cliente Linux, consulte também o exemplo do Python no [SDK do HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Clusrun para nós Linux
A ferramenta [clusrun](https://technet.microsoft.com/library/cc947685.aspx) do HPC Pack pode ser usada para executar comandos em nós do Linux por meio de um Prompt de comando ou do Gerenciador de Cluster do HPC. Veja alguns exemplos básicos.

* Mostre os nomes de usuário atuais em todos os nós no cluster.
  
    ```command
    clusrun whoami
    ```
* Instale a ferramenta de depuração **gdb** com o **yum** em todos os nós do grupo linuxnodes e os reinicie após 10 minutos.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Crie um script de shell exibindo cada número de 1 a 10 durante um segundo em cada nó no cluster, execute-o e mostre imediatamente a saída dos nós.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Talvez seja necessário usar determinados caracteres de escape em comandos **clusrun** . Como mostra este exemplo, use ^ em um Prompt de Comando para escapar o símbolo ">".
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Tente dimensionar verticalmente o cluster para um maior número de nós ou executar uma carga de trabalho do Linux no cluster. Para ver um exemplo, confira [Executar o NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure](hpcpack-cluster-namd.md).
* Tente um cluster com [VMs compatíveis com RDMA, com uso intensivo de computação](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para executar cargas de trabalho MPI. Para ver um exemplo, confira [Executar o OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](hpcpack-cluster-openfoam.md).
* Se você estiver interessado em trabalhar em nós do Linux em um cluster do HPC Pack local, veja as [diretrizes do TechNet](https://technet.microsoft.com/library/mt595803.aspx).

<!--Image references-->
[scenario]:media/hpcpack-cluster/scenario.png
[portal]:media/hpcpack-cluster/portal.png
[validate]:media/hpcpack-cluster/validate.png
[resources]:media/hpcpack-cluster/resources.png
[deploy]:media/hpcpack-cluster/deploy.png
[management]:media/hpcpack-cluster/management.png
[heatmap]:media/hpcpack-cluster/heatmap.png
[fileshareperms]:media/hpcpack-cluster/fileshare1.png
[filesharing]:media/hpcpack-cluster/fileshare2.png
[nfsauth]:media/hpcpack-cluster/nfsauth.png
[nfsshare]:media/hpcpack-cluster/nfsshare.png
[nfsperm]:media/hpcpack-cluster/nfsperm.png
[nfsmanage]:media/hpcpack-cluster/nfsmanage.png
