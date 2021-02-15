---
title: Solução VMware do Azure – migração usando o Azure Data Box
description: Como usar Azure Data Box para migrar dados em massa para a solução do Azure VMware.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86507651"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrando dados para a solução do Azure VMware usando Azure Data Box

A solução Microsoft Azure Data Box Cloud permite que você envie terabytes (TBs) de dados para o Azure de maneira rápida, barata e confiável. A transferência de dados segura é acelerada pelo envio de um dispositivo de armazenamento Data Box proprietário. Cada dispositivo de armazenamento tem uma capacidade de armazenamento utilizável máxima de 80 TB e é transportado para o datacenter por uma operadora regional. O dispositivo tem uma caixa reforçada para proteger os dados durante o transporte.

Usando Data Box, você pode migrar seus dados do VMware em massa para sua nuvem privada. Os dados do ambiente de VMware vSphere local são copiados para Data Box por meio do protocolo NFS (sistema de arquivos de rede). A migração de dados em massa envolve salvar uma cópia pontual de máquinas virtuais, configuração e dados associados para Data Box e, em seguida, enviá-los manualmente para o Azure.

Neste artigo, você aprenderá sobre:

* Configurando Data Box.
* Copiar dados do ambiente VMware local para o Data Box por meio de NFS.
* Preparando para o retorno de Data Box.
* Preparando dados de BLOB para copiar para a solução do Azure VMware.
* Copiar os dados do Azure para sua nuvem privada.

## <a name="scenarios"></a>Cenários

Use Data Box nos seguintes cenários para migração de dados em massa:

* Para migrar uma grande quantidade de dados do local para a solução do Azure VMware. Esse método estabelece uma linha de base e sincroniza as diferenças na rede.
* Para migrar um grande número de máquinas virtuais desativadas (máquinas virtuais frias).
* Para migrar dados de máquina virtual para configurar ambientes de desenvolvimento e teste.
* Para migrar um grande número de modelos de máquina virtual, arquivos ISO e discos de máquina virtual.

## <a name="before-you-begin"></a>Antes de começar

* Verifique os pré-requisitos e [solicite data Box](../databox/data-box-deploy-ordered.md) por meio de seu portal do Azure. Durante o processo de pedido, você deve selecionar uma conta de armazenamento que habilite o armazenamento de BLOBs. Depois de receber o dispositivo Data Box, conecte-o à sua rede local e [Configure o dispositivo](../databox/data-box-deploy-set-up.md) com um endereço IP acessível pela rede de gerenciamento do vSphere.

* Crie uma rede virtual e uma conta de armazenamento na mesma região em que sua solução do Azure VMware é provisionada.

* Crie uma [conexão de rede virtual do Azure](cloudsimple-azure-network-connection.md) de sua nuvem privada para a rede virtual na qual a conta de armazenamento é criada seguindo as etapas em [conectar a rede virtual do Azure ao CloudSimple usando o ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Configurar Data Box para NFS

Conecte-se à interface do usuário da Web local do Data Box seguindo as etapas na seção "conectar-se ao seu dispositivo" do [tutorial: cabo e conecte-se ao seu Azure data Box](../databox/data-box-deploy-set-up.md).  Configure Data Box para permitir o acesso a clientes NFS:

1. Na interface do usuário da Web local, vá para a página **conectar e copiar** . Em **configurações de NFS**, selecione **acesso de cliente NFS**. 

    ![Configurar o acesso de cliente NFS 1](media/nfs-client-access.png)

2. Insira o endereço IP dos hosts VMware ESXi e selecione **Adicionar**. Você pode configurar o acesso para todos os hosts no cluster vSphere repetindo esta etapa. Selecione **OK**.

    ![Configurar o acesso do cliente NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Sempre crie uma pasta para os arquivos que você pretende copiar no compartilhamento e, em seguida, copie os arquivos para a pasta**. A pasta criada nos compartilhamentos de blob de blocos e de blob de páginas representa um contêiner no qual os dados são carregados como blobs. Não é possível copiar arquivos diretamente para a pasta *raiz* na conta de armazenamento.

Em compartilhamentos de blob de blocos e blob de páginas, as entidades de primeiro nível são contêineres e as entidades de segundo nível são blobs. Em compartilhamentos para arquivos do Azure, as entidades de primeiro nível são compartilhamentos e as entidades de segundo nível são arquivos.

A tabela a seguir mostra o caminho UNC para os compartilhamentos na URL de caminho do Data Box e do Armazenamento do Azure em que os dados são carregados. A URL final de caminho do Armazenamento do Azure pode ser derivada do caminho de compartilhamento UNC.
 
| Blobs e arquivos | Caminho e URL |
|---------------- | ------------ |
| Blobs de blocos do Azure | <li>Caminho UNC para compartilhamentos: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Blobs de páginas do Azure  | <li>Caminho UNC para compartilhamentos: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Arquivos do Azure       |<li>Caminho UNC para compartilhamentos: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL do Armazenamento do Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Use blobs de blocos do Azure para copiar dados do VMware.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Monte o compartilhamento NFS como um armazenamento de dados em seu cluster do vCenter local e copie-os

O compartilhamento NFS do seu Data Box deve ser montado como um armazenamento de dados em seu cluster do vCenter local ou host de VMware ESXi para que você possa copiá-los para o armazenamento NFS:

1. Faça logon em seu servidor do vCenter local.

2. Clique com o botão direito do mouse em **datacenter**, selecione **armazenamento**, **novo repositório de armazenamento**e, em seguida, selecione **Avançar**.

   ![Adicionar novo repositório de armazenamento](media/databox-migration-add-datastore.png)

3. Na etapa 1 do assistente para adicionar repositório de armazenamento, selecione **NFS** em **tipo**.

   ![Adicionar novo repositório de armazenamento-tipo](media/databox-migration-add-datastore-type.png)

4. Na etapa 2 do assistente, selecione **NFS 3** como a versão do NFS e, em seguida, selecione **Avançar**.

   ![Adicionar novo repositório de armazenamento-versão do NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Na etapa 3 do assistente, especifique o nome do repositório de armazenamento, o caminho e o servidor. Você pode usar o endereço IP do seu Data Box para o servidor. O caminho da pasta estará no `/<StorageAccountName_BlockBlob>/<ContainerName>/` formato.

   ![Adicionar novo repositório de armazenamento-configuração de NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Na etapa 4 do assistente, selecione os hosts ESXi onde você deseja que o repositório de armazenamento seja montado e, em seguida, selecione **Avançar**.  Em um cluster, selecione todos os hosts para garantir a migração das máquinas virtuais.

   ![Adicionar novo repositório de armazenamento-selecionar hosts](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Na etapa 5 do assistente, examine o resumo e selecione **concluir**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Copiar dados para o Data Box armazenamento do NFS

As máquinas virtuais podem ser migradas ou clonadas para o novo repositório de armazenamento.  As máquinas virtuais não utilizadas que você deseja migrar podem ser migradas para o armazenamento de Data Box NFS usando a opção **Storage VMotion** . As máquinas virtuais ativas podem ser clonadas para o repositório de armazenamento do Data Box NFS.

* Identifique e liste as máquinas virtuais que podem ser **movidas**.
* Identifique e liste as máquinas virtuais que devem ser **clonadas**.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Mover uma máquina virtual para um repositório de Data Box de armazenamento

1. Clique com o botão direito do mouse na máquina virtual que você deseja mover para o repositório de Data Box de armazenamento e, em seguida, selecione **migrar**.

    ![Migrar máquina virtual](media/databox-migration-vm-migrate.png)

2. Selecione **alterar armazenamento somente** para o tipo de migração e, em seguida, selecione **Avançar**.

    ![Migrar máquina virtual-somente armazenamento](media/databox-migration-vm-migrate-change-storage.png)

3. Selecione **Data Box-datastore** como o destino e, em seguida, selecione **Avançar**.

    ![Migrar máquina virtual-selecionar repositório de armazenamento](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Examine as informações e selecione **concluir**.

5. Repita as etapas de 1 a 4 para máquinas virtuais adicionais.

> [!TIP]
> Você pode selecionar várias máquinas virtuais que estão no mesmo estado de energia (ativadas ou desativadas) e migrá-las em massa.

A máquina virtual será migrada para o repositório de armazenamento NFS de Data Box. Depois que todas as máquinas virtuais forem migradas, você poderá desligar (desligar) as máquinas virtuais ativas na preparação para a migração de dados para a solução Azure VMware.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Clonar uma máquina virtual ou um modelo de máquina virtual para o repositório de armazenamento Data Box

1. Clique com o botão direito do mouse em uma máquina virtual ou em um modelo de máquina virtual que você deseja clonar. Selecione **clonar**  >  **clonar para máquina virtual**.

    ![Clone da máquina virtual](media/databox-migration-vm-clone.png)

2. Selecione um nome para a máquina virtual clonada ou o modelo de máquina virtual.

3. Selecione a pasta onde você deseja colocar o objeto clonado e, em seguida, selecione **Avançar**.

4. Selecione o cluster ou o pool de recursos em que você deseja colocar o objeto clonado e, em seguida, selecione **Avançar**.

5. Selecione **Data Box-datastore** como o local de armazenamento e, em seguida, selecione **Avançar**.

    ![Clonagem de máquina virtual – selecionar repositório de armazenamento](media/databox-migration-vm-clone-select-datastore.png)

6. Se você quiser personalizar qualquer opção para o objeto clonado, selecione as opções de personalização e, em seguida, selecione **Avançar**.

7. Examine as configurações e selecione **concluir**.

8. Repita as etapas de 1 a 7 para máquinas virtuais ou modelos de máquina virtual adicionais.

As máquinas virtuais serão clonadas e armazenadas no repositório de armazenamento NFS de Data Box. Depois que as máquinas virtuais forem clonadas, verifique se elas estão desligadas na preparação para a migração de dados para a solução VMware do Azure.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Copiar arquivos ISO para o repositório de armazenamento de Data Box

1. Na interface do usuário da Web do vCenter local, vá para **armazenamento**.  Selecione **Data Box-datastore** e, em seguida, selecione **arquivos**. Crie uma nova pasta para armazenar arquivos ISO.

    ![Copiar ISO-criar nova pasta](media/databox-migration-create-folder.png)

2. Forneça um nome para a pasta em que os arquivos ISO serão armazenados.

3. Clique duas vezes na pasta recém-criada para abri-la.

4. Selecione **carregar arquivos** e, em seguida, selecione os arquivos ISO que você deseja carregar.
    
    ![Copiar arquivos de upload ISO](media/databox-migration-upload-iso.png)

> [!TIP]
> Se você já tiver arquivos ISO em seu repositório de armazenamento local, poderá selecionar os arquivos e **copiar para** para copiar os arquivos para o repositório de armazenamento do data Box NFS.


## <a name="prepare-data-box-for-return"></a>Preparar Data Box para retorno

Depois que todos os dados da máquina virtual, os dados de modelo de máquina virtual e todos os arquivos ISO forem copiados para o Data Box armazenamento NFS, você poderá desconectar o armazenamento de dados do seu vCenter. Todas as máquinas virtuais e modelos de máquina virtual devem ser removidos do inventário antes de você desconectar o repositório de armazenamento.

### <a name="remove-objects-from-inventory"></a>Remover objetos do inventário

1. Na interface do usuário da Web do vCenter local, vá para **armazenamento**. Selecione **Data Box-datastore** e, em seguida, selecione **VMs**.

    ![Remover máquinas virtuais do inventário-desativado](media/databox-migration-select-databox-vm.png)

2. Verifique se todas as máquinas virtuais estão desligadas.

3. Selecione todas as máquinas virtuais, clique com o botão direito do mouse e selecione **remover do inventário**.

    ![Remover máquinas virtuais do inventário](media/databox-migration-remove-vm-from-inventory.png)

4. Selecione **modelos de VM em pastas** e repita a etapa 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Remover o repositório de armazenamento do Data Box NFS do vCenter

O repositório de armazenamento do Data Box NFS deve ser desconectado de hosts VMware ESXi antes de se preparar para o retorno.

1. Na interface do usuário da Web do vCenter local, vá para **armazenamento**.

2. Clique com o botão direito do mouse em **Data Box-datastore** e selecione **desmontar repositório de armazenamento**.

    ![Desmontar Data Box repositório de armazenamento](media/databox-migration-unmount-datastore.png)

3. Selecione todos os hosts ESXi nos quais o repositório de armazenamento está montado e selecione **OK**.

    ![Desmontar Data Box repositório de armazenamento-selecionar hosts](media/databox-migration-unmount-datastore-select-hosts.png)

4. Examine e aceite todos os avisos e selecione **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Preparar Data Box para retornar e, em seguida, retorná-lo

Siga as etapas descritas no artigo [retornar Azure data box e verificar o carregamento de dados no Azure](../databox/data-box-deploy-picked-up.md) para retornar o data box. Verifique o status da cópia de dados para sua conta de armazenamento do Azure. Depois que o status for exibido como concluído, você poderá verificar os dados em sua conta de armazenamento do Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Copiar dados do armazenamento do Azure para a solução VMware do Azure

Os dados copiados para o dispositivo de Data Box estarão disponíveis em sua conta de armazenamento do Azure depois que o status do pedido de seu Data Box for mostrado como concluído. Os dados agora podem ser copiados para sua solução Azure VMware. Os dados na conta de armazenamento devem ser copiados para o armazenamento da vSAN na sua nuvem privada usando o protocolo NFS. 

Primeiro, copie os dados do armazenamento de BLOBs para um disco gerenciado em uma máquina virtual Linux no Azure usando o **AzCopy**. Disponibilize o disco gerenciado por meio de NFS, monte o compartilhamento NFS como um armazenamento de dados em sua nuvem privada e copie os dados. Esse método permite uma cópia mais rápida dos dados para sua nuvem privada.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Copiar dados para sua nuvem privada usando uma máquina virtual Linux e Managed disks e, em seguida, exportar como compartilhamento NFS

1. Crie uma [máquina virtual do Linux](../virtual-machines/linux/quick-create-portal.md) no Azure na mesma região em que sua conta de armazenamento é criada e tenha uma conexão de rede virtual do Azure com sua nuvem privada.

2. Crie um disco gerenciado cuja capacidade de armazenamento seja maior que a quantidade de dados de BLOB e [anexe-o à sua máquina virtual Linux](../virtual-machines/linux/attach-disk-portal.md).  Se a quantidade de dados de blob for maior do que a capacidade do maior disco gerenciado disponível, os dados deverão ser copiados em várias etapas ou usando vários discos gerenciados.

3. Conecte-se à máquina virtual Linux e monte o disco gerenciado.

4. Instale [o AzCopy em sua máquina virtual Linux](../storage/common/storage-use-azcopy-v10.md).

5. Baixe os dados do armazenamento de BLOBs do Azure no disco gerenciado usando o AzCopy.  Sintaxe do comando: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  Substitua `<storage-account-name>` pelo nome da conta de armazenamento do Azure e `<container-name>` pelo contêiner que contém os dados copiados por meio de data box.

6. Instale o servidor NFS em sua máquina virtual Linux:

    - Em uma distribuição do Ubuntu/Debian: `sudo apt install nfs-kernel-server` .
    - Em uma distribuição Enterprise Linux: `sudo yum install nfs-utils` .

7. Altere a permissão da pasta no disco gerenciado onde os dados do armazenamento de BLOBs do Azure foram copiados.  Altere as permissões para todas as pastas que você deseja exportar como um compartilhamento NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Atribua permissões para que os endereços IP do cliente acessem o compartilhamento NFS editando o `/etc/exports` arquivo.

    ```bash
    sudo vi /etc/exports
    ```
    
    Insira as seguintes linhas no arquivo para cada IP de host ESXi da sua nuvem privada.  Se você estiver criando compartilhamentos para várias pastas, adicione todas as pastas.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exporte os compartilhamentos NFS usando o `sudo exportfs -a` comando.

10. Reinicie o servidor de kernel NFS usando o `sudo systemctl restart nfs-kernel-server` comando.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Montar o compartilhamento NFS de máquina virtual Linux como um armazenamento de dados em um cluster do vCenter de nuvem privada e copiar

O compartilhamento NFS de sua máquina virtual Linux deve ser montado como um repositório de armazenamento em seu cluster do vCenter de nuvem privada. Depois de montado, os dados podem ser copiados do repositório de armazenamento NFS para o armazenamento de dados vSAN da nuvem privada.

1. Faça logon em seu vCenter Server de nuvem privada.

2. Clique com o botão direito do mouse em **datacenter**, selecione **armazenamento**, **novo repositório de armazenamento**e, em seguida, selecione **Avançar**.

   ![Adicionar novo repositório de armazenamento](media/databox-migration-add-datastore.png)

3. Na etapa 1 do assistente para adicionar repositório de armazenamento, selecione o tipo de **NFS** .

   ![Adicionar novo repositório de armazenamento-tipo](media/databox-migration-add-datastore-type.png)

4. Na etapa 2 do assistente, selecione **NFS 3** como a versão do NFS e, em seguida, selecione **Avançar**.

   ![Adicionar novo repositório de armazenamento-versão do NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Na etapa 3 do assistente, especifique o nome do repositório de armazenamento, o caminho e o servidor.  Você pode usar o endereço IP da sua máquina virtual Linux para o servidor.  O caminho da pasta estará no `/<folder>/<subfolder>/` formato.

   ![Adicionar novo repositório de armazenamento-configuração de NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Na etapa 4 do assistente, selecione os hosts ESXi onde você deseja que o repositório de armazenamento seja montado e, em seguida, selecione **Avançar**.  Em um cluster, selecione todos os hosts para garantir a migração das máquinas virtuais.

   ![Adicionar novo repositório de armazenamento-selecionar hosts](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Na etapa 5 do assistente, examine o resumo e selecione **concluir**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Adicionar máquinas virtuais e modelos de máquina virtual de um repositório de armazenamento NFS ao inventário

1. Em sua interface do usuário da Web da nuvem privada do vCenter, acesse **armazenamento**.  Selecione um repositório de armazenamento NFS de máquina virtual Linux e, em seguida, selecione **arquivos**.

    ![Selecionar arquivos do repositório de armazenamento NFS](media/databox-migration-datastore-select-files.png)

2. Selecione uma pasta que contenha uma máquina virtual ou um modelo de máquina virtual.  No painel de detalhes, selecione um arquivo. vmx para uma máquina virtual ou um arquivo. vmtx para um modelo de máquina virtual.

3. Selecione **registrar VM** para registrar a máquina virtual em sua nuvem privada vCenter.

    ![Registrar máquina virtual](media/databox-migration-datastore-register-vm.png)

4. Selecione o datacenter, a pasta e o pool de recursos/cluster em que você deseja que a máquina virtual seja registrada.

4. Repita as etapas 3 e 4 para todas as máquinas virtuais e modelos de máquina virtual.

5. Vá para a pasta que contém os arquivos ISO.  Selecione os arquivos ISO e, em seguida, selecione **copiar para** para copiar os arquivos para uma pasta no armazenamento de seus vSAN.

As máquinas virtuais e os modelos de máquina virtual agora estão disponíveis em sua nuvem privada vCenter. Essas máquinas virtuais devem ser movidas do repositório de armazenamento NFS para o armazenamento de datavsan antes de ligá-las. Você pode usar a opção **Storage VMotion** e selecionar o armazenamento de datavsan como o destino para as máquinas virtuais.

Os modelos de máquina virtual devem ser clonados do repositório de armazenamento NFS da máquina virtual Linux para o armazenamento de seus vSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Limpar sua máquina virtual Linux

Depois que todos os dados forem copiados para a sua nuvem privada, você poderá remover o armazenamento NFS de sua nuvem privada:

1. Certifique-se de que todas as máquinas virtuais e modelos sejam movidos e clonados para o armazenamento de seus vSAN.

2. Remova do inventário todos os modelos de máquina virtual do repositório de armazenamento NFS.

3. Desmonte o repositório de armazenamento de máquina virtual Linux do vCenter de nuvem privada.

4. Exclua a máquina virtual e o disco gerenciado do Azure.

5. Se você não quiser manter os dados transferidos pelo Data Box em sua conta de armazenamento, exclua a conta de armazenamento do Azure.  
    


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Data Box](../databox/data-box-overview.md).
* Saiba mais sobre as diferentes opções para [migrar cargas de trabalho para sua nuvem privada](migrate-workloads.md).
