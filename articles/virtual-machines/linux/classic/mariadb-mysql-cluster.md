---
title: Executar um cluster MariaDB (MySQL) no Azure | Microsoft Docs
description: "Criar um cluster MariaDB + Galera MySQL nas Máquinas Virtuais do Azure"
services: virtual-machines-linux
documentationcenter: 
author: sabbour
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 53e9bf18b26338212411ea7c4f260eb308486738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>Cluster MariaDB (MySQL): tutorial do Azure
> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager](../../../resource-manager-deployment-model.md) e Clássico. Este artigo aborda o modelo de implantação clássico. A Microsoft recomenda que a maioria das novas implantações use o modelo do Azure Resource Manager.

> [!NOTE]
> O cluster MariaDB Enterprise agora está disponível no Azure Marketplace. A nova oferta implantará automaticamente um cluster MariaDB Galera no Azure Resource Manager. Você deve usar a nova oferta do [Azure Marketplace](https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/).
>
>

Este artigo mostra como criar um cluster [Galera](http://galeracluster.com/products/) multimestre de [MariaDBs](https://mariadb.org/en/about/), (uma substituição robusta, escalonável e confiável para o MySQL) que funciona em um ambiente altamente disponível em Máquinas Virtuais do Azure.

## <a name="architecture-overview"></a>Visão geral da arquitetura
Este artigo descreve como concluir as etapas a seguir:

- Crie um cluster de três nós.
- Separe os discos de dados do disco do sistema operacional.
- Criar os discos de dados na configuração RAID-0/distribuído para aumentar o IOPS.
- Use o Azure Load Balancer para balancear a carga dos três nós.
- Para minimizar o trabalho repetitivo, crie uma imagem de VM contendo MariaDB+Galera e use-a para criar as outras VMs do cluster.

![Arquitetura do sistema](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> Este tópico usa as ferramentas da [CLI do Azure](../../../cli-install-nodejs.md), certifique-se de baixá-las e conectá-las à sua assinatura do Azure de acordo com as instruções. Se você precisar de uma referência aos comandos disponíveis na CLI do Azure, confira a [Referência de comando da CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Você também precisará [criar uma chave SSH para autenticação] e anotar o local do arquivo .pem.
>
>

## <a name="create-the-template"></a>Criar o modelo
### <a name="infrastructure"></a>Infraestrutura
1. Crie um grupo de afinidades para manter os recursos juntos.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. Crie uma rede virtual.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. Crie uma conta de armazenamento para hospedar todos os nossos discos. Você não deve colocar mais de 40 discos muito usados na mesma conta de armazenamento para evitar atingir o limite de 20.000 IOPS da conta de armazenamento. Nesse caso, estamos muito abaixo desse limite, por isso vamos armazenar tudo na mesma conta por fins de simplicidade.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. Localize o nome da imagem da máquina virtual CentOS 7.

        azure vm image list | findstr CentOS
   A saída será algo como `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Use esse nome na etapa a seguir.
5. Crie o modelo de VM e substitua /path/to/key.pem pelo caminho em que você armazenou a chave SSH .pem gerada.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. Anexe quatro discos de dados de 500 GB à VM para uso na configuração RAID.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. Use o SSH para entrar na VM de modelo que você criou em mariadbhatemplate.cloudapp.net:22 e conecte-se usando sua chave privada.

### <a name="software"></a>Software
1. Obtenha a raiz.

        sudo su

2. Instale o suporte RAID:

    a. Instalar mdadm.

              yum install mdadm

    b. Crie a configuração RAID0/faixa com um sistema de arquivos EXT4.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Crie o diretório de ponto de montagem.

              mkdir /mnt/data
    d. Recupere o UUID do dispositivo RAID recém-criado.

              blkid | grep /dev/md0
    e. Edite /etc/fstab.

              vi /etc/fstab
    f. Adicione o dispositivo para habilitar a montagem automática na reinicialização, substituindo o UUID pelo valor obtido do comando **blkid** anterior.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Monte a nova partição.

              mount /mnt/data

3. Instale o MariaDB.

    a. Crie o arquivo MariaDB.repo.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Atualize o arquivo de repositório com o seguinte conteúdo:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Para evitar conflitos, remova os mariadb-libs e pós-fixados existentes.

           yum remove postfix mariadb-libs-*
    d. Instale o MariaDB com Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

4. Mova o diretório de dados MySQL para o dispositivo de blocos RAID.

    a. Copie o diretório atual do MySQL para o novo local e remova o diretório antigo.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. Defina as permissões para o novo diretório adequadamente.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Crie um symlink que aponta o diretório antigo para o novo local na partição do RAID.

           ln -s /mnt/data/mysql /var/lib/mysql

5. Como o [SELinux interfere nas operações de cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), é necessário desabilitá-lo para a sessão atual. Edite `/etc/selinux/config` para desabilitá-lo para reinicializações subsequentes.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. Valide as execuções do MySQL.

   a. Inicie o MySQL.

           service mysql start
   b. Proteja a instalação do MySQL, defina a senha raiz, remova usuários anônimos para desabilitar o logon de raiz remoto e remova o banco de dados de teste.

           mysql_secure_installation
   c. Crie um usuário no banco de dados para as operações de cluster e, opcionalmente, para seus aplicativos.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Pare o MySQL.

            service mysql stop
7. Crie espaço reservado para configuração.

   a. Edite a configuração do MySQL para criar um espaço reservado para as configurações de cluster. Não substitua o **`<Variables>`** nem remova as marcas de comentários agora. Isso acontecerá depois de criar uma VM com base neste modelo.

            vi /etc/my.cnf.d/server.cnf
   b. Edite a seção **[galera]** e limpe-a.

   c. Edite a seção **[mariadb]**.

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
8. Abra as portas necessárias no firewall usando FirewallD no CentOS 7.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Recarregue o firewall: `firewall-cmd --reload`

9. Otimize o sistema para o desempenho. Para obter mais informações, consulte [estratégia de ajuste de desempenho](optimize-mysql.md).

   a. Edite o arquivo de configuração do MySQL novamente.

            vi /etc/my.cnf.d/server.cnf
   b. Edite a seção **[mariadb]** e acrescente o seguinte conteúdo:

   > [!NOTE]
   > É recomendável que innodb\_buffer\_pool_size seja 70% da memória da VM. Neste exemplo, ele foi definido como 2,45 GB para a VM do Azure média com 3,5 GB de RAM.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. Pare o MySQL, desabilite o serviço MySQL para que não seja executado na inicialização a fim de evitar a interrupção do cluster ao adicionar um novo nó e desprovisione o computador.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. Capture a VM por meio do portal. (Atualmente, as ferramentas do [problema #1268 na CLI do Azure](https://github.com/Azure/azure-xplat-cli/issues/1268) descrevem o fato de que as imagens capturadas pelas ferramentas da CLI do Azure não capturam os discos de dados anexados.)

    a. Desligue o computador por meio do portal.

    b. Clique em **Capturar** e especifique o nome da imagem como **mariadb-galera-image**. Forneça uma descrição e marque “Executei o waagent”.
      
      ![Capturar a máquina virtual](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>Criar o cluster
Crie três VMs com o modelo criado e configure e inicie o cluster.

1. Crie sua primeira VM do CentOS 7 da imagem mariadb-galera-image que você criou, fornecendo as seguintes informações:

 - Nome da rede virtual: mariadbvnet
 - Sub-rede: mariadb
 - Tamanho do computador: médio
 - Nome do serviço de nuvem: mariadbha (ou qualquer nome que desejar para ser acessado por mariadbha.cloudapp.net)
 - Nome do computador: mariadb1
 - Nome de usuário: azureuser
 - Acesso SSH: habilitado
 - Passe o arquivo .pem de certificado SSH e substitua /path/to/key.pem pelo caminho em que você armazenou a chave SSH .pem gerada.

   > [!NOTE]
   > Os comandos a seguir são divididos em várias linhas para maior clareza, mas você deve inserir cada um como uma única linha.
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
2. Crie duas outas máquinas virtuais conectando-as ao serviço de nuvem mariadbha. Altere o nome da VM e a porta SSH para uma porta exclusiva que não esteja em conflito com outras VMs no mesmo serviço de nuvem.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  Para MariaDB3:

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
3. Você precisará obter o endereço IP interno de cada uma das três VMs para a próxima etapa:

    ![Obtendo o endereço de IP](./media/mariadb-mysql-cluster/IP.png)
4. Use SSH para entrar nas três VMs e edite o arquivo de configuração em cada um delas.

        sudo vi /etc/my.cnf.d/server.cnf

    Remova as marcas de comentário **`wsrep_cluster_name`** e **`wsrep_cluster_address`** removendo o **#** no início da linha.
    Além disso, substitua **`<ServerIP>`** em **`wsrep_node_address`** e **`<NodeName>`** em **`wsrep_node_name`** pelo endereço IP e o nome da VM, respectivamente, removendo também as marcas de comentário nessas linhas.
5. Inicie o cluster em MariaDB1 e deixe-o em execução na inicialização.

        sudo service mysql bootstrap
        chkconfig mysql on
6. Inicie o MySQL em MariaDB2 e MariaDB3 e deixe-o em execução na inicialização.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>Balancear carga do cluster
Ao criar as VMs clusterizadas, você as adicionou em um conjunto de disponibilidade chamado clusteravset para garantir que sejam colocadas em diferentes domínios de falha e atualização, e que o Azure nunca faça manutenção em todos as máquinas ao mesmo tempo. Essa configuração atende aos requisitos para ter suporte pelo SLA (Contrato de Nível de Serviço) do Azure.

Agora, use o Azure Load Balancer para balancear as solicitações entre os três nós.

Execute os comandos a seguir no seu computador usando a CLI do Azure.

A estrutura dos parâmetros de comando é: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

A CLI define o intervalo da investigação do balanceador de carga para 15 segundos, o que pode ser um tanto longo demais. Altere-o no portal em **Pontos de Extremidade** para qualquer uma das VMs.

![Editar ponto de extremidade](./media/mariadb-mysql-cluster/Endpoint.PNG)

Selecione **Reconfigurar o conjunto de balanceamento de carga**.

![Reconfigurar o conjunto de balanceamento de carga](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Altere o **Intervalo de Investigação** para 5 segundos e salve suas alterações.

![Alterar intervalo de investigação](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>Validar o cluster
O trabalho pesado está feito. O cluster agora deve estar acessível em `mariadbha.cloudapp.net:3306`, que atinge o balanceador de carga e redireciona as solicitações entre as três VMs de forma fluente e eficaz.

Use seu cliente MySQL favorito para conectar ou conecte-se de uma VMs para verificar se o cluster está funcionando.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Em seguida, crie um novo banco de dados e popule-o com alguns dados.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

O banco de dados que você criou retorna a tabela a seguir:

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um cluster MariaDB + Galera de três nós altamente disponível nas Máquinas Virtuais do Azure que executa o CentOS 7. As VMs passaram pelo balanceamento de carga com o Azure Load Balancer.

Pode ser interessante ver [outra maneira de clusterizar o MySQL no Linux](mysql-cluster.md) e nas formas de [otimizar e testar o desempenho do MySQL nas VMs Linux do Azure](optimize-mysql.md).

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[criar uma chave SSH para autenticação]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
