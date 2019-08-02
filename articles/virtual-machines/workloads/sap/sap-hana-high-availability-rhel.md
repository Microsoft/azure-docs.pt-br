---
title: Configurar a replicação de sistema do SAP HANA em VMs (máquinas virtuais) do Azure | Microsoft Docs
description: Estabeleça alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 66e1e4603602835d6ed5be9af58eb09a24b00b63
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709112"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Alta disponibilidade do SAP HANA em VMs do Azure no Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]: https://launchpad.support.sap.com/#/notes/2292690
[2455582]: https://launchpad.support.sap.com/#/notes/2455582
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Para desenvolvimento local, é possível usar a Replicação de Sistema do HANA ou usar o armazenamento compartilhado para estabelecer alta disponibilidade para SAP HANA.
Em VMs (máquinas virtuais) do Azure, a Replicação de Sistema do HANA no Azure é atualmente a única função de alta disponibilidade com suporte.
A replicação do SAP HANA consiste em um nó primário e, pelo menos, um nó secundário. As alterações nos dados do nó primário são replicadas no nó secundário de modo síncrono ou assíncrono.

Este artigo descreve como implantar e configurar as máquinas virtuais, instalar a estrutura de cluster e instalar e configurar a Replicação de Sistema do SAP HANA.
Nas configurações de exemplo, são usados os comandos de instalação, o número da instância **03** a ID do Sistema do HANA **HN1**.

Primeiro, leia os seguintes documentos e Notas SAP:

* A Nota SAP [1928533], que tem:
  * A lista de tamanhos de VM do Azure que têm suporte para a implantação de software SAP.
  * Informações importantes sobre capacidade para tamanhos de VM do Azure.
  * O software SAP e combinações de SO (sistema operacional) e banco de dados com suporte.
  * A versão do kernel do SAP necessária para Windows e Linux no Microsoft Azure.
* A Nota SAP [2015553] lista pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
* Nota SAP [2002167] recomendou configurações do sistema operacional Red Hat Enterprise Linux
* Nota SAP [2009879] tem diretrizes SAP HANA para Red Hat Enterprise Linux
* A Nota SAP [2178632] contém informações detalhadas sobre todas as métricas de monitoramentos relatadas para o SAP no Azure.
* A Nota SAP [2191498] tem a versão necessária do SAP Host Agent para Linux no Azure.
* A Nota SAP [2243692] tem informações sobre o licenciamento do SAP no Linux no Azure.
* A Nota SAP [1999351] tem informações de solução de problemas adicionais para a Extensão de Monitoramento Avançado do Azure para SAP.
* [WIKI da comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as Notas SAP necessárias para Linux.
* [Máquinas virtuais de planejamento e implementação para SAP no Linux do Azure][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux (Este artigo)][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [Replicação do sistema SAP HANA no cluster de marca-passo](https://access.redhat.com/articles/3004101)
* Documentação geral RHEL
  * [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Documentação específica do RHEL do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade do RHEL - máquinas virtuais do Microsoft Azure como membros de cluster](https://access.redhat.com/articles/3131341)
  * [Instalando e configurando um Cluster de alta disponibilidade do Red Hat Enterprise Linux 7.4 (e posterior) no Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Instalar o SAP HANA no Red Hat Enterprise Linux para uso no Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Visão geral

Para obter a alta disponibilidade, o SAP HANA é instalada em duas máquinas virtuais. Os dados são replicados usando a Replicação de Sistema do HANA.

![Visão geral de alta disponibilidade do SAP HANA](./media/sap-hana-high-availability-rhel/ha-hana.png)

A configuração da Replicação de Sistema do SAP HANA usa um nome do host virtual dedicado e endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga:

* Configuração de front-end: Endereço de IP 10.0.0.13 para hn1 db
* Configuração de back-end: Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do HANA System Replication
* Porta de Investigação: Porta 62503
* Regras de balanceamento de carga: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Implantar para Linux

O Azure Marketplace contém uma imagem do Red Hat Enterprise Linux 7.4 para SAP HANA que você pode usar para implantar novas máquinas virtuais.

### <a name="deploy-with-a-template"></a>Implantar com um modelo

É possível usar um dos modelos de início rápido que estão no GitHub para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade, e assim por diante.
Para implantar o modelo, siga estas etapas:

1. Abra o [modelo de banco de dados][template-multisid-db] no portal do Azure.
1. Insira os parâmetros s seguir:
    * **ID do sistema SAP**: Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID é usada como um prefixo para os recursos que serão implantados.
    * **Tipo de SO**: Selecione uma das distribuições do Linux. Neste exemplo, selecione **RHEL 7**.
    * **Tipo de banco de dados**: Selecionar **HANA**.
    * **Tamanho do Sistema SAP**: Insira o número de SAPS que o novo sistema irá fornecer. Se não tiver certeza de quantos SAPS o sistema precisará, pergunte ao Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
    * **Disponibilidade do Sistema**: Selecione **HA**.
    * **Chave de Admin Username, a senha de administrador ou SSH**: Um novo usuário é criado que pode ser usado para fazer logon máquina.
    * **ID da Sub-rede**: Se você deseja implantar a VM em uma rede virtual existente em que você tem uma sub-rede definida para a qual a VM deve ser designada, nomeie a identificação dessa sub-rede específica. Geralmente, a ID é semelhante a **/subscriptions/\<ID da assinatura ID>/resourceGroups/\<nome do grupo de recursos>/providers/Microsoft.Network/virtualNetworks/\<nome da rede virtual>/subnets/\<nome da sub-rede>** . Deixe em branco, se você quiser criar uma nova rede virtual

### <a name="manual-deployment"></a>Implantação manual

1. Crie um grupo de recursos.
1. Crie uma rede virtual.
1. Crie um conjunto de disponibilidade.  
   Defina o máximo de domínio de atualização.
1. Crie um balanceador de carga (interno).
   * Selecione a rede virtual criada na etapa 2.
1. Crie a máquina virtual 1.  
   Use pelo menos Red Hat Enterprise Linux 7.4 para o SAP HANA. Este exemplo usa o Red Hat Enterprise Linux 7.4 para imagem do SAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> selecionar a conjunto criada na etapa 3 de disponibilidade.
1. Crie a máquina virtual 2.  
   Use pelo menos Red Hat Enterprise Linux 7.4 para o SAP HANA. Este exemplo usa o Red Hat Enterprise Linux 7.4 para imagem do SAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> selecionar a conjunto criada na etapa 3 de disponibilidade.
1. Adicione discos de dados.
1. Configure o balanceador de carga. Primeiro, crie um pool de IP de front-end:

   1. Abra o balanceador de carga, selecione **pool de front-end** e selecione **Adicionar**.
   1. Insira o nome do novo pool de IP front-end (por exemplo, **hana-frontend**).
   1. Defina a **Atribuição** para **Estático** e insira o endereço IP (por exemplo, **10.0.0.13**).
   1. Selecione **OK**.
   1. Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.

1. Em seguida, crie um pool de back-end:

   1. Abra o balanceador de carga, selecione **pools de back-end** e selecione **Adicionar**.
   1. Insira o nome do novo pool de back-end (por exemplo, **hana-backend**).
   1. Selecione **Adicionar uma máquina virtual**.
   1. Selecione o conjunto de disponibilidade criado na etapa 3.
   1. Selecione as máquinas virtuais do cluster do SAP HANA.
   1. Selecione **OK**.

1. Em seguida, crie uma investigação de integridade:

   1. Abra o balanceador de carga, selecione **investigações de integridade** e selecione **Adicionar**.
   1. Insira o nome da nova investigação de integridade (por exemplo, **hana-hp**).
   1. Selecione **TCP** como o protocolo e porta 625**03**. Mantenha o valor do **Intervalo** como 5 e o valor **Limite não íntegro** como 2.
   1. Selecione **OK**.

1. Para o SAP HANA 1.0, crie as regras de balanceamento de carga:

   1. Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
   1. Insira o nome da nova regra do balanceador de carga (por exemplo, hana-lb-3**03**15).
   1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **hana-frontend**).
   1. Mantenha o **Protocolo** definido como **TCP** e insira a porta 3**03**15.
   1. Aumente o **tempo limite de ociosidade** para 30 minutos.
   1. Certifique-se de **habilitar IP Flutuante**.
   1. Selecione **OK**.
   1. Repita essas etapas para a porta 3**03**17.

1. Para o SAP HANA 2.0, crie as regras de balanceamento de carga para o banco de dados do sistema:

   1. Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
   1. Insira o nome da nova regra do balanceador de carga (por exemplo, hana-lb-3**03**13).
   1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **hana-frontend**).
   1. Mantenha o **Protocolo** definido como **TCP** e insira a porta 3**03**13.
   1. Aumente o **tempo limite de ociosidade** para 30 minutos.
   1. Certifique-se de **habilitar IP Flutuante**.
   1. Selecione **OK**.
   1. Repita essas etapas para a porta 3**03**14.

1. Para o SAP HANA 2.0, primeiro crie as regras de balanceamento de carga para o banco de dados do locatário:

   1. Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
   1. Insira o nome da nova regra do balanceador de carga (por exemplo hana-lb-3**03**40).
   1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade criados anteriormente (por exemplo, **hana-frontend**).
   1. Mantenha o **Protocolo** definido como **TCP** e insira a porta 3**03**40.
   1. Aumente o **tempo limite de ociosidade** para 30 minutos.
   1. Certifique-se de **habilitar IP Flutuante**.
   1. Selecione **OK**.
   1. Repita essas etapas para portas 3**03**41 e 3**03**42.

Para obter mais informações sobre as portas necessárias para o SAP HANA, leia o capítulo [conexões com bancos de dados de locatário](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) na [bancos de dados de locatário do SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) guia ou [nota SAP 2388694][2388694].

> [!IMPORTANT]
> Não habilite os carimbos de hora do TCP em VMs do Azure colocadas por trás do balanceador de carga do Azure. Habilitar TCP carimbos de hora fará com que as investigações de integridade falha. Defina o parâmetro **net.ipv4.tcp_timestamps** à **0**. Para obter detalhes, consulte [investigações de integridade do balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Consulte também SAP Observação [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Instalar SAP HANA

As etapas nesta seção usam os seguintes prefixos:

* **[A]** : A etapa se aplica a todos os nós.
* **[1]** : A etapa se aplica apenas ao nó 1.
* **[2]** : A etapa se aplica ao nó 2 do cluster do Pacemaker apenas.

1. **[A]** Configurar o layout de disco: discos sem formatação: **Gerenciamento de volumes lógicos (LVM)** .

   É recomendável que você use o LVM para volumes que armazenam dados e arquivos de log. O exemplo a seguir assume que as máquinas virtuais tenham quatro discos de dados anexados que são usados para criar dois volumes.

   Listar todos os discos disponíveis:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Saída de exemplo:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Crie volumes físicos para todos os discos que você quer usar:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Crie um grupo de volumes para os arquivos de dados. Use um grupo de volumes para os arquivos de log e outro para o diretório compartilhado do SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Criar os volumes lógicos. Um volume linear é criado quando você usa `lvcreate` sem a opção `-i`. É recomendável que você crie um volume distribuído para um melhor desempenho de E/S, em que o argumento `-i` deve ser o número do volume físico subjacente. Neste documento, dois volumes físicos são usados para o volume de dados, portanto, o argumento da chave `-i` é definido com **2**. Um volume físico é usado para o volume do log, portanto, nenhuma opção `-i` é usada explicitamente. Use a opção `-i` e defina-a para o número do volume físico subjacente quando você usar mais de um volume físico para cada volume de dados, log ou compartilhado.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Crie os diretórios de montagem e copie o UUID de todos os volumes lógicos:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Crie entradas `fstab` para os três volumes lógicos:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Insira a seguinte linha no arquivo `/etc/fstab`:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Monte os novos volumes:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Configurar o layout de disco: discos sem formatação: **Discos simples**.

   Para sistemas de demonstração, você pode colocar os arquivos de log e dados do HANA em um disco. Crie uma partição em /dev/disk/azure/scsi1/lun0 e formate-a com xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Insira esta linha no arquivo /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Crie o diretório de destino e monte o disco:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Configure a resolução do nome do host para todos os hosts.

   É possível usar um servidor DNS ou modificar o arquivo /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas no arquivo /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao ambiente:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL para configuração do HANA

   Configurar RHEL, conforme descrito na nota do SAP [2292690] e [2455582] e <https://access.redhat.com/solutions/2447641>.

1. **[A]**  Instalar o SAP HANA

   Para instalar a replicação de sistema do SAP HANA, execute <https://access.redhat.com/articles/3004101>.

   * Execute o **hdblcm** programa a partir do DVD do HANA. Insira os valores a seguir no prompt:
   * Escolha instalação: Insira **1**.
   * Selecione os componentes adicionais para instalação: Insira **1**.
   * Insira o caminho de instalação [/hana/shared]: Selecione Enter.
   * Insira o Nome do Host Local: Selecione Enter.
   * Você deseja adicionar outros hosts ao sistema? (s/n) [n]: Selecione Enter.
   * Insira a ID do Sistema SAP HANA: Insira o SID do HANA, por exemplo: **HN1**.
   * Insira o Número da Instância [00]: Insira o número de instância do HANA. Insira **03** se você usou o modelo do Azure ou seguiu a seção de implantação manual deste artigo.
   * Selecione Modo de Banco de Dados/Inserir Índice [1]: Selecione Enter.
   * Selecione o Uso do Sistema/Inserir Índice [4]: Selecione o valor de uso do sistema.
   * Insira o Local dos Volumes de Dados [/hana/data/HN1]: Selecione Enter.
   * Insira o Local dos Volumes de Log [/hana/log/HN1]: Selecione Enter.
   * Restringir a alocação máxima de memória? [n]: Selecione Enter.
   * Insira o Nome do Host do Certificado para o '...' [...]: Selecione Enter.
   * Insira a Senha de Usuário do Agente do Host SAP (sapadm): Insira a senha de usuário do agente de host.
   * Confirme a Senha de Usuário do Agente do Host SAP (sapadm): Insira a senha do usuário do agente de host novamente para confirmar.
   * Insira a Senha do Administrador de Sistema (hdbadm): Insira a senha de administrador do sistema.
   * Confirme a Senha do Administrador de Sistema (hdbadm): Insira a senha do usuário do agente administrador novamente para confirmar.
   * Insira o Diretório Base do Administrador de Sistema [/usr/sap/HN1/home]: Selecione Enter.
   * Insira o Shell de Logon do Administrador de Sistema [/bin/sh]: Selecione Enter.
   * Insira a ID de Usuário do Administrador de Sistema [1001]: Selecione Enter.
   * Insira a ID do Grupo de Usuários (sapsys) [79]: Selecione Enter.
   * Insira a Senha de Usuário do Banco de Dados (SYSTEM): Senha de Usuário do Banco de Dados.
   * Confirme a Senha de Usuário do Banco de Dados (SYSTEM): Insira a senha do usuário novamente para confirmar.
   * Reiniciar o sistema após a reinicialização do computador? [n]: Selecione Enter.
   * Deseja continuar? (s/n): Valide o resumo. Insira **y** para continuar.

1. **[A]** Atualize o Agente de Host do SAP.

   Baixe o arquivo do SAP Host Agent mais recente do [Centro de Software SAP][sap-swcenter] e execute o seguinte comando para atualizar o agente. Substitua o caminho do arquivo para apontar para o arquivo que você baixou:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]**  Configurar o firewall

   Crie a regra de firewall para a porta de investigação do balanceador de carga do Azure.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Configurar a replicação do Sistema SAP HANA 2.0

As etapas nesta seção usam os seguintes prefixos:

* **[A]** : A etapa se aplica a todos os nós.
* **[1]** : A etapa se aplica apenas ao nó 1.
* **[2]** : A etapa se aplica ao nó 2 do cluster do Pacemaker apenas.

1. **[A]**  Configurar o firewall

   Crie regras de firewall para permitir a replicação do sistema HANA e o tráfego do cliente. As portas necessárias estão listadas em [Portas TCP / IP de todos os produtos SAP](https://help.sap.com/viewer/ports). Os comandos a seguir são apenas um exemplo para permitir o tráfego de replicação de sistema do HANA 2.0 e o cliente para o banco de dados SYSTEMDB, HN1 e NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Crie o banco de dados de locatário.

   Se você estiver usando o SAP HANA 2.0 ou o MDC, crie um banco de dados de locatário para o sistema SAP NetWeaver. Substitua **NW1** pelo SID do sistema SAP.

   Executar como < hanasid\>adm o seguinte comando:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Configure a Replicação de Sistema no primeiro nó:

   Fazer backup de bancos de dados, como < hanasid\>adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copie os arquivos PKI do sistema para o site secundário:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Crie o site primário:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configure a Replicação de Sistema no segundo nó:
    
   Registre o segundo nó para iniciar a replicação de sistema. Execute o seguinte comando, como < hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]**  Verificar status de replicação

   Verifique o status de replicação e aguarde até que todos os bancos de dados estão em sincronizado. Se o status permanece desconhecido, verifique suas configurações de firewall.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configurar a replicação do Sistema SAP HANA 1.0

As etapas nesta seção usam os seguintes prefixos:

* **[A]** : A etapa se aplica a todos os nós.
* **[1]** : A etapa se aplica apenas ao nó 1.
* **[2]** : A etapa se aplica ao nó 2 do cluster do Pacemaker apenas.

1. **[A]**  Configurar o firewall

   Crie regras de firewall para permitir a replicação do sistema HANA e o tráfego do cliente. As portas necessárias estão listadas em [Portas TCP / IP de todos os produtos SAP](https://help.sap.com/viewer/ports). Os comandos a seguir são apenas um exemplo para permitir a replicação de sistema do HANA 2.0. Adaptá-lo à sua instalação do SAP HANA 1.0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** Crie os usuários necessários.

   Execute o comando a seguir como raiz. Certifique-se de substituir as cadeias em negrito (ID do Sistema HANA **HN1** e o número da instância **03**) pelos valores da sua instalação do SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Crie a entrada do repositório de chaves.

   Execute o comando a seguir como raiz para criar uma nova entrada de repositório de chaves:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Faça backup do banco de dados.

   Fazer backup de bancos de dados como raiz:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Se você usar uma instalação de vários locatários, faça também backup do banco de dados do locatário:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Configure a Replicação de Sistema no primeiro nó.

   Criar o site primário como < hanasid\>adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configure a Replicação de Sistema no nó secundário.

   Registre o site secundário como < hanasid\>adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Criar um cluster do Pacemaker

Siga as etapas em [Configurando o Pacemaker no Red Hat Enterprise Linux no Azure](high-availability-guide-rhel-pacemaker.md) para criar um cluster básico do Pacemaker para esse servidor HANA.

## <a name="create-sap-hana-cluster-resources"></a>Crie os recursos de cluster do SAP HANA

Instale os agentes de recursos do SAP HANA em **todos os nós**. Certifique-se de habilitar um repositório que contém o pacote.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Em seguida, crie a topologia HANA. Execute os seguintes comandos em um dos nós de cluster do Pacemaker:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> --clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Em seguida, crie os recursos do HANA:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Certifique-se de que o status do cluster está correto e que todos os recursos foram iniciados. Não é importante saber em qual nó os recursos estão sendo executados.

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta seção descreve como é possível testar a configuração. Antes de iniciar um teste, certifique-se de que o Pacemaker não possui nenhuma ação com falha (via status de pcs), não há restrições de local inesperadas (por exemplo, sobras de um teste de migração) e que o HANA é o estado de sincronização, por exemplo com systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testar a migração

Estado do recurso antes de iniciar o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

É possível migrar o nó mestre do SAP HANA executando o comando a seguir:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Se você definir `AUTOMATED_REGISTER="false"`, esse comando deverá migrar o nó mestre do SAP HANA e o grupo que contém o endereço IP virtual para hn1-db-1.

Uma vez que a migração é feita, a saída "sudo pcs status" se parece com isso

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

O recurso do SAP HANA em hn1-db-0 é interrompido. Nesse caso, configure a instância do HANA como secundária, executando este comando:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

A migração cria restrições de local que precisam ser excluídas novamente:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitore o estado do recurso do HANA usando 'pcs status'. Depois que o HANA for iniciado em hn1-db-0, a saída deverá ser semelhante a esta

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testar o agente de isolamento do Azure

Estado do recurso antes de iniciar o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Você pode testar a configuração do agente de fence do Azure desativando a interface de rede no nó em que o SAP HANA está sendo executado como mestre.
Ver [artigo da Base de conhecimento do Red Hat 79523](https://access.redhat.com/solutions/79523) para obter uma descrição sobre como simular uma falha de rede. Neste exemplo, usamos o script net_breaker para bloquear todo o acesso à rede.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

A máquina virtual agora deverá reiniciar ou parar, dependendo da configuração do cluster.
Se você definir a configuração `stonith-action` como desativada, a máquina virtual será interrompida e os recursos migrados para a máquina virtual em execução.

> [!NOTE]
> Ele pode levar até 15 minutos até que as máquinas virtuais esteja online novamente.

Após iniciar a máquina virtual novamente, o recurso SAP HANA não será iniciado como secundário se você definir `AUTOMATED_REGISTER="false"`. Nesse caso, configure a instância do HANA como secundária, executando este comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Estado do recurso após o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testar um failover manual

Estado do recurso antes de iniciar o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Você pode testar um failover manual interrompendo o cluster no nó hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Após o failover, você pode iniciar o cluster novamente. Se você definir `AUTOMATED_REGISTER="false"`, o recurso do SAP HANA no nó hn1-db-0 não será iniciado como secundário. Nesse caso, configure a instância do HANA como secundária, executando este comando:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Estado do recurso após o teste:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>Próximas etapas

* [Máquinas virtuais de planejamento e implementação para o SAP do Azure][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e plano de recuperação de desastre do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastre do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md)
