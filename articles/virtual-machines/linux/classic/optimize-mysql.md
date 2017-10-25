---
title: Otimizar o desempenho do MySQL no Linux | Microsoft Docs
description: "Aprenda a otimizar o MySQL em execução em uma máquina virtual do Azure (VM) executando o Linux."
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0c1c7fc5-a528-4d84-b65d-2df225f2233f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: ningk
ms.openlocfilehash: 8f2ec884fa98e989448ac11675e71f39aa21fa7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Otimizar o desempenho do MySQL em VMs Linux do Azure
Há muitos fatores que afetam o desempenho do MySQL no Azure, tanto na configuração de software como na seleção de hardware virtual. Este artigo se concentra na otimização de desempenho por meio de armazenamento, sistema e configurações de banco de dados.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager](../../../resource-manager-deployment-model.md) e Clássico. Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para saber mais sobre as otimizações de VM Linux com o modelo do Resource Manager, veja [Otimizar sua VM Linux no Azure](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Utilizar RAID em uma máquina virtual do Azure
O armazenamento é o principal fator que afeta o desempenho do banco de dados em ambientes de nuvem. Em comparação com um único disco, o RAID pode fornecer acesso mais rápido por meio de simultaneidade. Para obter mais informações, consulte [Níveis de RAID padrão](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

A taxa de transferência de E/S de disco e o tempo de resposta de E/S no Azure podem ser melhorados significativamente com o RAID. Nossos testes de laboratório mostram que a taxa de transferência de E/S de disco pode ser duplicada e o tempo de resposta de E/S pode ser reduzido pela metade em média quando o número de discos RAID é duplicado (de dois para quatro, quatro para oito, etc.). Confira o [Apêndice A](#AppendixA) para obter detalhes.  

Além de e/s de disco, o desempenho do MySQL melhora quando você aumenta o nível do RAID.  Confira o [Apêndice B](#AppendixB) para obter detalhes.  

Também pode ser interessante considerar o tamanho da parte. Em geral, quando você tem um grande parte, haverá uma sobrecarga mais baixa, especialmente para grandes gravações. No entanto, quando o tamanho da parte for grande demais, ela pode adicionar uma sobrecarga adicional que impedirá que o RAID seja aproveitado. O tamanho atual padrão é 512 KB, que provou ser o ideal para a maioria dos ambientes de produção gerais. Confira o [Apêndice C](#AppendixC) para obter detalhes.   

Há limites para quantos discos você pode adicionar para diferentes tipos de máquinas virtuais. Esses limites são detalhados em [Tamanhos de máquinas virtuais e serviço de nuvem para o Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Você precisará de quatro discos de dados anexados para seguir o exemplo do RAID neste artigo, embora seja possível optar por configurar o RAID com menos discos.  

Este artigo pressupõe que você já criou uma máquina virtual Linux e já tenha o MYSQL instalado e configurado. Para obter mais informações sobre como começar, consulte Como instalar o MySQL no Azure.  

### <a name="set-up-raid-on-azure"></a>Configurar o RAID no Azure
As etapas a seguir mostram como criar o RAID no Azure usando o portal do Azure. Você também pode configurar o RAID usando scripts do Windows PowerShell.
Neste exemplo, configuraremos o RAID 0 com quatro discos.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>Adicionar um disco de dados à sua máquina virtual
No portal do Azure, vá para o painel e selecione a máquina virtual à qual você deseja adicionar um disco de dados. Neste exemplo, a máquina virtual é mysqlnode1.  

<!--![Virtual machines][1]-->

Clique em **Discos** e depois em **Anexar Novo**.

![Máquinas virtuais - adicionar disco](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

Crie um novo disco de 500 GB. Verifique se a **Preferência de Cache do Host** está definida como **Nenhum**.  Quando tiver terminado, clique em **OK**.

![Anexar disco vazio](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


Isso adiciona um disco vazio à sua máquina virtual. Repita essa etapa mais três vezes para que você tenha quatro discos de dados para o RAID.  

Você pode ver as unidades adicionadas na máquina virtual, observando o log de mensagens do kernel. Por exemplo, para ver isso no Ubuntu, use o seguinte comando:  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>Criar o RAID com os discos adicionais
As etapas a seguir descrevem como [configurar o software RAID no Linux](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Se você estiver usando o sistema de arquivos XFS, execute as etapas abaixo após criar o RAID.
>
>

Para instalar o XFS no Debian, Ubuntu ou Linux Mint, use o seguinte comando:  

    apt-get -y install xfsprogs  

Para instalar o XFS no Fedora, CentOS ou RHEL, use o seguinte comando:  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>Configurar um novo caminho de armazenamento
Use o comando a seguir para configurar um novo caminho de armazenamento:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>Copiar os dados originais para o novo caminho de armazenamento
Use o comando a seguir para copiar os dados para um novo caminho de armazenamento:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Modificar permissões para que o MySQL possa acessar (leitura e gravação) o disco de dados
Use o comando a seguir para modificar permissões:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>Ajustar o algoritmo de agendamento de e/s de disco
O Linux implementa quatro tipos de algoritmos de agendamento de e/s:  

* Algoritmo NOOP (nenhuma operação)
* Algoritmo de prazo (prazo)
* Algoritmo de fila completamente justa (CFQ)
* Algoritmo de período de orçamento (antecipado)  

Você pode selecionar diferentes agendadores de e/s em cenários diferentes para otimizar o desempenho. Em um ambiente de acesso completamente aleatório, não há uma grande diferença entre os algoritmos CFQ e de Data Limite para o desempenho. Recomendamos definir o ambiente de banco de dados do MySQL como Data Limite para fins de estabilidade. Se houver muita E/S sequencial, o CFQ poderá reduzir o desempenho de E/S do disco.   

Para o SSD e outros equipamentos, NOOP ou Data Limite podem obter um desempenho melhor que o agendador padrão.   

Antes do kernel 2.5, o algoritmo de agendamento de E/S padrão era Data Limite. A partir do kernel 2.6.18, o CFQ tornou-se o algoritmo de agendamento de E/S padrão.  Você pode especificar essa configuração no momento da inicialização do kernel ou modificar essa configuração dinamicamente quando o sistema estiver em execução.  

O exemplo a seguir demonstra como verificar e definir o agendador padrão para o algoritmo NOOP na família de distribuição Debian.  

### <a name="view-the-current-io-scheduler"></a>Exibir o agendador de E/S atual
Para exibir o agendador, execute o seguinte comando:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Você verá o seguinte resultado, que indica o agendador atual:  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>Alterar o dispositivo atual (/dev/sda) do algoritmo de agendamento de E/S
Execute os comandos a seguir para alterar o dispositivo atual:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> Configurar este somente para /dev/sda não é útil. Ele deve ser definido em todos os discos de dados nos quais o banco de dados reside.  
>
>

Você verá o seguinte resultado, que indica que grub.cfg foi recriado com êxito e que o agendador padrão foi atualizado para o NOOP:  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Para a família de distribuição Red Hat, apenas o seguinte comando é necessário:

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>Definir as configurações de operações de arquivos do sistema
Uma prática recomendada é desabilitar o recurso de registro em log *atime* no sistema de arquivos. Atime é a hora do último acesso ao arquivo. Sempre que um arquivo é acessado, o sistema de arquivos registra o carimbo de data/hora no log. No entanto, essas informações raramente são usadas. Você pode desabilitá-lo se não precisar dele, o que reduzirá o tempo de acesso total do disco.  

Para desabilitar o log atime, você precisa modificar o arquivo de configuração do sistema de arquivos /etc/ fstab e adicionar a opção **noatime** .  

Por exemplo, edite o arquivo vim /etc/fstab, adicionando o noatime conforme mostrado no exemplo a seguir:  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Em seguida, monte novamente o sistema de arquivos com o seguinte comando:  

    mount -o remount /RAID0

Teste o resultado modificado. Quando você modifica o arquivo de teste, o tempo de acesso não é atualizado. Os exemplos a seguir mostram o código antes e depois da modificação.

Antes:        

![Código antes da modificação de acesso][5]

Depois:

![Código depois da modificação de acesso][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Aumentar o número máximo de manipuladores de sistema para alta simultaneidade
O MySQL é um banco de dados de alta simultaneidade. O número de manipuladores simultâneos é 1024 para Linux, que nem sempre é suficiente. Use as etapas a seguir para aumentar os manipuladores simultâneos máximos do sistema para dar suporte à alta simultaneidade do MySQL.

### <a name="modify-the-limitsconf-file"></a>Modificar o arquivo limits.conf
Para aumentar o máximo de identificadores simultâneos permitido, adicione as quatro linhas a seguir no arquivo /etc/security/limits.conf. Observe que 65536 é o número máximo ao qual o sistema pode dar suporte.   

    * soft nofile 65536
    * hard nofile 65536
    * soft nproc 65536
    * hard nproc 65536

### <a name="update-the-system-for-the-new-limits"></a>Atualizar o sistema para os novos limites
Para atualizar o sistema, execute os seguintes comandos:  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>Assegurar se os limites são atualizados no momento da inicialização
Coloque os comandos de inicialização a seguir no arquivo /etc/rc.local para que ele entre em vigor no momento da inicialização.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>Otimização de banco de dados do MySQL
Para configurar o MySQL no Azure, você pode usar a mesma estratégia de ajuste de desempenho usada em um computador local.  

As principais regras de otimização de e/s são:   

* Aumente o tamanho do cache.
* Reduza o tempo de resposta de E/S.  

Para otimizar as configurações do servidor MySQL, você pode atualizar o arquivo my.cnf, que é o arquivo de configuração padrão para computadores cliente e servidor.  

Os seguintes itens de configuração são os principais fatores que afetam o desempenho do MySQL:  

* **innodb_buffer_pool_size**: o pool de buffers contém dados armazenados em buffer e o índice. Isto geralmente é definido como 70% da memória física.
* **innodb_log_file_size**: esse é o tamanho do redo log. Você pode usar os logs de refazer para garantir que as operações de gravação sejam rápidas, confiáveis e recuperáveis após uma falha. Isto é definido como 512 MB, o que lhe oferecerá bastante espaço para operações de gravação em log.
* **max_connections**: às vezes, os aplicativos não fecham as conexões corretamente. Um valor maior oferece ao servidor mais tempo para reciclar conexões ociosas. O número máximo de conexões é 10.000, porém o máximo recomendado é 5.000.
* **Innodb_file_per_table**: essa configuração habilita ou desabilita a capacidade do InnoDB armazenar as tabelas em arquivos separados. Ative a opção para garantir que várias operações de administração avançadas possam ser aplicadas com eficiência. Do ponto de vista de desempenho, ela pode acelerar a transmissão de espaço de tabela e otimizar o desempenho do gerenciamento de resíduos. A configuração recomendada para esta opção é ON.</br></br>
No MySQL 5.6, a configuração padrão é ON, portanto, nenhuma ação é necessária. Para versões anteriores, a configuração padrão é OFF. A configuração deve ser alterada antes dos dados serem carregados, pois apenas as tabelas recém-criadas são afetadas.
* **innodb_flush_log_at_trx_commit**: o valor padrão é 1, com o escopo definido como 0~2. O valor padrão é a opção mais adequada para o banco de dados do MySQL autônomo. A configuração de 2 habilita mais integridade de dados e é adequada para Mestre no Cluster do MySQL. A configuração de 0 permite a perda de dados, o que pode afetar a confiabilidade (em alguns casos com melhor desempenho) e é adequada para o Subordinado no Cluster do MySQL.
* **Innodb_log_buffer_size**: o buffer de log permite que as transações sejam executadas sem ter que liberar o log no disco antes que as transações sejam confirmadas. No entanto, se houver objetos binários ou campos de texto grandes, o cache será consumido rapidamente e E/S de disco frequente será disparada. É melhor aumentar o tamanho do buffer se a variável de estado Innodb_log_waits não é 0.
* **query_cache_size**: a melhor opção é desabilitá-lo desde o início. Defina query_cache_size como 0 (esta é a configuração padrão no MySQL 5.6) e use outros métodos para agilizar as consultas.  

Consulte [Apêndice D](#AppendixD) para ver uma comparação de desempenho antes e depois da otimização.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Ativar o log de consultas lentas do MySQL para analisar o estrangulamento de desempenho
O log de consultas lentas do MySQL pode ajudá-lo a identificar as consultas lentas para o MySQL. Depois de habilitar o log de consultas lentas do MySQL, você pode usar as ferramentas do MySQL como **mysqldumpslow** para identificar o estrangulamento de desempenho.  

Por padrão, isso não está habilitado. Ativar o log de consultas lentas pode consumir alguns recursos da CPU. É recomendável que você habilite isso temporariamente para solucionar problemas de afunilamento de desempenho. Para ativar o log de consultas lentas:

1. Modificar o arquivo my.cnf adicionando as seguintes linhas ao final:

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. Inicie o MySQL Server.

        service  mysql  restart

3. Verifique se a configuração está em vigor usando o comando **show**.

![Slow-query-log ON][7]   

![Slow-query-log results][8]

Neste exemplo, você pode ver que o recurso de consulta lenta foi ativado. Você pode usar a ferramenta **mysqldumpslow** para determinar os estrangulamentos de desempenho e otimizar o desempenho, como a adição de índices.

## <a name="appendices"></a>Anexos
A seguir estão os dados do teste de desempenho de exemplo produzidos em um ambiente de laboratório de destino. Eles oferecem informações gerais sobre a tendência dos dados de desempenho com diferentes abordagens de ajuste de desempenho. Os resultados podem variar em versões diferentes de ambiente ou produto.

### <a name="AppendixA"></a>Apêndice A  
**Desempenho de disco (IOPS) com diferentes níveis de RAID**

![IOPS de Disco com diferentes níveis de RAID][9]

**Comandos de teste**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> A carga de trabalho deste teste usa 64 threads, tentando alcançar o limite superior do RAID.
>
>

### <a name="AppendixB"></a>Apêndice B  
**Comparação de desempenho (taxa de transferência) do MySQL com diferentes níveis de RAID**   
(sistema de arquivos XFS)

![Comparação de desempenho do MySQL com diferentes níveis de RAID][10]  
![Comparação de desempenho do MySQL com diferentes níveis de RAID][11]

**Comandos de teste**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Comparação de desempenho (OLTP) do MySQL com diferentes níveis de RAID**  
![Comparação de desempenho (OLTP) do MySQL com diferentes níveis de RAID][12]

**Comandos de teste**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>Apêndice C   
**Comparação de desempenho de disco (IOPS) para diferentes tamanhos de partes**  
(sistema de arquivos XFS)

![][13]

**Comandos de teste**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Os tamanhos de arquivos usados nesse teste são 30 GB e 1 GB respectivamente, com sistema de arquivos XFS de RAID 0 (4 discos).

### <a name="AppendixD"></a>Apêndice D  
**Comparação de desempenho do MySQL (taxa de transferência) antes e depois da otimização**  
(sistema de arquivos XFS)

![Comparação de desempenho do MySQL (taxa de transferência) antes e depois da otimização][14]

**Comandos de teste**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**A definição de configuração para padrão e otimização é a seguinte:**

| parâmetros | Padrão | Otimização |
| --- | --- | --- |
| **innodb_buffer_pool_size** |Nenhum |7 GB |
| **innodb_log_file_size** |5 MB |512 MB |
| **max_connections** |100 |5.000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

Para ver [parâmetros de configuração de otimização](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html) mais detalhados, consulte as [Instruções oficiais do MySQL](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method).  

  **Ambiente de teste**  

| Hardware | Detalhes |
| --- | --- |
| CPU |Processador AMD Opteron(tm) 4171 HE/4 cores |
| Memória |14 GB |
| Disco |10 GB/disco |
| SO |Ubuntu 14.04.1 LTS |

[1]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png

