---
title: Implantação da plataforma de BI do SAP BusinessObjects no Azure para Linux | Microsoft Docs
description: Implantar e configurar a plataforma de BI SAP BusinessObjects no Azure para Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: b94e1f82409da3329eb6d978fa2ae0222928cd97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505929"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Guia de implantação da plataforma SAP BusinessObjects BI para Linux no Azure

Este artigo descreve a estratégia para implantar a plataforma SAP BOBI no Azure para Linux. Neste exemplo, duas máquinas virtuais com SSD Premium Managed Disks como seu diretório de instalação são configuradas. O banco de dados do Azure para MySQL é usado para o banco de dados CMS e Azure NetApp Files para o servidor de repositório de arquivos é compartilhado entre ambos os servidores. O aplicativo Web Java do Tomcat padrão e o aplicativo da plataforma de BI são instalados juntos em ambas as máquinas virtuais. Para balancear a carga da solicitação do usuário, o gateway de aplicativo é usado com recursos de descarregamento de TLS/SSL nativos.

Esse tipo de arquitetura é eficaz para implantação pequena ou ambiente de não produção. Para implantação em larga escala ou de produção, você pode ter hosts separados para o aplicativo Web e também pode ter vários hosts de aplicativos BOBI, permitindo que o servidor processe mais informações.

![Implantação do SAP BOBI no Azure para Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

Neste exemplo, a versão do produto e o layout do sistema de arquivos são usados

- Plataforma SAP BusinessObjects 4,3
- SUSE Linux Enterprise Server 12 SP5
- Banco de dados do Azure para MySQL (versão: 8.0.15)
- Conector da API do MySQL C-libmysqlclient (versão: 6.1.11)

| Sistema de Arquivos        | Descrição                                                                                                               | Tamanho (GB)             | Proprietário  | Agrupar  | Armazenamento                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | O sistema de arquivos para instalação da instância do SAP BOBI, aplicativo Web Tomcat padrão e drivers de banco de dados (se necessário) | Diretrizes de dimensionamento do SAP | bl1adm | sapsys | Disco Premium gerenciado-SSD |
| /usr/sap/frsinput  | O diretório de montagem é para os arquivos compartilhados em todos os hosts BOBI que serão usados como diretório de repositório do arquivo de entrada  | Necessidade comercial         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | O diretório de montagem é para os arquivos compartilhados em todos os hosts BOBI que serão usados como diretório de repositório do arquivo de saída | Necessidade comercial         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Implantar a máquina virtual do Linux via portal do Azure

Nesta seção, criaremos duas VMs (máquinas virtuais) com a imagem do sistema operacional Linux (SO) para a plataforma SAP BOBI. As etapas de alto nível para criar máquinas virtuais são as seguintes:

1. Criar um [grupo de recursos](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Criar uma [rede virtual](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Não use uma sub-rede única para todos os serviços do Azure na implantação da plataforma SAP BI. Com base na arquitetura da plataforma SAP BI, você precisa criar várias sub-redes. Nesta implantação, criaremos três sub-redes – sub-rede, sub-rede do repositório de arquivos e sub-rede do gateway de aplicativo.
   - No Azure, o gateway de aplicativo e o Azure NetApp Files sempre precisam estar em uma sub-rede separada. Verifique [aplicativo Azure gateway](../../../application-gateway/configuration-overview.md) e [diretrizes para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) artigo de planejamento de rede para obter mais detalhes.

3. Crie um conjunto de disponibilidade.

   - Para obter redundância para cada camada na implantação de várias instâncias, coloque as máquinas virtuais para cada camada em um conjunto de disponibilidade. Certifique-se de separar os conjuntos de disponibilidade para cada camada com base em sua arquitetura.

4. Crie a máquina virtual 1 **(azusbosl1).**

   - Você pode usar a imagem personalizada ou escolher uma imagem do Azure Marketplace. Consulte [implantando uma VM do Azure Marketplace para SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) ou [implantando uma VM com uma imagem personalizada para SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) com base em sua necessidade.

5. Crie a máquina virtual 2 **(azusbosl2).**
6. Adicione um disco SSD Premium. Ele será usado como o diretório de instalação do SAP BOBI.

## <a name="provision-azure-netapp-files"></a>Provisionar Azure NetApp Files

Antes de continuar com a instalação do Azure NetApp Files, familiarize-se com a [documentação dos arquivos](../../../azure-netapp-files/azure-netapp-files-introduction.md)do Azure NetApp.

Azure NetApp Files está disponível em várias [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Verifique se sua região do Azure selecionada oferece Azure NetApp Files.

Use a página de [Azure NetApp files disponibilidade pela região do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) para verificar a disponibilidade de Azure NetApp files por região.

Solicite a integração com o Azure NetApp Files acessando [Azure NetApp files instruções](../../../azure-netapp-files/azure-netapp-files-register.md) antes de implantar Azure NetApp files.

### <a name="deploy-azure-netapp-files-resources"></a>Implantar recursos do Azure NetApp Files

As instruções a seguir pressupõem que você já tenha implantado sua [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md). Os recursos Azure NetApp Files e as VMs, em que os recursos de Azure NetApp Files serão montados, devem ser implantados na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure.

1. Se você ainda não tiver implantado os recursos, solicite [a integração ao Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Crie uma conta do NetApp na região do Azure selecionada seguindo as instruções em [criar uma conta do NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

3. Configure um pool de capacidade Azure NetApp Files seguindo as instruções em [configurar um pool de capacidade Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

   - A arquitetura da plataforma SAP BI apresentada neste artigo usa um único pool de capacidade de Azure NetApp Files no nível de serviço *Premium* . Para o servidor de repositório de arquivos do SAP BI no Azure, é recomendável usar um [nível de serviço](../../../azure-netapp-files/azure-netapp-files-service-levels.md)Azure NetApp files *Premium* ou *ultra* .

4. Delegue uma sub-rede para Azure NetApp Files, conforme descrito nas instruções em [delegar uma sub-rede para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Implante Azure NetApp Files volumes seguindo as instruções em [criar um volume de NFS para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

   O volume seja pode ser implantado como NFSv3 e NFSv 4.1, pois ambos os protocolos têm suporte para a plataforma SAP BOBI. Implante os volumes na respectiva sub-rede de Azure NetApp Files. Os endereços IP dos volumes do Azure NetApp são atribuídos automaticamente.

Tenha em mente que os recursos Azure NetApp Files e as VMs do Azure devem estar na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure. Por exemplo, azusbobi-frsinput, azusbobi-frsoutput são os nomes de volume e nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput são os caminhos de arquivo para os volumes de Azure NetApp Files.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Considerações importantes

Ao criar seu Azure NetApp Files para o servidor de repositório de arquivos da plataforma SAP BOBI, esteja ciente da seguinte consideração:

1. O pool de capacidade mínima é 4 tebibytes (TiB).
2. O tamanho mínimo do volume é 100 Gibibytes (GiB).
3. Azure NetApp Files e todas as máquinas virtuais em que os volumes de Azure NetApp Files serão montados devem estar na mesma rede virtual do Azure ou em [redes virtuais emparelhadas](../../../virtual-network/virtual-network-peering-overview.md) na mesma região. O acesso do Azure NetApp Files por Emparelhamento VNET na mesma região agora é compatível. O acesso do Azure NetApp por emparelhamento global ainda não tem suporte.
4. A rede virtual selecionada deve ter uma sub-rede que é delegada para Azure NetApp Files.
5. Com a [política de exportação](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)de Azure NetApp files, você pode controlar os clientes permitidos, o tipo de acesso (leitura/gravação, somente leitura e assim por diante).
6. O recurso de Azure NetApp Files ainda não reconhece a zona. Atualmente, o recurso não é implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atendo às possíveis implicações de latência em algumas regiões do Azure.
7. Volumes do Azure NetApp Files podem ser implantados como volumes NFSv3 ou NFSv4.1. Os dois protocolos têm suporte para os aplicativos da plataforma SAP BI.

## <a name="configure-file-systems-on-linux-servers"></a>Configurar sistemas de arquivos em servidores Linux

As etapas nesta seção usam os seguintes prefixos:

**[A]**: a etapa se aplica a todos os hosts

### <a name="format-and-mount-sap-file-system"></a>Formatar e montar o sistema de arquivos SAP

1. **[A]** listar todos os discos anexados

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** dispositivo de bloco de formato para/usr/SAP

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** criar diretório de montagem

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** obter UUID do dispositivo de bloco

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** manter entrada de montagem do sistema de arquivos em/etc/fstab

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** montar sistema de arquivos

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Montar Azure NetApp Files volume

1. **[A]** criar diretórios de montagem

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A] configurar o** sistema operacional cliente para dar suporte à montagem do nfsv 4.1 **(aplicável somente se estiver usando o nfsv 4.1)**

   Se você estiver usando Azure NetApp Files volumes com o protocolo NFSv 4.1, execute a configuração a seguir em todas as VMs, em que Azure NetApp Files os volumes do NFSv 4.1 precisam ser montados.

   **Verificar as configurações de domínio do NFS**

   Verifique se o domínio está configurado como o domínio de Azure NetApp Files padrão, ou seja,  **defaultv4iddomain.com** e o mapeamento está definido como **ninguém**.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Certifique-se de definir o domínio NFS em/etc/idmapd.conf na VM para corresponder à configuração de domínio padrão em Azure NetApp Files: **defaultv4iddomain.com**. Se houver uma incompatibilidade entre a configuração de domínio no cliente NFS (ou seja, a VM) e o servidor NFS, ou seja, a configuração da Azure NetApp, as permissões para arquivos nos volumes do Azure NetApp que são montados nas VMs serão exibidas como ninguém.

   Verificar `nfs4_disable_idmapping`. Ele deve ser definido como **Y**. Para criar a estrutura de diretório em que `nfs4_disable_idmapping` está localizado, execute o comando mount. Você não poderá criar o diretório manualmente em /sys/modules, pois o acesso é reservado para o kernel e os drivers.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** Adicionar entradas de montagem

   Se estiver usando NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Se estiver usando o NFSv 4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** montar volumes de NFS

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>Configurar banco de dados CMS-banco de dados do Azure para MySQL

Esta seção fornece detalhes sobre como provisionar o banco de dados do Azure para MySQL usando o portal do Azure. Ele também fornece instruções sobre como criar bancos de dados de auditoria e CMS para a plataforma SAP BOBI e uma conta de usuário para acessar o banco de dados.

As diretrizes serão aplicáveis somente se você estiver usando o banco de BD do Azure para MySQL. Para outros bancos de dados, consulte documentação específica do SAP ou do banco de dados para obter instruções.

### <a name="create-an-azure-database-for-mysql"></a>Criar um banco de dados do Azure para MySQL

Entre no portal do Azure e siga as etapas mencionadas neste [Guia de início rápido do banco de dados do Azure para MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Alguns pontos a serem observados ao provisionar o banco de dados do Azure para MySQL-

1. Selecione a mesma região para o banco de dados do Azure para MySQL em que os servidores de aplicativos da plataforma SAP BI estão em execução.

2. Escolha uma versão do BD com suporte com base na [matriz de disponibilidade do produto (PAM) para SAP BI](https://support.sap.com/pam) específica para sua versão do SAP BOBI. Siga as mesmas diretrizes de compatibilidade conforme abordado para o MySQL AB no SAP PAM

3. Em "computação + armazenamento", selecione **Configurar servidor** e selecione o tipo de preço apropriado com base na saída de dimensionamento.

4. O **aumento automático do armazenamento** é habilitado por padrão. Tenha em mente que o [armazenamento](../../../mysql/concepts-pricing-tiers.md#storage) pode ser escalado verticalmente, não inativo.

5. Por padrão, o **período de retenção de backup** é de sete dias, mas você pode configurá-lo [opcionalmente](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) até 35 dias.

6. Os backups do banco de dados do Azure para MySQL são localmente redundantes por padrão. portanto, se você quiser backups de servidor no armazenamento com redundância geográfica, selecione **geograficamente redundante** das **Opções de redundância de backup**.

> [!NOTE]
> Não há suporte para a alteração das [Opções de redundância de backup](../../../mysql/concepts-backup.md#backup-redundancy-options) após a criação do servidor.

### <a name="configure-connection-security"></a>Configurar a segurança da conexão

Por padrão, o servidor criado é protegido com um firewall e não é acessível publicamente. Para fornecer acesso à rede virtual onde os servidores de aplicativos da plataforma SAP BI estão em execução, siga as etapas abaixo-  

1. Vá para recursos do servidor no portal do Azure e selecione **segurança de conexão** no menu do lado esquerdo para o recurso de servidor.
2. Selecione **Sim** para **permitir o acesso aos serviços do Azure**.
3. Em regras de VNET, selecione **Adicionar rede virtual existente**. Selecione a rede virtual e a sub-rede do servidor de aplicativos da plataforma SAP BI. Além disso, você precisa fornecer acesso à caixa de salto ou a outros servidores de onde você pode conectar o [MySQL Workbench](../../../mysql/connect-workbench.md) ao banco de dados do Azure para MySQL. O MySQL Workbench será usado para criar o CMS e o banco de dados de auditoria
4. Depois que as redes virtuais forem adicionadas, selecione **salvar**.

### <a name="create-cms-and-audit-database"></a>Criar CMS e banco de dados de auditoria

1. Baixe e instale o MySQL Workbench do [site do MySQL](https://dev.mysql.com/downloads/workbench/). Certifique-se de instalar o MySQL Workbench no servidor que pode acessar o banco de dados do Azure para MySQL.

2. Conecte-se ao servidor usando o MySQL Workbench. Siga a instrução mencionada neste [artigo](../../../mysql/connect-workbench.md#get-connection-information). Se o teste de conexão for bem-sucedido, você receberá a seguinte mensagem:

   ![Conexão do SQL Workbench](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Na guia consulta SQL, execute a consulta abaixo para criar o esquema para o CMS e o banco de dados de auditoria.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Criar conta de usuário para se conectar ao esquema

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. Para verificar os privilégios e as funções da conta de usuário do MySQL

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Instalar o MySQL C API Connector (libmysqlclient) no servidor Linux

Para o servidor de aplicativos SAP BOBI acessar o banco de dados, ele requer drivers/cliente de banco de dados. O conector de API do MySQL C para Linux deve ser usado para acessar bancos de dados do CMS e de auditoria. Não há suporte para a conexão ODBC com o banco de dados CMS. Esta seção fornece instruções sobre como configurar o conector de API do MySQL C no Linux.

1. Consulte o artigo [drivers do MySQL e ferramentas de gerenciamento compatíveis com o banco de dados do Azure para MySQL](../../../mysql/concepts-compatibility.md) , que descreve os drivers que são compatíveis com o banco de dados do Azure para MySQL. Verifique o driver do **MySQL Connector/C (libmysqlclient)** no artigo.

2. Consulte este [link](https://downloads.mysql.com/archives/c-c/) para baixar drivers.

3. Selecione o sistema operacional e baixe o pacote de RPM do componente compartilhado do conector do MySQL. Neste exemplo, a versão do conector MySQL-Connector-c-Shared-6.1.11 é usada.

4. Instale o conector em todas as instâncias do aplicativo SAP BOBI.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Verifique o caminho do libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Defina LD_LIBRARY_PATH para apontar para o `/usr/lib64` diretório da conta de usuário que será usada para instalação.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Preparação do servidor

As etapas nesta seção usam os seguintes prefixos:

**[A]**: a etapa se aplica a todos os hosts.

1. **[A]** com base no tipo de Linux (SLES ou RHEL), você precisa definir os parâmetros do kernel e instalar as bibliotecas necessárias. Consulte a seção **requisitos do sistema** no [Guia de instalação da plataforma de Business Intelligence para UNIX](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US).

2. **[A]** Verifique se o fuso horário em seu computador está definido corretamente. Consulte a [seção requisitos adicionais de UNIX e Linux](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) no guia de instalação.

3. **[A]** criar conta de usuário (**BL1** ADM) e grupo (SAPs) sob a qual os processos em segundo plano do software podem ser executados. Use essa conta para executar a instalação e executar o software. A conta não requer privilégios de raiz.

4. **[A]** definir o ambiente de conta de usuário (**BL1** ADM) para usar uma localidade UTF-8 com suporte e garantir que o software do console ofereça suporte a conjuntos de caracteres UTF-8. Para garantir que seu sistema operacional use a localidade correta, defina as variáveis de ambiente LC_ALL e LANG para sua localidade preferida no seu ambiente de usuário do (**BL1** ADM).

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** configurar conta de usuário (**BL1** ADM).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Baixe e extraia mídia para a plataforma de BI SAP BusinessObjects do SAP Service Marketplace.

## <a name="installation"></a>Instalação

Verificar a localidade da conta de usuário **BL1** ADM no servidor

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Navegue até a mídia da plataforma de BI do SAP BusinessObjects e execute o comando a seguir com o usuário **BL1** ADM-

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Siga o guia de instalação da [plataforma SAP BOBI](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) para UNIX, específico para sua versão. Alguns pontos a serem observados durante a instalação da plataforma SAP BOBI.

- Na tela **Configurar registro do produto** , você pode usar a chave de licença temporária para soluções SAP BusinessObjects da nota SAP [1288121](https://launchpad.support.sap.com/#/notes/1288121) ou pode gerar a chave de licença no SAP Service Marketplace

- Na tela **Selecionar tipo de instalação** , selecione instalação **completa** no primeiro servidor (azusbosl1), para outro servidor (azusbosl2) selecione **personalizado/expandir** que expandirá a configuração existente do BOBI.

- Na tela **Selecionar Banco de dados padrão ou existente** , selecione **configurar um banco de dados existente**, o que solicitará que você selecione CMS e banco de dados de auditoria. Selecione **MySQL** para tipo de banco de dados CMS e tipo de banco de dados de auditoria.

  Você também pode selecionar sem banco de dados de auditoria, se não quiser configurar a auditoria durante a instalação.

- Selecione as opções apropriadas na **tela selecionar servidor de aplicativos da Web Java** com base em sua arquitetura SAP BOBI. Neste exemplo, selecionamos a opção 1, que instala o servidor Tomcat na mesma plataforma SAP BOBI.

- Insira as informações do banco de dados CMS em **configurar banco de dados do repositório CMS-MySQL**. Exemplo de entrada para informações do banco de dados CMS para instalação do Linux. O banco de dados do Azure para MySQL é usado na porta padrão 3306
  
  ![Implantação do SAP BOBI no banco de dados do Linux-CMS](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- Adicional Insira informações do banco de dados de auditoria em **configurar repositório de auditoria banco de dados-MySQL**. Exemplo de entrada para informações de banco de dados de auditoria para instalação do Linux.

  ![Implantação do SAP BOBI no Linux – banco de dados de auditoria](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Siga as instruções e insira as entradas necessárias para concluir a instalação.

Para a implantação de várias instâncias, execute a instalação do no segundo host (azusbosl2). Durante a tela **Selecionar tipo de instalação** , selecione **personalizado/expandir** que expandirá a configuração existente do BOBI.

Na oferta do banco de dados do Azure para MySQL, um gateway é usado para redirecionar as conexões para instâncias de servidor. Depois que a conexão for estabelecida, o cliente MySQL exibirá a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, use o `SELECT VERSION();` comando no prompt do MySQL. Portanto, no console de gerenciamento central (CMC), você encontrará uma versão de banco de dados diferente que é basicamente a versão definida no gateway. Verifique [as versões do servidor de banco de dados do Azure para MySQL com suporte](../../../mysql/concepts-supported-versions.md) para obter mais detalhes.

![Implantação do SAP BOBI no Linux – configurações de CMC](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Pós-instalação

### <a name="tomcat-clustering---session-replication"></a>Clustering do Tomcat – replicação da sessão

O Tomcat dá suporte ao clustering de dois ou mais servidores de aplicativos para replicação e failover de sessão. As sessões da plataforma SAP BOBI são serializadas, uma sessão de usuário pode fazer failover diretamente para outra instância do Tomcat, mesmo quando um servidor de aplicativos falha.

Por exemplo, se um usuário estiver conectado a um servidor Web que falha enquanto o usuário está navegando em uma hierarquia de pastas no aplicativo SAP BI. Com um cluster configurado corretamente, o usuário pode continuar navegando na hierarquia de pastas sem ser redirecionado para a página de entrada.

No SAP Note [2808640](https://launchpad.support.sap.com/#/notes/2808640), as etapas para configurar o clustering do Tomcat são fornecidas usando multicast. Mas, no Azure, não há suporte para multicast. Portanto, para que o cluster do Tomcat funcione no Azure, você deve usar [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Verifique o [clustering do Tomcat usando a associação estática para SAP BusinessObjects plataforma de bi](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) no blog SAP para configurar o cluster do Tomcat no Azure.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Camada da Web de balanceamento de carga da plataforma SAP BI

Na implantação de várias instâncias do SAP BOBI, os servidores de aplicativos Web Java (camada da Web) estão em execução em dois ou mais hosts. Para distribuir a carga de usuário uniformemente entre servidores Web, você pode usar um balanceador de carga entre usuários finais e servidores Web. No Azure, você pode usar Azure Load Balancer ou Aplicativo Azure gateway para gerenciar o tráfego para seus servidores de aplicativos Web. Detalhes sobre cada oferta são explicados na seção a seguir.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure Load Balancer (balanceador de carga baseado em rede)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) é um balanceador de carga de camada 4 (TCP, UDP) de baixa latência que distribui o tráfego entre máquinas virtuais íntegras. Uma investigação de integridade do balanceador de carga monitora uma determinada porta em cada VM e distribui apenas o tráfego para uma ou mais máquinas virtuais operacionais. Você pode escolher um balanceador de carga público ou um balanceador de carga interno, dependendo se você deseja que a plataforma SAP BI seja acessível da Internet ou não. Sua zona com redundância, garantindo alta disponibilidade entre Zonas de Disponibilidade.

Consulte a seção Load Balancer interna na figura abaixo, em que servidor de aplicativos da Web é executado na porta 8080, porta HTTP Tomcat padrão, que será monitorada pela investigação de integridade. Portanto, qualquer solicitação de entrada que venha dos usuários finais será redirecionada para os servidores de aplicativos Web (azusbosl1 ou azusbosl2) no pool de back-end. O balanceador de carga não dá suporte à terminação TLS/SSL (também conhecida como descarregamento de TLS/SSL). Se você estiver usando o Azure Load Balancer para distribuir o tráfego entre servidores Web, é recomendável usar Standard Load Balancer.

> [!NOTE]
> Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Standard Azure Load Balancer (sem endereço IP público), não haverá nenhuma conectividade de saída com a Internet se não houver configuração adicional a fim de permitir o roteamento para pontos de extremidade públicos. Para obter detalhes sobre como alcançar conectividade de saída, veja [Conectividade de ponto de extremidade público para Máquinas Virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](high-availability-guide-standard-load-balancer-outbound-connections.md).

![Azure Load Balancer balancear o tráfego entre servidores Web](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Gateway de aplicativo do Azure (balanceador de carga de aplicativo Web)

O [Gateway de aplicativo Azure (AGW)](../../../application-gateway/overview.md) fornece o ADC (controlador de entrega de aplicativos) como um serviço, que é usado para ajudar o aplicativo a direcionar o tráfego do usuário para um ou mais servidores de aplicativos Web. Ele oferece vários recursos de balanceamento de carga de camada 7, como descarregamento de TLS/SSL, WAF (firewall do aplicativo Web), afinidade de sessão baseada em cookie e outros para seus aplicativos.

Na plataforma SAP BI, o gateway de aplicativo direciona o tráfego da Web do aplicativo para os recursos especificados em um pool de back-end-azusbosl1 ou azusbos2. Você atribui um ouvinte à porta, cria regras e adiciona recursos a um pool de back-end. Na figura abaixo, o gateway de aplicativo com endereço IP de front-end privado (10.31.3.20) atua como ponto de entrada para os usuários, manipula conexões TLS/SSL (HTTPS-TCP/443) de entrada, descriptografa o TLS/SSL e passando a solicitação não criptografada (HTTP-TCP/8080) para os servidores no pool de back-end. Com o recurso de terminação TLS/SSL criado, precisamos apenas manter um certificado TLS/SSL no gateway de aplicativo, o que simplifica as operações.

![Gateway de aplicativo para balancear o tráfego entre servidores Web](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Para configurar o gateway de aplicativo para o servidor Web SAP BOBI, você pode consultar [balanceamento de carga de servidores Web SAP BOBI usando aplicativo Azure gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) no blog SAP.

> [!NOTE]
> É recomendável usar Aplicativo Azure gateway para balancear a carga do tráfego para o servidor Web, pois ele fornece gosta de recursos como descarregamento de SSL, centralizar o gerenciamento de SSL para reduzir a sobrecarga de criptografia e descriptografia no servidor, Round-Robin algoritmo para distribuir o tráfego, recursos de WAF (firewall do aplicativo Web), alta disponibilidade e assim por diante.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>Plataforma de BI SAP BusinessObjects-backup e restauração

Backup e restauração é um processo de criação de cópias periódicas de dados e aplicativos para um local separado. Portanto, ele pode ser restaurado ou recuperado para o estado anterior se os dados ou os aplicativos originais forem perdidos ou danificados. Ele também é um componente essencial de qualquer estratégia de recuperação de desastres de negócios.

Para desenvolver uma estratégia abrangente de backup e restauração para a plataforma SAP BOBI, identifique os componentes que levam ao tempo de inatividade do sistema ou à interrupção no aplicativo. Na plataforma SAP BOBI, o backup dos seguintes componentes é vital para proteger o aplicativo.

- Diretório de instalação do SAP BOBI (discos Premium gerenciados)
- Servidor de repositório de arquivos (arquivos Azure NetApp Files ou Premium do Azure)
- Banco de dados CMS (banco de dados do Azure para MySQL ou banco de dados na VM do Azure)

A seção a seguir descreve como implementar a estratégia de backup e restauração para cada componente na plataforma SAP BOBI.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Backup & restauração para o diretório de instalação do SAP BOBI

No Azure, a maneira mais simples de fazer backup de servidores de aplicativos e de todos os discos anexados é usando o serviço de [backup do Azure](../../../backup/backup-overview.md) . Ele fornece backups independentes e isolados para proteger a destruição não intencional dos dados em suas VMs. Os backups são armazenados em um cofre dos Serviços de Recuperação com gerenciamento interno de pontos de recuperação. A configuração e o dimensionamento são simples, os backups são otimizados e podem ser restaurados facilmente quando necessário.

Como parte do processo de backup, o instantâneo é obtido e os dados são transferidos para o cofre do serviço de recuperação sem afetar as cargas de trabalho de produção. O instantâneo fornece um nível de consistência diferente, conforme descrito no artigo de [consistência do instantâneo](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) . Você também pode optar por fazer backup do subconjunto dos discos de dados na VM usando a funcionalidade de backup e restauração de discos seletivos. Para obter mais informações, consulte documento de [backup de VM do Azure](../../../backup/backup-azure-vms-introduction.md) e [perguntas frequentes-backup de VMs do Azure](../../../backup/backup-azure-vm-backup-faq.yml).

#### <a name="backup--restore-for-file-repository-server"></a>Backup & restauração do servidor de repositório de arquivos

Por **Azure NetApp files**, você pode criar instantâneos sob demanda e agendar o instantâneo automático usando políticas de instantâneo. As cópias de instantâneo fornecem uma cópia pontual do volume seja. Para obter mais informações, consulte [gerenciar instantâneos usando Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

O backup de **arquivos do Azure** é integrado ao serviço de [backup nativo do Azure](../../../backup/backup-overview.md) , que centraliza a função de backup e restauração junto com as VMs backup e simplifica o trabalho de operação. Para obter mais informações, consulte [backup de compartilhamento de arquivos do Azure](../../../backup/azure-file-share-backup-overview.md) e [perguntas frequentes – fazer backup de arquivos do Azure](../../../backup/backup-azure-files-faq.md).

#### <a name="backup--restore-for-cms-database"></a>Backup & restauração para banco de dados CMS

O banco de dados do Azure do MySQL é uma oferta de DBaaS no Azure, que cria automaticamente backups de servidor e os armazena em um armazenamento com redundância geográfica ou local configurado pelo usuário. O banco de dados do Azure do MySQL faz backups dos arquivos de data e do log de transações. Dependendo do tamanho máximo de armazenamento com suporte, ele usa backups completos e diferenciais (servidores de armazenamento máximo de 4 TB) ou backup de instantâneo (até 16 TB de servidores de armazenamento máximo). Esses backups permitem que você restaure um servidor em qualquer ponto no tempo em seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias, que você pode [opcionalmente configurar](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) para até três dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Esses arquivos de backup não são expostos pelo usuário e não podem ser exportados. Esses backups só podem ser usados para operações de restauração no banco de dados do Azure para MySQL. Você pode usar [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) para copiar um banco de dados. Para obter mais informações, consulte [backup e restauração no banco de dados do Azure para MySQL](../../../mysql/concepts-backup.md).

Para o banco de dados instalado em máquinas virtuais, você pode usar as ferramentas de backup padrão ou o [backup do Azure](../../../backup/sap-hana-db-about.md) para o banco de dados Hana. Além disso, se os serviços e as ferramentas do Azure não atenderem ao seu requisito, você poderá usar outras ferramentas de backup ou script para criar backups de discos.

## <a name="sap-businessobjects-bi-platform-reliability"></a>Confiabilidade da plataforma de BI SAP BusinessObjects

A plataforma de BI SAP BusinessObjects inclui diferentes camadas, que são otimizadas para tarefas e operações específicas. Quando um componente de qualquer camada se torna indisponível, o aplicativo SAP BOBI se tornará inacessível ou determinadas funcionalidades do aplicativo não funcionarão. Portanto, é necessário ter certeza de que cada camada foi projetada para ser confiável para manter o aplicativo operacional sem qualquer interrupção dos negócios.

Esta seção se concentra nas seguintes opções para a plataforma SAP BOBI-

- **Alta disponibilidade:** Uma plataforma alta disponível tem pelo menos duas de tudo na região do Azure para manter o aplicativo operacional se um dos servidores ficar indisponível.
- **Recuperação de desastre:** É um processo de restaurar a funcionalidade do aplicativo se houver perda catastrófica, como toda a região do Azure se tornar indisponível devido a algum desastre natural.

A implementação dessa solução varia de acordo com a natureza da configuração do sistema no Azure. Portanto, o cliente precisa personalizar a solução de alta disponibilidade e recuperação de desastres com base em seus requisitos de negócios.

### <a name="high-availability"></a>Alta disponibilidade

Alta disponibilidade refere-se a um conjunto de tecnologias que podem minimizar as interrupções de ti, fornecendo continuidade de negócios de aplicativos/serviços por meio de componentes redundantes, tolerantes a falhas ou protegidos por failover dentro do mesmo data center. Em nosso caso, os data centers estão dentro de uma região do Azure. O artigo [arquitetura e cenários de alta disponibilidade para o SAP](sap-high-availability-architecture-scenarios.md) fornece uma visão inicial de diferentes técnicas de alta disponibilidade e recomendações oferecidas no Azure para aplicativos SAP, que complementam as instruções nesta seção.

Com base no resultado de dimensionamento da plataforma SAP BOBI, você precisa projetar o cenário e determinar a distribuição de componentes de BI em sub-redes e máquinas virtuais do Azure. O nível de redundância na arquitetura distribuída depende do RTO (objetivo de tempo de recuperação) necessário e do RPO (objetivo de ponto de recuperação). A SAP BOBI Platform inclui diferentes camadas e componentes em cada camada deve ser projetado para obter redundância. Assim, se um componente falhar, haverá pouca ou nenhuma interrupção no aplicativo SAP BOBI. Por exemplo,

- Servidores de aplicativos redundantes, como servidores de aplicativos de BI e servidor Web
- Componentes exclusivos como banco de dados CMS, servidor de repositório de arquivos, Load Balancer

A seção a seguir descreve como obter alta disponibilidade em cada componente da plataforma SAP BOBI.

#### <a name="high-availability-for-application-servers"></a>Alta disponibilidade para servidores de aplicativos

Para servidores de aplicativos da Web e BI se eles são instalados separadamente ou juntos, o não precisa de uma solução de alta disponibilidade específica. Você pode obter alta disponibilidade por redundância, ou seja, configurando várias instâncias de BI e servidores Web em várias máquinas virtuais do Azure.

Para reduzir o impacto do tempo de inatividade devido a um ou mais eventos, é aconselhável seguir abaixo a prática de alta disponibilidade para os servidores de aplicativos em execução em várias máquinas virtuais.

- Use Zonas de Disponibilidade para proteger as falhas do datacenter.
- Configure várias máquinas virtuais em um conjunto de disponibilidade para redundância.
- Use Managed Disks para VMs em um conjunto de disponibilidade.
- Configure cada camada de aplicativo em conjuntos de disponibilidade separados.

Para obter mais informações, marque [gerenciar a disponibilidade de máquinas virtuais do Linux](../../availability.md)

#### <a name="high-availability-for-cms-database"></a>Alta disponibilidade para o banco de dados CMS

Se você estiver usando o serviço DBaaS (banco de dados como serviço) do Azure para banco de dados CMS, a estrutura de alta disponibilidade será fornecida por padrão. Basta selecionar a região e o serviço inerentes recursos de alta disponibilidade, redundância e resiliência sem exigir que você configure nenhum componente adicional. Para obter mais detalhes sobre o SLA de oferta de DBaaS com suporte no Azure, verifique a [alta disponibilidade no banco de dados do Azure para MySQL](../../../mysql/concepts-high-availability.md) e [alta disponibilidade para o banco de dados SQL do Azure](../../../azure-sql/database/high-availability-sla.md)

Para outra implantação de DBMS para banco de dados CMS, consulte os [guias de implantação do DBMS para carga de trabalho do SAP](dbms_guide_general.md), que fornece informações sobre a implantação de DBMS diferente e sua abordagem para obter alta disponibilidade.

#### <a name="high-availability-for-file-repository-server"></a>Alta disponibilidade para o servidor de repositório de arquivos

O FRS (servidor de repositório de arquivos) refere-se aos diretórios de disco em que o conteúdo como relatórios, universos e conexões são armazenados. Ele está sendo compartilhado entre todos os servidores de aplicativos do sistema. Portanto, você deve ter certeza de que ele está altamente disponível.

No Azure, você pode escolher [arquivos premium do Azure](../../../storage/files/storage-files-introduction.md) ou [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) para compartilhamento de arquivos que são projetados para serem altamente disponíveis e altamente duráveis por natureza. Para obter mais informações, consulte a seção [redundância](../../../storage/files/storage-files-planning.md#redundancy) para arquivos do Azure.

> [!NOTE]
> O protocolo SMB para arquivos do Azure está em disponibilidade geral, mas o suporte do protocolo NFS para arquivos do Azure está atualmente em versão prévia. Para obter mais informações, consulte o [suporte do NFS 4,1 para arquivos do Azure está agora em visualização](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

Como esse serviço de compartilhamento de arquivos não está disponível em todas as regiões, certifique-se de consultar os [produtos disponíveis por](https://azure.microsoft.com/en-us/global-infrastructure/services/) site de região para encontrar informações atualizadas. Se o serviço não estiver disponível em sua região, você poderá criar um servidor NFS a partir do qual você pode compartilhar o sistema de arquivos para o aplicativo SAP BOBI. Mas você também precisará considerar sua alta disponibilidade.

#### <a name="high-availability-for-load-balancer"></a>Alta disponibilidade para o balanceador de carga

Para distribuir o tráfego entre o servidor Web, você pode usar Azure Load Balancer ou Aplicativo Azure gateway. A redundância para qualquer um dos balanceadores de carga pode ser obtida com base na SKU escolhida para implantação.

- Por Azure Load Balancer, a redundância pode ser obtida Configurando Standard Load Balancer frontend como com redundância de zona. Para obter mais informações, consulte [Standard Load Balancer e zonas de disponibilidade](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Para o gateway de aplicativo, a alta disponibilidade pode ser obtida com base no tipo de camada selecionado durante a implantação.
  - a SKU v1 dá suporte a cenários de alta disponibilidade quando você implantou duas ou mais instâncias. O Azure distribui essas instâncias entre domínios de atualização e de falha para garantir que todas as instâncias não falham ao mesmo tempo. Assim, com essa SKU, a redundância pode ser obtida dentro da zona
  - a SKU v2 garante automaticamente que novas instâncias sejam distribuídas entre domínios de falha e domínios de atualização. Se a redundância de zona for escolhida, as instâncias mais recentes também serão distribuídas entre as zonas de disponibilidade para oferecer resiliência zonal em caso de falha. Para obter mais detalhes, consulte [dimensionamento automático e gateway de aplicativo com redundância de zona v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Arquitetura de alta disponibilidade de referência para plataforma de BI SAP BusinessObjects

A arquitetura de referência abaixo descreve a configuração da plataforma SAP BOBI usando o conjunto de disponibilidade, que fornece redundância e disponibilidade de VMs dentro da zona. A arquitetura demonstra o uso de diferentes serviços do Azure, como Aplicativo Azure gateway, Azure NetApp Files e banco de dados do Azure para MySQL para plataforma SAP BOBI, que oferece redundância interna, o que reduz a complexidade de gerenciar diferentes soluções de alta disponibilidade.

Na figura abaixo, o tráfego de entrada (HTTPS-TCP/443) tem balanceamento de carga usando Aplicativo Azure SKU do gateway v1, que é altamente disponível quando implantado em duas ou mais instâncias. Várias instâncias do servidor Web, servidores de gerenciamento e servidores de processamento são implantadas em máquinas virtuais separadas para obter redundância e cada camada é implantada em conjuntos de disponibilidade separados. Azure NetApp Files tem redundância interna no data center, portanto, os volumes de seja para o servidor de repositório de arquivos estarão altamente disponíveis. O banco de dados CMS é provisionado no banco de dados do Azure para MySQL (DBaaS) que tem alta disponibilidade inerente. Para obter mais informações, consulte o guia de [alta disponibilidade no banco de dados do Azure para MySQL](../../../mysql/concepts-high-availability.md) .

![Redundância da plataforma de BI SAP BusinessObjects usando conjuntos de disponibilidade](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

A arquitetura acima fornece informações sobre como a implantação do SAP BOBI no Azure pode ser feita. Mas ele não cobre todas as opções de configuração possíveis para a plataforma SAP BOBI no Azure. O cliente pode personalizar sua implantação com base em seus requisitos de negócios, escolhendo produtos/serviços diferentes para componentes diferentes, como Load Balancer, servidor de repositório de arquivos e DBMS.

Em várias regiões do Azure, Zonas de Disponibilidade são oferecidas, o que significa que ela tem fornecimento independente de fonte de energia, resfriamento e rede. Ele permite que o cliente implante o aplicativo em duas ou três zonas de disponibilidade. Para o cliente que deseja obter alta disponibilidade no AZs, é possível implantar a plataforma SAP BOBI em zonas de disponibilidade, garantindo que cada componente no aplicativo tenha redundância de zona.

### <a name="disaster-recovery"></a>Recuperação de desastre

A instrução nesta seção explica a estratégia para fornecer proteção de recuperação de desastre para a plataforma SAP BOBI. Ele complementa o documento de [recuperação de desastre para SAP](../../../site-recovery/site-recovery-sap.md) , que representa os principais recursos para a abordagem geral de recuperação de desastres do SAP.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Referência de arquitetura de recuperação de desastre para plataforma de BI SAP BusinessObjects

Essa arquitetura de referência está executando a implantação de várias instâncias da plataforma SAP BOBI com servidores de aplicativos redundantes. Para a recuperação de desastres, você deve fazer failover de todas as camadas para uma região secundária. Cada camada usa uma estratégia diferente para fornecer proteção de recuperação de desastre.

![Recuperação de desastre da plataforma de BI SAP BusinessObjects](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Balanceador de carga

Load Balancer é usado para distribuir o tráfego entre servidores de aplicativos da Web da plataforma SAP BOBI. Para obter a DR para Aplicativo Azure gateway, implemente a instalação paralela do gateway de aplicativo na região secundária.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Máquinas virtuais que executam servidores de aplicativos Web e BI

Azure Site Recovery serviço pode ser usado para replicar máquinas virtuais que executam servidores de aplicativos Web e BI na região secundária. Ele replica os servidores na região secundária para que, quando ocorrerem desastres e interrupções, você possa fazer failover facilmente para seu ambiente replicado e continuar trabalhando

#### <a name="file-repository-servers"></a>Servidores de repositório de arquivos

- **Azure NetApp files** fornece volumes de NFS e SMB, portanto, qualquer ferramenta de cópia baseada em arquivo pode ser usada para replicar dados entre regiões do Azure. Para obter mais informações sobre como copiar o volume seja em outra região, consulte [perguntas frequentes sobre Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  Você pode usar Azure NetApp Files replicação entre regiões, que está atualmente em versão [prévia](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) que usa a tecnologia de® SnapMirror do NetApp. Portanto, somente os blocos alterados são enviados pela rede em um formato compactado e eficiente. Essa tecnologia proprietária minimiza a quantidade de dados necessária para replicar entre as regiões, o que poupa custos de transferência de dados. Ele também reduz o tempo de replicação para que você possa obter um RPO (objetivo de ponto de restauração) menor. Consulte [requisitos e considerações para usar a replicação entre regiões](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) para obter mais informações.

- **Os arquivos premium do Azure** oferecem suporte apenas localmente com redundância local (LRS) e ZRS (armazenamento com redundância de zona). Para a estratégia de DR de arquivos premium do Azure, você pode usar [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) ou [Azure PowerShell](/powershell/module/az.storage/) para copiar os arquivos para outra conta de armazenamento em uma região diferente. Para obter mais informações, consulte [recuperação de desastre e failover da conta de armazenamento](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>Banco de dados CMS

O banco de dados do Azure para MySQL fornece várias opções para recuperar o banco de dados se houver qualquer desastre. Escolha a opção apropriada que funciona para sua empresa.

- Habilite réplicas de leitura entre regiões para aprimorar sua continuidade de negócios e planejamento de recuperação de desastre. Você pode replicar do servidor de origem para até cinco réplicas. As réplicas de leitura são atualizadas de forma assíncrona usando a tecnologia de replicação de log binário do MySQL. As réplicas são novos servidores que você gerencia de modo semelhante aos servidores normais do Banco de Dados do Azure para PostgreSQL. Saiba mais sobre réplicas de leitura, regiões disponíveis, restrições e como fazer failover do [artigo conceitos de leitura de réplicas](../../../mysql/concepts-read-replicas.md).

- Use o recurso de restauração geográfica do banco de dados do Azure para MySQL que restaura o servidor usando backups com redundância geográfica. Esses backups são acessíveis mesmo quando a região em que o servidor está hospedado está offline. É possível restaurar a partir desses backups para qualquer outra região e retornar o servidor para online.

  > [!NOTE]
  > A restauração geográfica somente será possível se o servidor foi provisionado com armazenamento de backup com redundância geográfica. Não há suporte para a alteração das **Opções de redundância de backup** após a criação do servidor. Para obter mais informações, consulte o artigo [redundância de backup](../../../mysql/concepts-backup.md#backup-redundancy-options) .

A seguir, a recomendação para a recuperação de desastre de cada camada usada neste exemplo.

| Camadas de plataforma SAP BOBI   | Recomendação                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Gateway de Aplicativo do Azure | Configuração paralela do gateway de aplicativo na região secundária                                                |
| Servidores de aplicativos Web   | Replicar usando Site Recovery                                                                         |
| Servidores de aplicativos de BI    | Replicar usando Site Recovery                                                                         |
| Azure NetApp Files        | Ferramenta de cópia baseada em arquivo para replicar dados para a região secundária **ou** replicação entre regiões do seja (versão prévia) |
| Banco de Dados do Azure para MySQL  | Réplicas de leitura entre regiões **ou** backup de restauração de backups com redundância geográfica.                             |

## <a name="next-steps"></a>Próximas etapas

- [Configurar a recuperação de desastre para uma implantação de aplicativo SAP de várias camadas](../../../site-recovery/site-recovery-sap.md)
- [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP](planning-guide.md)
- [Implantação de Máquinas Virtuais do Azure para SAP](deployment-guide.md)
- [Implantação do DBMS de Máquinas Virtuais do Azure para SAP](./dbms_guide_general.md)
