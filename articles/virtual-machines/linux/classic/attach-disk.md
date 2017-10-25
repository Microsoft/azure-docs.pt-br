---
title: Anexar um disco a uma VM do Linux no Azure | Microsoft Docs
description: "Saiba como anexar um disco de dados a uma VM do Linux usando o modelo de implantação Clássico e inicializar o disco para que esteja pronto para uso"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4901384d-2a6f-4f46-bba0-337a348b7f87
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 017ba7197e11c2b222082833d5acabb9e542b762
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Como anexar um disco de dados na máquina virtual Linux
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Veja como [anexar um disco de dados usando o modelo de implantação do Resource Manager](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Você pode anexar tanto discos vazios como discos que contenham dados às suas VMs do Azure. Ambos os tipos de discos são arquivos .vhd que residem em uma conta de armazenamento do Azure. Como acontece com a adição de qualquer disco a uma máquina Linux, depois que você anexar o disco, será necessário inicializá-lo e formatá-lo para que ele fique pronto para uso. Este artigo detalha a anexação de discos vazios e de discos que já contenham dados às suas VMs, e também como então inicializar e formatar um novo disco.

> [!NOTE]
> É uma prática recomendada usar um ou mais discos separados para armazenar dados de uma máquina virtual. Quando você cria uma máquina virtual Azure, há um disco do sistema operacional e um disco temporário. **Não use o disco temporário para armazenar dados persistentes.** Como seu nome quer dizer, ele oferece armazenamento apenas temporariamente. Não oferece redundância nem backup porque eles não residem no armazenamento do Azure.
> O disco temporário é normalmente gerenciado pelo agente Linux do Azure e montado automaticamente em **/mnt/resource** (ou **/mnt** nas imagens do Ubuntu). Por outro lado, um disco de dados pode ser chamado pelo kernel Linux como `/dev/sdc`, e você precisará fazer a partição, formatar e montar esse recurso. Consulte o [Guia do Usuário do Agente do Linux do Azure][Agent] para obter detalhes.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Inicializar um novo disco de dados no Linux
1. SSH para sua VM. Para obter mais informações, veja [Como fazer logon em uma Máquina Virtual que executa o Linux][Logon].
2. Em seguida, você precisa localizar o identificador de dispositivo para inicializar o disco de dados. Há duas maneiras de fazer isso:
   
    a) Grep para dispositivos SCSI nos logs, como no comando a seguir:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    Para distribuições Ubuntu recentes, você talvez precise usar `sudo grep SCSI /var/log/syslog` pois o logon em `/var/log/messages` pode estar desabilitado por padrão.
   
    Você pode localizar o identificador do último disco de dados que foi adicionado nas mensagens que são exibidas.
   
    ![Obter as mensagens do disco](./media/attach-disk/scsidisklog.png)
   
    OU
   
    b) Use o comando `lsscsi` para descobrir a ID do dispositivo. O `lsscsi` pode ser instalado pelo `yum install lsscsi` (distribuições baseadas no Red Hat) ou pelo `apt-get install lsscsi` (distribuições baseadas no Debian). Você pode encontrar o disco que está procurando pelo seu *lun* ou **número de unidade lógica**. Por exemplo, o *lun* dos discos que você anexou pode ser facilmente visto por meio do `azure vm disk list <virtual-machine>` como:

    ```azurecli
    azure vm disk list myVM
    ```

    A saída deverá ser semelhante a esta:

    ```azurecli
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                         OS
    data:    ---  --------  --------------------------------  -----
    data:         30        myVM-2645b8030676c8f8.vhd  Linux
    data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
    info:    vm disk list command OK
    ```
   
    Compare esses dados com a saída de `lsscsi` para o mesmo exemplo de máquina virtual:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    O último número na tupla em cada linha é o *lun*. Veja `man lsscsi` para obter mais informações.
3. No prompt, digite o comando a seguir para criar seu dispositivo:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. Quando solicitado, digite **n** para criar uma partição.

    ![Criar dispositivo](./media/attach-disk/fdisknewpartition.png)

5. Quando solicitado, digite **p** para definir a partição como primária. Digite **1** para torná-la a primeira partição e digite enter para aceitar o valor padrão para o cilindro. Em alguns sistemas, ele pode mostrar os valores padrão do primeiro e último setores, em vez do cilindro. Você pode optar por aceitar esses padrões.

    ![Criar partição](./media/attach-disk/fdisknewpartdetails.png)


6. Digite **p** para ver os detalhes sobre o disco que está sendo particionado.

    ![Listar informações de disco](./media/attach-disk/fdiskpartitiondetails.png)


7. Digite **w** para gravar as configurações do disco.

    ![Gravar as alterações de disco](./media/attach-disk/fdiskwritedisk.png)

8. Agora você pode criar o sistema de arquivos na nova partição. Acrescente o número de partição à ID do dispositivo (no `/dev/sdc1`de exemplo a seguir). O exemplo a seguir cria uma partição ext4 em /dev/sdc1:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![Criar sistema de arquivos](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > Sistemas SuSE Linux Enterprise 11 dão suporte apenas a acesso somente leitura para sistemas de arquivos ext4. Para esses sistemas, é recomendável formatar o novo sistema de arquivos como ext3 em vez de ext4.

9. Crie um diretório para montar o novo sistema de arquivos, como a seguir:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Por fim, você pode montar a unidade, da seguinte maneira:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    Agora o disco de dados está pronto para ser usado como **/datadrive**.
   
    ![Criar o diretório e montar o disco](./media/attach-disk/mkdirandmount.png)

11. Adicione a nova unidade ao /etc/fstab:
   
    Para garantir que a unidade seja remontada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo /etc/fstab. Além disso, é altamente recomendável que o UUID (Identificador Universal Exclusivo) seja usado no /etc/fstab para referir-se à unidade em vez de apenas o nome do dispositivo (por exemplo, /dev/sdc1). Usar o UUID evitará que o disco incorreto seja montado em uma determinada localização, se o sistema operacional detectar um erro de disco durante a inicialização, e que os discos de dados restantes sejam atribuídos a essas IDs de dispositivo. Para localizar o UUID da nova unidade, você pode usar o utilitário **blkid** :
   
    ```bash
    sudo -i blkid
    ```
   
    A saída deve ser semelhante ao seguinte exemplo:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > A edição inadequada do arquivo **/etc/fstab** pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

    Em seguida, abra o arquivo **/etc/fstab** em um editor de texto:

    ```bash
    sudo vi /etc/fstab
    ```

    Neste exemplo, usamos o valor UUID para o novo dispositivo **/dev/sdc1** criado nas etapas anteriores e o ponto de montagem **/datadrive**. Adicione a seguinte linha no final do arquivo **/etc/fstab** :

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    Ou, em sistemas baseados em SUSE Linux, talvez você precise usar um formato ligeiramente diferente:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > A opção `nofail` garante que a VM inicie mesmo que o sistema de arquivos esteja corrompido ou que o disco não exista no momento da inicialização. Sem essa opção, você poderá encontrar um comportamento conforme descrito em [Não é possível conectar-se a uma VM Linux via SSH devido a erros no FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Agora você pode testar se o sistema de arquivos está montado corretamente ao desmontar e montar novamente o sistema de arquivos ou seja, usando o ponto de montagem `/datadrive` de exemplo criado nas etapas anteriores:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Se o comando `mount` produzir um erro, verifique se o arquivo /etc/fstab tem a sintaxe correta. Se as partições ou unidades de dados adicionais forem criadas, insira-as separadamente em/etc/fstab também.

    Torne a unidade gravável usando este comando:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Remover subsequentemente um disco de dados sem editar fstab pode fazer com que a VM falhe ao ser inicializada. Se esta for uma ocorrência comum, a maioria das distribuições fornecerá as opções fstab `nofail` e/ou `nobootwait`, que permitirão que o sistema se inicialize mesmo se a montagem do disco falhar no momento da inicialização. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte a TRIM/UNMAP para Linux no Azure
Alguns kernels Linux permitem operações TRIM/UNMAP para descartar os blocos não utilizados no disco. Essas operações são úteis principalmente no Armazenamento Standard, para informar o Azure de que as páginas excluídas não são mais válidas e podem ser descartadas. Descartar páginas poderá representar uma economia de dinheiro se você criar arquivos grandes e, em seguida, excluí-los.

Há duas maneiras de habilitar o suporte a TRIM em sua VM do Linux. Como de costume, consulte sua distribuição para obter a abordagem recomendada:

* Use a opção de montagem `discard` em `/etc/fstab`, por exemplo:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* Em alguns casos a opção `discard` pode afetar o desempenho. Como alternativa, você pode executar o comando `fstrim` manualmente na linha de comando ou adicioná-lo a crontab para ser executado normalmente:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**
  
    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Solucionar problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Próximas etapas
Você pode ler mais sobre como usar sua VM do Linux nos seguintes artigos:

* [Como fazer logon em uma máquina virtual que executa o Linux][Logon]
* [Como desanexar um disco de uma máquina virtual Linux](detach-disk.md)
* [Usando a CLI do Azure com o modelo de implantação Clássico](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Configurar o RAID em uma VM Linux no Azure](../configure-raid.md)
* [Configurar o LVM em uma VM Linux no Azure](../configure-lvm.md)

<!--Link references-->
[Agent]:../agent-user-guide.md
[Logon]:../mac-create-ssh-keys.md
