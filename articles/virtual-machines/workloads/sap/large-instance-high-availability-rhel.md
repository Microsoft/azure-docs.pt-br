---
title: Alta disponibilidade de instâncias grandes do Azure para SAP no RHEL
description: Saiba como automatizar um failover de banco de dados SAP HANA usando um cluster pacemaker no Red Hat Enterprise Linux.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 99e9994d01e4579bf6ef2e369e0fe85c48af52ef
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182427"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Alta disponibilidade de instâncias grandes do Azure para SAP no RHEL

Neste artigo, você aprenderá a configurar o cluster pacemaker no RHEL 7,6 para automatizar um failover de banco de dados SAP HANA. Você precisa ter uma boa compreensão do Linux, SAP HANA e pacemaker para concluir as etapas neste guia.

A tabela a seguir inclui os nomes de host que são usados em todo este artigo. Os blocos de código no artigo mostram os comandos que precisam ser executados, bem como a saída desses comandos. Preste muita atenção ao nó que é referenciado em cada comando.

| Digite | Nome do host | Nó|
|-------|-------------|------|
|Host primário|`sollabdsm35`|nó 1|
|Host secundário|`sollabdsm36`|nó 2|

## <a name="configure-your-pacemaker-cluster"></a>Configurar o cluster pacemaker


Antes de começar a configurar o cluster, configure a troca de chaves SSH para estabelecer a confiança entre os nós.

1. Use os comandos a seguir para criar idênticos `/etc/hosts` em ambos os nós.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    0.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    0.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    0.20.251.150 sollabdsm36-st

    10.20.251.151 sollabdsm35-st

    

    10.20.252.151 sollabdsm36-back

    10.20.252.150 sollabdsm35-back

    

    10.20.253.151 sollabdsm36-node

    10.20.253.150 sollabdsm35-node

    ```

2.  Crie e troque as chaves SSH.
    1. Gerar chaves SSH.

       ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
       ```
    2. Copie as chaves para os outros hosts para SSH com senha.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Desabilite o SELinux em ambos os nós.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Reinicialize os servidores e, em seguida, use o comando a seguir para verificar o status de SELinux.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. Configure o NTP (protocolo NTP). Os fusos horários e horários para ambos os nós de cluster devem corresponder. Use o comando a seguir para abrir `chrony.conf` e verificar o conteúdo do arquivo.
    1. O conteúdo a seguir deve ser adicionado ao arquivo de configuração. Altere os valores reais de acordo com o seu ambiente.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Habilite o serviço chrony. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. Atualizar o sistema
    1. Primeiro, instale as atualizações mais recentes no sistema antes de começar a instalar o dispositivo SBD.
    1. Se você não quiser uma atualização completa do sistema, mesmo se for recomendado, atualize os pacotes a seguir no mínimo.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```
 

7. Instale os repositórios SAP HANA e RHEL-HA.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Instale as ferramentas pacemaker, SBD, OpenIPMI, ipmitools e fencing_sbd em todos os nós.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitools
    ```

  ## <a name="configure-watchdog"></a>Configurar Watchdog

Nesta seção, você aprenderá a configurar o Watchdog. Esta seção usa os mesmos dois hosts `sollabdsm35` e `sollabdsm36` , referenciados no início deste artigo.

1. Verifique se o daemon Watchdog não está em execução em nenhum sistema.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. O Watchdog do Linux padrão, que será instalado durante a instalação, é o Watchdog iTCO, que não tem suporte dos sistemas UCS e HPE SDFlex. Portanto, esse Watchdog deve ser desabilitado.
    1. O Watchdog errado está instalado e carregado no sistema:
       ```
   
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. Descarregar o driver errado do ambiente:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Para verificar se o driver não está carregado durante a próxima inicialização do sistema, o driver deve ser incluídos. Para bloquear alist dos módulos iTCO, adicione o seguinte ao final do `50-blacklist.conf` arquivo:
       ```
   
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. Copie o arquivo para o host secundário.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. Teste se o serviço IPMI foi iniciado. É importante que o temporizador IPMI não esteja em execução. O gerenciamento de timer será feito do serviço SBD pacemaker.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. Por padrão, o dispositivo necessário é/dev/Watchdog não será criado.

    ```
    No watchdog device was created

    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. Configure o Watchdog do IPMI.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. Copie o arquivo de configuração do Watchdog para secundário.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Habilite e inicie o serviço IPMI.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     Agora, o serviço IPMI é iniciado e o dispositivo/dev/Watchdog é criado – mas o temporizador ainda é interrompido. Posteriormente, o SBD gerenciará a redefinição de Watchdog e habilitará o temporizador IPMI.
7.  Verifique se o/dev/Watchdog existe, mas não está em uso.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>Configuração do SBD
Nesta seção, você aprenderá a configurar o SBD. Esta seção usa os mesmos dois hosts `sollabdsm35` e `sollabdsm36` , referenciados no início deste artigo.

1.  Verifique se o disco iSCSI ou FC está visível em ambos os nós. Este exemplo usa um dispositivo SBD baseado em FC. Para obter mais informações sobre o isolamento SBD, consulte a [documentação de referência](http://www.linux-ha.org/wiki/SBD_Fencing).
2.  O LUN-ID deve ser idêntico em todos os nós.
  
3.  Verifique o status de vários caminhos para o dispositivo SBD.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  Criar os discos SBD e configurar o isolamento primitivo do cluster. Esta etapa deve ser executada no primeiro nó.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  Copie o SBD config para NODE2.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  Verifique se o disco SBD está visível em ambos os nós.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  Adicione o dispositivo SBD no arquivo de configuração SBD.

    ```
    \# SBD_DEVICE specifies the devices to use for exchanging sbd messages

    \# and to monitor. If specifying more than one path, use ";" as

    \# separator.

    \#

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    \## Type: yesno
     Default: yes
     \# Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Inicialização do cluster
Nesta seção, você inicializa o cluster. Esta seção usa os mesmos dois hosts `sollabdsm35` e `sollabdsm36` , referenciados no início deste artigo.

1.  Configure a senha de usuário do cluster (todos os nós).
    ```
    passwd hacluster
    ```
2.  Inicie os PCS em todos os sistemas.
    ```
    systemctl enable pcsd
    ```
  

3.  Interrompa o firewall e desabilite-o em (todos os nós).
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Inicie o serviço PCSD.
    ```
    systemctl start pcsd
    ```
  
  

5.  Execute a autenticação de cluster somente do Node1.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36



        Username: hacluster

            Password:

            sollabdsm35.localdomain: Authorized

            sollabdsm36.localdomain: Authorized

     ``` 

6.  Crie o cluster.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  Verifique o status do cluster.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. Se um nó não estiver ingressando no cluster, verifique se o firewall ainda está em execução.

  

9. Criar e habilitar o dispositivo SBD
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  

10. Interrompa o cluster para reiniciar os serviços de cluster (em todos os nós).

    ```
    pcs cluster stop --all
    ```


11. Reinicie os serviços de cluster (em todos os nós).

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Corosync deve iniciar o serviço SBD.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Reinicie o cluster (se não for iniciado automaticamente a partir de PCSD).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Habilite as configurações de Stonith.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Verifique o novo status do cluster com agora um recurso.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. Agora o temporizador IPMI deve ser executado e o dispositivo/dev/Watchdog deve ser aberto pelo SBD.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm351 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. Verifique o status do SBD.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Teste o isolamento de SBD ao travar o kernel.

    * Disparar a falha do kernel.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * O segundo teste a ser executado é cercar um nó usando comandos de PCS.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. Para o restante do SAP HANA clustering, você pode desabilitar o STONITH definindo:

   * conjunto de propriedades de PCs `stonith-enabled=false`
   * Esse parâmetro deve ser definido como true para uso produtivo. Se esse parâmetro não estiver definido como true, o cluster não terá suporte.
   * conjunto de propriedades de PCs `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>Integração do HANA no cluster

Nesta seção, você integrará o HANA ao cluster. Esta seção usa os mesmos dois hosts `sollabdsm35` e `sollabdsm36` , referenciados no início deste artigo.

Há duas opções para integrar o HANA. A primeira opção é uma solução de custo otimizado em que você pode usar o sistema secundário para executar o sistema QAS. Não recomendamos esse método porque ele deixa nenhum sistema para testar atualizações no software de cluster, no sistema operacional ou no HANA, e as atualizações de configuração podem levar a tempo de inatividade não planejado do sistema PRD. Além disso, se o sistema PRD precisar ser ativado no sistema secundário, o QAS deverá ser desligado no nó secundário. A segunda opção é instalar o sistema QAS em um cluster e usar um segundo cluster para o PRD. Essa opção também permite testar todos os componentes antes que eles sejam colocados em produção. Este artigo mostra como configurar a segunda opção.


* Este processo é a compilação da descrição do RHEL na página:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>Etapas a serem seguidas para configurar o HSR

1.  Essas são as ações a serem executadas em Node1 (primário).
    1. Verifique se o modo de log do banco de dados está definido como normal.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p SAPhana10 -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. SAP HANA replicação do sistema só funcionará após a execução do backup inicial. O comando a seguir cria um backup inicial no `/tmp/` diretório. Selecione um sistema de arquivos de backup apropriado para o banco de dados. 
       ```
       * hdbsql -i 00 -u system -p SAPhana10 "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```
    

    3. Backup de todos os contêineres de banco de dados deste banco de dados.
       ```
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"
   
       
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Habilite o processo HSR no sistema de origem.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Verifique o status do sistema primário.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. Essas são as ações a serem executadas em NODE2 (secundários).
     1. Pare o banco de dados.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Somente para o SAP HANA 2.0, copie o sistema SAP HANA `PKI SSFS_HR2.KEY` e os `SSFS_HR2.DAT` arquivos do nó primário para o nó secundário.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. Habilite o secundário como o site de replicação.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. Inicie o banco de dados.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. Verifique o estado do banco de dados.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. Também é possível obter mais informações sobre o status de replicação:
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>Descrição do modo de replicação de log

Para obter mais informações sobre o modo de replicação de log, consulte a [documentação oficial do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/c039a1a5b8824ecfa754b55e0caffc01.html).
  

#### <a name="network-setup-for-hana-system-replication"></a>Configuração de rede para replicação de sistema do HANA


Para garantir que o tráfego de replicação esteja usando a VLAN certa para a replicação, ele deve ser configurado corretamente no `global.ini` . Se você ignorar essa etapa, o HANA usará a VLAN de acesso para a replicação, que pode não ser desejada.


Os exemplos a seguir mostram a configuração de resolução de nome de host para a replicação do sistema para um site secundário. Três redes distintas podem ser identificadas:

* Rede pública com endereços no intervalo de 10.0.1. *

* Rede para comunicação interna de SAP HANA entre hosts em cada site: 192.168.1. *

* Rede dedicada para replicação de sistema: 10.5.1. *

No primeiro exemplo, o `[system_replication_communication]listeninterface` parâmetro foi definido como `.global` e somente os hosts do site de replicação vizinho são especificados.

No exemplo a seguir, o `[system_replication_communication]listeninterface` parâmetro foi definido como `.internal` e todos os hosts de ambos os sites são especificados.

  

### <a name="source-sap-ag-sap-hana-hrs-networking"></a>Sistema SAP AG de origem SAP HANA hr

  

Para a replicação do sistema, não é necessário editar o `/etc/hosts` arquivo, os nomes de host internos (' virtuais ') devem ser mapeados para os endereços IP no `global.ini` arquivo para criar uma rede dedicada para a replicação do sistema. A sintaxe para isso é a seguinte:

global.ini

[system_replication_hostname_resolution]

<IP-address_site>=<host interno-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Configurar SAP HANA em um cluster pacemaker
Nesta seção, você aprenderá a configurar o SAP HANA em um cluster pacemaker. Esta seção usa os mesmos dois hosts `sollabdsm35` e `sollabdsm36` , referenciados no início deste artigo.

Verifique se você atendeu aos seguintes pré-requisitos:  

* O cluster pacemaker é configurado de acordo com a documentação e tem um isolamento adequado e funcional

* SAP HANA inicialização na inicialização é desabilitada em todos os nós de cluster, pois o início e a parada serão gerenciados pelo cluster

* SAP HANA a replicação do sistema e tomada usando as ferramentas do SAP estão funcionando corretamente entre nós de cluster

* SAP HANA contém a conta de monitoramento que pode ser usada pelo cluster de ambos os nós de cluster

* Ambos os nós são assinados para os canais ' alta disponibilidade ' e ' RHEL for SAP HANA ' (RHEL 6, RHEL 7)

  

* Em geral, execute todos os comandos de PCs somente no nó, pois o CIB será atualizado automaticamente do Shell de PCs.

* [Mais informações sobre a política de quorum](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>Etapas para configurar 
1. Configurar computadores.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Configurar o corosync.
    ```
    https://access.redhat.com/solutions/1293523 --> quorum information RHEL7

    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

1.  Criar recurso de SAPHanaTopology clonado.
    ```
    pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1 interleave=true
    SAPHanaTopology resource is gathering status and configuration of SAP
    HANA System Replication on each node. SAPHanaTopology requires
    following attributes to be configured.



        Attribute Name Description

        SID SAP System Identifier (SID) of SAP HANA installation. Must be
    same for all nodes.

    InstanceNumber 2-digit SAP Instance identifier.
    pcs resource show SAPHanaTopology_HR2_00-clone

    Clone: SAPHanaTopology_HR2_00-clone

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true

        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat
    type=SAPHanaTopology)

        Attributes: InstanceNumber=00 SID=HR2

        Operations: monitor interval=60 timeout=60
    (SAPHanaTopology_HR2_00-monitor-interval-60)

        start interval=0s timeout=180
    (SAPHanaTopology_HR2_00-start-interval-0s)

        stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)

    ```

3.  Criar recurso de SAPHana primário/secundário.

    ```
    SAPHana resource is responsible for starting, stopping and relocating the SAP HANA database. This resource must be run as a Primary/    Secondary cluster resource. The resource has the following attributes.

    

    Attribute Name Required? Default value Description

    SID Yes None SAP System Identifier (SID) of SAP HANA installation. Must be same for all nodes.

    InstanceNumber Yes none 2-digit SAP Instance identifier.

    PREFER_SITE_TAKEOVER

    no yes Should cluster prefer to switchover to secondary instance instead of restarting primary locally? ("no": Do prefer restart locally;   "yes": Do prefer takeover to remote site)

    AUTOMATED_REGISTER no false Should the former SAP HANA primary be registered as secondary after takeover and DUPLICATE_PRIMARY_TIMEOUT?     ("false": no, manual intervention will be needed; "true": yes, the former primary will be registered by resource agent as secondary)

    DUPLICATE_PRIMARY_TIMEOUT no 7200 Time difference (in seconds) needed between primary time stamps, if a dual-primary situation occurs. If   the time difference is less than the time gap, then the cluster holds one or both instances in a "WAITING" status. This is to give an   admin a chance to react on a failover. A failed former primary will be registered after the time difference is passed. After this   registration to the new primary all data will be overwritten by the system replication.
    ```
  

5.  Crie o recurso do HANA.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200   AUTOMATED_REGISTER=true primary notify=true clone-max=2 clone-node-max=1 interleave=true

    pcs resource show SAPHana_HR2_00-primary



    Primary: SAPHana_HR2_00-primary

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true

        Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)

        Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200
    InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2

        Operations: demote interval=0s timeout=320
    (SAPHana_HR2_00-demote-interval-0s)

        monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)

        monitor interval=121 role=Secondary timeout=60
    (SAPHana_HR2_00-monitor-

        interval-121)

        monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-

        interval-119)

        promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)

        start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)

        stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  Criar recurso de endereço IP virtual.

    ```
    Cluster will contain Virtual IP address in order to reach the Primary instance of SAP HANA. Below is example command to create IPaddr2  resource with IP 10.7.0.84/24

    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Criar restrições.

    ```
    For correct operation we need to ensure that SAPHanaTopology resources are started before starting the SAPHana resources and also that  the virtual IP address is present on the node where the Primary resource of SAPHana is running. To achieve this, the following 2    constraints need to be created.

    pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
    pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
    ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>Testando a movimentação manual do recurso SAPHana para outro nó

#### <a name="sap-hana-takeover-by-cluster"></a>(SAP Hana tomada por cluster)


Para testar a movimentação do recurso SAPHana de um nó para outro, use o comando a seguir. Observe que a opção `--primary` não deve ser usada ao executar o comando a seguir devido a como o recurso SAPHana funciona internamente.
```pcs resource move SAPHana_HR2_00-primary```

Após cada recurso de computadores mover a invocação de comando, o cluster cria restrições de local para obter a movimentação do recurso. Essas restrições devem ser removidas para permitir o failover automático no futuro.
Para removê-los, você pode usar o comando a seguir.
```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* Faça logon no HANA como verificação.

  * host rebaixado:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Host promovido:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

Com a opção a `AUTOMATED_REGISTER=false` , você não pode alternar para frente e para trás.

Se essa opção for definida como false, você deverá registrar novamente o nó:

  
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```
  

Agora, NODE2, que era o primário, atua como o host secundário.

Considere definir essa opção como true para automatizar o registro do host rebaixado.

  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true

pcs cluster node clear node1
```
