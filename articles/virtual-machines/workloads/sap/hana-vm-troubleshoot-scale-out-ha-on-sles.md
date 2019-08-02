---
title: Solucionar problemas de instalação do SAP HANA 2.0 expansão HSR Pacemaker com SLES 12 SP3 em máquinas virtuais do Azure | Microsoft Docs
description: Guia para verificar e solucionar problemas de uma configuração de expansão de alta disponibilidade do SAP HANA com base no SAP HANA System Replication (HSR) e no Pacemaker no SLES 12 SP3 em execução nas máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: b794b045efa4be20a63e9996425d69f0212ae0d7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707249"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Verificar e solucionar problemas de instalação de alta disponibilidade de expansão do SAP HANA no SLES 12 SP3 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Este artigo ajuda você a verificar a configuração de cluster do Pacemaker para expansão do SAP HANA executado em máquinas virtuais (VMs) do Azure. A configuração do cluster foi realizada em combinação com SAP HSR (HANA System Replication) e SAPHanaSR-ScaleOut do pacote RPM SUSE. Todos os testes foram realizados em SUSE SLES 12 SP3 apenas. As seções do artigo abrangem diferentes áreas e incluem exemplos de comandos e trechos de arquivos de configuração. Recomendamos essas amostras como um método para verificar e verificar toda a configuração do cluster.



## <a name="important-notes"></a>Observações importantes

Todos os testes para expansão do SAP HANA em combinação com a replicação de sistema do SAP HANA e o Pacemaker foi feita somente com o SAP HANA 2.0. A versão do sistema operacional era SUSE Linux Enterprise Server 12 SP3 para aplicativos SAP. O pacote RPM mais recente, SAPHanaSR-ScaleOut do SUSE, foi usado para configurar o cluster do Pacemaker.
SUSE publicado uma [descrição detalhada do que essa configuração de otimização de desempenho][sles-hana-scale-out-ha-paper].

Para tipos de máquinas virtuais que têm suporte para expansão do SAP HANA, verifique as [diretório de IaaS certificadas pelo SAP HANA][sap-hana-iaas-list].

Houve um problema técnico com expansão do SAP HANA em combinação com várias sub-redes e vNICs e configuração de HSR. É obrigatório usar os patches mais recentes do SAP HANA 2.0 em que esse problema foi corrigido. Há suporte para as seguintes versões do SAP HANA: 

* rev2.00.024.04 ou superior 
* rev2.00.032 ou superior

Se você precisar de suporte do SUSE, siga este [guia][suse-pacemaker-support-log-files]. Colete todas as informações sobre o cluster do SAP HANA alta disponibilidade (HA), conforme descrito no artigo. Suporte do SUSE precisa dessas informações para análise posterior.

Durante os testes internos, a configuração do cluster foi confundida por um encerramento normal e normal da VM por meio do portal do Azure. Portanto, recomendamos que você teste um failover de cluster por outros métodos. Use métodos como forçar um pânico do kernel, ou desligar as redes ou migrar o recurso **msl**. Consulte os detalhes nas seções a seguir. A suposição é que um desligamento padrão acontece de modo intencional. O melhor exemplo de um desligamento intencional é para manutenção. Consulte os detalhes [manutenção planejada](#planned-maintenance).

Além disso, durante os testes internos, a configuração do cluster ficou confusa após uma aquisição manual do SAP HANA enquanto o cluster estava no modo de manutenção. Recomendamos que você o retorne manualmente antes de finalizar o modo de manutenção do cluster. Outra opção é acionar um failover antes de colocar o cluster no modo de manutenção. Para obter mais informações, consulte [Manutenção planejada](#planned-maintenance). A documentação do SUSE descreve como você pode redefinir o cluster dessa forma usando o comando **crm**. Mas a abordagem mencionada anteriormente foi robusta durante os testes internos e nunca mostrou efeitos colaterais inesperados.

Quando você usa o comando **crm migrate**, certifique-se de limpar a configuração do cluster. Adiciona restrições de localização das quais você pode não estar ciente. Essas restrições afetam o comportamento do cluster. Veja mais detalhes em [Manutenção planejada](#planned-maintenance).



## <a name="test-system-description"></a>Descrição do sistema de teste

 Para a verificação e certificação de HA de expansão do SAP HANA, foi usada uma configuração. Consistia em dois sistemas com três nós do SAP HANA: um mestre e dois trabalhadores. A tabela a seguir lista nomes de VM e endereços IP internos. Todas as amostras de verificação que se seguem foram feitas nessas VMs. Usando esses nomes de VM e endereços IP nas amostras de comando, você pode reconhecer melhor os comandos e suas saídas:


| Tipo de nó | Nome da VM | Endereço IP |
| --- | --- | --- |
| Nó mestre no site 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Nó de trabalho 1 no site 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Nó de trabalho 2 no site 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Nó mestre no site 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Nó de trabalho 1 no site 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Nó de trabalho 2 no site 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Nó do criador da maioria | hso-hana-dm | 10.0.0.13 |
| Servidor do dispositivo SBD | hso-hana-sbd | 10.0.0.19 |
| | | |
| Servidor NFS 1 | hso-nfs-vm-0 | 10.0.0.15 |
| Servidor NFS 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Várias sub-redes e vNICs

Seguindo as recomendações de rede do SAP HANA, três sub-redes foram criadas em uma rede virtual do Azure. A expansão do SAP HANA no Azure deve ser instalado no modo não compartilhado. Isso significa que cada nó usa volumes de disco locais para **/hana/data** e **/hana/log**. Como os nós usam apenas volumes de disco locais, não é necessário definir uma sub-rede separada para armazenamento:

- 10.0.2.0/24 para comunicação internode SAP HANA
- 10.0.1.0/24 para o SAP HANA System Replication (HSR)
- 10.0.0.0/24 para todo o resto

Para obter informações sobre a configuração do SAP HANA relacionada ao uso de várias redes, consulte [SAP HANA global.ini](#sap-hana-globalini).

Cada VM no cluster possui três vNICs que correspondem ao número de sub-redes. [Como criar uma máquina virtual Linux no Azure com rede várias placas de interface][azure-linux-multiple-nics] describes a potential routing issue on Azure when deploying a Linux VM. This specific routing article applies only for use of multiple vNICs. The problem is solved by SUSE per default in SLES 12 SP3. For more information, see [Multi-NIC with cloud-netconfig in EC2 and Azure][suse-cloud-netconfig].


Para verificar se o SAP HANA está configurado corretamente para usar várias redes, execute os seguintes comandos. Primeiro, verifique no nível do sistema operacional que todos os três endereços IP internos das três sub-redes estão ativos. Se você definiu as sub-redes com diferentes intervalos de endereços IP, terá que adaptar os comandos:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

A saída de amostra a seguir é do segundo nó de trabalho no site 2. Você pode ver três endereços IP internos diferentes de eth0, eth1 e eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Em seguida, verifique as portas do SAP HANA para o servidor de nomes e o HSR. O SAP HANA deve escutar nas sub-redes correspondentes. Dependendo do número de instância do SAP HANA, você precisa adaptar os comandos. Para o sistema de teste, o número da instância era **00**. Existem diferentes maneiras de descobrir quais portas são usadas. 

A seguinte instrução SQL retorna o ID da instância, o número da instância e outras informações:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Para encontrar os números de porta corretos, você pode procurar, por exemplo, no HANA Studio em **Configuração** ou por meio de uma instrução SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Para localizar cada porta que é usada na pilha de software SAP, incluindo o SAP HANA, pesquise [portas de TCP/IP de todos os produtos SAP][sap-list-port-numbers].

Dado o número da instância **00** no sistema de teste do SAP HANA 2.0, o número da porta para o servidor de nomes é **30001**. É o número da porta para comunicação de metadados HSR **40002**. Uma opção é entrar um nó de trabalho e, em seguida, verificar os serviços do nó mestre. Para este artigo, verificamos o nó do trabalhador 2 no site 2 tentando se conectar ao nó mestre no site 2.

Verifique a porta do servidor de nomes:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Para provar que a comunicação do internode usa a sub-rede **10.0.2.0/24**, o resultado deve se parecer com a saída de amostra a seguir.
Apenas a conexão via sub-rede **10.0.2.0/24** deve ser bem-sucedida:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Verificar agora para a porta HSR **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Para provar que a comunicação HSR usa a sub-rede **10.0.1.0/24**, o resultado deve se parecer com a saída de amostra a seguir.
Apenas a conexão via sub-rede **10.0.1.0/24** deve ter sucesso:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


O arquivo de configuração **corosync** precisa estar correto em todos os nós do cluster, incluindo o nó criador da maioria. Se a junção do cluster de um nó não funcionar como esperado, crie ou copie **/etc/corosync/corosync.conf** manualmente em todos os nós e reinicie o serviço. 

O conteúdo do **corosync.conf** do sistema de teste é um exemplo.

A primeira seção é **totem**, conforme descrito em [Instalação do cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation), etapa 11. Você pode ignorar o valor para **mcastaddr**. Apenas mantenha a entrada existente. As entradas para **token** e **consenso** deve ser definida de acordo com a [documentação do SAP HANA no Microsoft Azure][sles-pacemaker-ha-guide].

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

A segunda seção, **logging**, não foi alterada a partir dos padrões fornecidos:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

A terceira seção mostra a **nodelist**. Todos os nós do cluster precisam aparecer com o **nodeid**:

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

Na última seção, **quorum**, é importante definir o valor para **expected_votes** corretamente. Deve ser o número de nós, incluindo o nó do criador da maioria. E o valor para **two_node** deve ser **0**. Não remova a entrada completamente. Basta definir o valor como **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Reinicie o serviço por meio de **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>Dispositivo SBD

Como configurar um dispositivo SBD em uma VM do Azure é descrito em [ SBD fence ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing).

Primeiro, verifique a VM do servidor do SBD se houver entradas da ACL para cada nó no cluster. Execute o seguinte comando na VM do servidor SBD:


<pre><code>
targetcli ls
</code></pre>


No sistema de teste, a saída do comando se parece com a amostra a seguir. Nomes de ACL como **iqn.2006-04.hso-db-0.local: hso-db-0** devem ser inseridos como os nomes de iniciadores correspondentes nas VMs. Cada VM precisa de um diferente.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Em seguida, verifique se os nomes dos iniciadores em todas as VMs são diferentes e correspondem às entradas mostradas anteriormente. Este exemplo é do nó 1 do trabalhador no site 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

A saída se parece com o exemplo a seguir:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Em seguida, verifique se a **descoberta** funciona corretamente. Execute o seguinte comando em cada nó do cluster usando o endereço IP da VM do servidor SBD:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

A saída deve se parecer com o exemplo a seguir:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

O próximo ponto de prova é verificar se o nó vê o dispositivo SDB. Verifique-o em cada nó, incluindo o nó do criador da maioria:

<pre><code>
lsscsi | grep dbhso
</code></pre>

A saída deve se parecer com a amostra a seguir. No entanto, os nomes podem ser diferentes. O nome do dispositivo também pode mudar após a reinicialização da VM:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

Dependendo do status do sistema, às vezes, ajuda a reiniciar os serviços iSCSI para resolver problemas. Em seguida, execute os comandos a seguir:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


De qualquer nó, você pode verificar se todos os nós estão **limpos**. Certifique-se de usar o nome do dispositivo correto em um nó específico:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

A saída deve mostrar **limpo** para cada nó no cluster:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Outra verificação do SBD é a opção **dump** do comando **sbd**. Neste exemplo de comando e saída do nó fabricante majoritário, o nome do dispositivo era **sdd**, não **sdm**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

A saída, além do nome do dispositivo deve ser a mesma em todos os nós:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

Mais uma verificação de SBD é a possibilidade de enviar uma mensagem para outro nó. Para enviar uma mensagem ao nó 2 do trabalhador no site 2, execute o seguinte comando no nó 1 do trabalhador no site 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

No lado da VM de destino, **hso-hana-vm-s2-2** neste exemplo, você pode encontrar a seguinte entrada em **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Verifique se as entradas em **/etc/sysconfig/sbd** correspondem à descrição em [Configurando o Pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing). Verifique se a configuração de inicialização em **/etc/iscsi/iscsid.conf** está definida como automática.

As seguintes entradas são importantes em **/etc/sysconfig/sbd**. Adapte o valor **id**, se necessário:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Verifique a configuração de inicialização em **/etc/iscsi/iscsid.conf**. A configuração necessária deveria ter acontecido com o seguinte comando **iscsiadm**, descrito na documentação. Verifique e adaptá-lo manualmente com **vi** se ele for diferente.

Este comando define o comportamento de inicialização:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Verifique esta entrada no **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Durante os testes e verificações, após o reinício de uma VM, o dispositivo SBD não era mais visível em alguns casos. Houve uma discrepância entre a configuração de inicialização e o que o YaST2 mostrou. Para verificar as configurações, siga estas etapas:

1. Inicie o YaST2.
2. Selecione **Serviços de Rede** no lado esquerdo.
3. Role para baixo no lado direito até **Iniciador iSCSI** e selecione-o.
4. Na próxima tela, na guia **Serviço**, você verá o nome do iniciador exclusivo para o nó.
5. Acima do nome do iniciador, verifique se o valor **Service Start** está definido como **Ao inicializar**.
6. Se não estiver, defina-o como **inicializar quando** em vez de **manualmente**.
7. Em seguida, mude a guia superior para **Targets conectados**.
8. Na tela **Destinos Conectados**, você deve ver uma entrada para o dispositivo SBD como este exemplo: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Verifique se o valor **Start-Up** está definido como **na inicialização**.
10. Se não, escolha **editar** e alterá-lo.
11. Salve as alterações e sair do YaST2.



## <a name="pacemaker"></a>Marca-Passo

Depois que tudo estiver configurado corretamente, você poderá executar o seguinte comando em cada nó para verificar o status do serviço Pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

A parte superior da saída deve se parecer com a amostra a seguir. É importante que o status após **Ativo** seja mostrado como **carregado** e **ativo (em execução)** . O status após **Loaded** deve ser mostrado como **ativado**.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Se a configuração ainda estiver em **desativada**, execute o seguinte comando:

<pre><code>
systemctl enable pacemaker
</code></pre>

Para ver todos os recursos configurados no Pacemaker, execute o seguinte comando:

<pre><code>
crm status
</code></pre>

A saída deve se parecer com a amostra a seguir. É bom que os recursos **cln** e **msl** sejam mostrados como parados na VM criadora da maioria, **hso-hana-dm**. Não há nenhuma instalação do SAP HANA no nó principal do criador. Portanto, os recursos **cln** e **msl** são mostrados como interrompidos. É importante que ele mostre o número total correto de VMs, **7**. Todas as VMs que fazem parte do cluster devem ser listadas com o status **Online**. O nó principal primário atual deve ser reconhecido corretamente. Neste exemplo, ele tem **hso-hana-vm-s1-0**:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Uma característica importante do Pacemaker é o modo de manutenção. Nesse modo, você pode fazer modificações sem provocar uma ação de cluster imediata. Um exemplo é uma reinicialização da VM. Um caso de uso típico seria a manutenção planejada da infraestrutura do sistema operacional ou do Azure. Ver [manutenção planejada](#planned-maintenance). Use o seguinte comando para colocar o Pacemaker no modo de manutenção:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Quando você verifica com **status crm**, você percebe na saída que todos os recursos estão marcados como **não gerenciados**. Nesse estado, o cluster não reage a nenhuma alteração como iniciar ou interromper o SAP HANA.
A amostra a seguir mostra a saída do comando **crm status** enquanto o cluster está no modo de manutenção:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Este exemplo de comando mostra como finalizar o modo de manutenção do cluster:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Outro comando **crm** obtém a configuração completa do cluster em um editor, para que você possa editá-lo. Depois de salvar as alterações, o cluster inicia as ações apropriadas:

<pre><code>
crm configure edit
</code></pre>

Para observar a configuração completa do cluster, use a opção **crm show**:

<pre><code>
crm configure show
</code></pre>



Após falhas de recursos de cluster, o **status de crm** comando mostra uma lista de **ações de falha**. Veja o seguinte exemplo desta saída:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

É necessário fazer uma limpeza de cluster após falhas. Use o comando **crm** novamente e use a opção de comando **cleanup** para se livrar dessas entradas de ação com falha. Nomeie o recurso de cluster correspondente da seguinte maneira:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

O comando deve retornar a saída como o exemplo a seguir:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Failover ou a tomada de controle

Conforme discutido em [Notas importantes](#important-notes), você não deve usar um desligamento padrão para testar o failover do cluster ou o controle SAP HANA HSR. Em vez disso, recomendamos que você acione um pânico do kernel, forçar uma migração de recurso ou, possivelmente, encerrar todas as redes no nível do SO de uma VM. Outro método é o comando **crm\< node \>standby**. Consulte a [documento SUSE][sles-12-ha-paper]. 

Os três comandos de amostra a seguir podem forçar um failover de cluster:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Conforme descrito em [Manutenção planejada](#planned-maintenance), uma boa maneira de monitorar as atividades do cluster é executar **SAPHanaSR-showAttr** com o comando **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Também ajuda a observar o status da paisagem do SAP HANA proveniente de um script SAP Python. A configuração do cluster está procurando esse valor de status. Fica claro quando você pensa em uma falha no nó do trabalhador. Se um nó de trabalho ficar inativo, o SAP HANA não retornará imediatamente um erro para a integridade de todo o sistema de expansão. 

Há algumas tentativas para evitar failovers desnecessários. O cluster reage apenas se o status for alterado de **Ok**, retornar valor **4**, para **erro**, retornar valor **1**. Para que ela esteja correta se a saída do **SAPHanaSR showAttr** mostra uma VM com o estado **offline**. Mas ainda não há atividade para alternar entre primário e secundário. Nenhuma atividade do cluster é disparada enquanto o SAP HANA não retornar um erro.

Você pode monitorar o status de integridade da paisagem do SAP HANA como o usuário **\<HANA SID\>adm** chamando o script SAP Python da seguinte maneira. Talvez seja necessário adaptar o caminho:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

A saída desse comando deve parecer com o exemplo a seguir. A coluna **Host Status** e o **status geral do host** são importantes. A saída real é mais ampla, com colunas adicionais.
Para tornar a tabela de saída mais legível neste documento, a maioria das colunas no lado direito foi retirada:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Há outro comando para verificar as atividades atuais do cluster. Veja o seguinte comando e a cauda de saída depois que o nó mestre do site primário foi eliminado. Você pode ver a lista de ações de transição como **promovendo** o antigo nó mestre secundário, **hso-hana-vm-s2-0**, como o novo mestre principal. Se tudo estiver bem e todas as atividades estiverem concluídas, esta lista **Resumo da transição** deve estar vazia.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Manutenção planejada 

Há diferentes casos de uso que dizem respeito à manutenção planejada. Uma questão é se é apenas manutenção de infraestrutura, como alterações no nível do sistema operacional e na configuração do disco ou uma atualização do HANA.
Você pode encontrar informações adicionais em documentos do SUSE, como [no tempo de inatividade Zero][sles-zero-downtime-paper] or [SAP HANA SR Performance Optimized Scenario][sles-12-for-sap]. Esses documentos também incluem amostras que mostram como migrar manualmente um primário.

Intensos testes internos foram feitos para verificar o caso de uso de manutenção de infraestrutura. Para evitar problemas relacionados à migração do primário, decidimos sempre migrar um primário antes de colocar um cluster no modo de manutenção. Dessa forma, não é necessário fazer com que o cluster esqueça a situação anterior: qual lado era primário e qual era secundário.

Há duas situações diferentes nesse sentido:

- **Manutenção planejada no secundário atual**. Nesse caso, você pode simplesmente colocar o cluster no modo de manutenção e executar o trabalho no secundário sem afetar o cluster.

- **Manutenção planejada no primário atual**. Para que os usuários possam continuar a trabalhar durante a manutenção, você precisa forçar um failover. Com essa abordagem, você deve acionar o failover do cluster pelo Pacemaker e não apenas no nível do HSR do SAP HANA. A configuração do Pacemaker aciona automaticamente o controle do SAP HANA. Você também precisa realizar o failover antes de colocar o cluster no modo de manutenção.

O procedimento para manutenção no site secundário atual é o seguinte:

1. Coloque o cluster no modo de manutenção.
2. Realize o trabalho no site secundário. 
3. Finalize o modo de manutenção do cluster.

O procedimento para manutenção no site primário atual é mais complexo:

1. Acionar manualmente um failover ou aquisição do SAP HANA por meio de uma migração de recurso do marcapasso. Veja os detalhes a seguir.
2. O SAP HANA no antigo site primário é encerrado pela configuração do cluster.
3. Coloque o cluster no modo de manutenção.
4. Após a conclusão do trabalho de manutenção, registre o antigo primário como o novo site secundário.
5. Limpe a configuração do cluster. Veja os detalhes a seguir.
6. Finalize o modo de manutenção do cluster.


Migrar um recurso adiciona uma entrada à configuração do cluster. Um exemplo é forçar um failover. Você precisa limpar essas entradas antes de finalizar o modo de manutenção. Veja o exemplo a seguir.

Primeiro, force um failover de cluster migrando o recurso **msl** para o nó principal secundário atual. Esse comando fornece um aviso de que um **mover restrição** foi criado:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Verifique o processo de failover por meio do comando **SAPHanaSR-showAttr**. Para monitorar o status do cluster, abra uma janela shell dedicada e inicie o comando com **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

A saída deve mostrar o failover manual. O primeiro nó mestre secundário foi **promovido**, nesta amostra, **hso-hana-vm-s2-0**. O primeiro site primário foi parado, **lss** valor **1** para o nó mestre primário anterior **hso-hana-vm-s1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Após o failover do cluster e o controle do SAP HANA, coloque o cluster no modo de manutenção, conforme descrito em [Marcapasso](#pacemaker).

Os comandos **SAPHanaSR showAttr** e **crm status** não indicam algo sobre as restrições criadas pela migração de recursos. Uma opção para tornar essas restrições visíveis é mostrar a configuração do recurso de cluster completa com o seguinte comando:

<pre><code>
crm configure show
</code></pre>

Dentro da configuração de cluster, você deve encontrar uma nova restrição de local causada pela antiga migração de recurso manual. Esta entrada de exemplo começa com **location cli-** :

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Infelizmente, essas restrições podem afetar o comportamento geral do cluster. Portanto, é obrigatório removê-los novamente antes de trazer todo o sistema de volta. Com o comando **unmigrate**, é possível limpar as restrições de localização que foram criadas antes. A nomenclatura pode ser um pouco confusa. Ele não tenta migrar o recurso de volta para a VM original a partir da qual foi migrado. Apenas remove as restrições de localização e também retorna informações correspondentes quando você executa o comando:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

No final do trabalho de manutenção, você interrompe o modo de manutenção do cluster, conforme mostrado no [Marcapasso](#pacemaker).



## <a name="hbreport-to-collect-log-files"></a>hb_report para coletar arquivos de log

Para analisar os problemas do cluster do Pacemaker, é útil e também solicitado pelo suporte do SUSE para executar o utilitário **hb_report**. Coleta todos os arquivos de log importantes que você precisa para analisar o que aconteceu. Essa chamada de amostra usa um horário de início e término em que ocorreu um incidente específico. Veja também [Notas importantes](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

O comando informa onde colocar os arquivos de log compactados:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Você pode então extrair os arquivos individuais por meio do comando **tar** padrão:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Quando você olha para os arquivos extraídos, você encontra todos os arquivos de log. A maioria deles foi colocada em diretórios separados para cada nó no cluster:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


Dentro do intervalo de tempo especificado, o nó principal atual **hso-hana-vm-s1-0** foi eliminado. Você pode encontrar entradas relacionadas a este evento no **journal.log**:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Outro exemplo é o arquivo de log do Pacemaker no mestre secundário, que se tornou o novo mestre primário. Este trecho mostra que o status do nó principal primário morto foi definido como **off-line**:

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>global.ini do SAP HANA


Os trechos a seguir são do arquivo SAP HANA **global.ini** no site de cluster 2. Este exemplo mostra as entradas de resolução do nome do host para usar redes diferentes para comunicação de internode do SAP HANA e HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>HAWK

A solução de cluster fornece uma interface de navegador que oferece uma GUI para usuários que preferem menus e gráficos para ter todos os comandos no nível do shell.
Para usar a interface do navegador, substitua **\<nó\>** por um nó real do SAP HANA no seguinte URL. Em seguida, insira as credenciais do cluster (cluster **do usuário**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Esta captura de tela mostra o painel do cluster:


![Painel do cluster HAWK](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Este exemplo mostra as restrições de localização causadas por uma migração de recurso de cluster, conforme explicado em [Manutenção planejada](#planned-maintenance):


![Restrições de lista de falcão](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Você também pode fazer upload da saída **hb_report** no Hawk, abaixo do **Histórico**, mostrado a seguir. Consulte hb_report para coletar arquivos de log: 

![Saída do HAWK upload hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Com o **Explorador Histórico**, você pode percorrer todas as transições do cluster incluídas na saída **hb_report**:

![Transições de hawk na saída hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Esta captura de tela final mostra a seção **Detalhes** de uma única transição. O cluster reagiu em uma falha do nó principal primário, nó **hso-hana-vm-s1-0**. Agora, promovendo o nó secundário como o novo mestre, é **hso-hana-vm-s2-0**:

![HAWK única transição](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Próximas etapas

Este guia de solução de problemas descreve a alta disponibilidade do SAP HANA em uma configuração de expansão. Além do banco de dados, outro componente importante em um cenário SAP é a pilha do SAP NetWeaver. Saiba mais sobre [alta disponibilidade para SAP NetWeaver em máquinas virtuais do Azure que usam o SUSE Enterprise Linux Server][sap-nw-ha-guide-sles].

