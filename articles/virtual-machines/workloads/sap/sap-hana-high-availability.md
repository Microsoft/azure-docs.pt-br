---
title: "Alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure | Microsoft Docs"
description: "Estabeleça alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure."
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: sedusch
ms.openlocfilehash: 951150e621d21037b0adde7287b9f985290d8d11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

No local, você pode usar a Replicação do Sistema HANA ou o armazenamento compartilhado para estabelecer alta disponibilidade para SAP HANA.
Atualmente, damos suporte apenas à configuração da Replicação do Sistema HANA no Azure. A Replicação do Sistema HANA consiste em um nó mestre e, pelo menos, um nó subordinado. As alterações de dados no nó mestre são replicadas aos nós subordinados de modo síncrono e assíncrono.

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster, bem como a instalar e configurar a Replicação do Sistema SAP HANA.
Nos exemplos de configurações, comandos de instalação etc., são usados o número de instância 03 e o HDB da ID do Sistema HANA.

Primeiro, leia os seguintes documentos e Notas SAP

* A Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure que têm suporte para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * A versão do kernel do SAP necessária para Windows e para Linux no Microsoft Azure
* A Nota SAP [2015553] lista pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
* A Nota SAP [2205917] tem configurações de SO recomendadas para SUSE Linux Enterprise Server para aplicativos SAP
* A Nota SAP [1944799] tem Diretrizes SAP HANA para SUSE Linux Enterprise Server para aplicativos SAP
* A Nota SAP [2178632] contém informações detalhadas sobre todas as métricas de monitoramentos relatadas para o SAP no Azure.
* A Nota SAP [2191498] tem a versão necessária do SAP Host Agent para Linux no Azure.
* A Nota SAP [2243692] tem informações sobre o licenciamento do SAP no Linux no Azure.
* A Nota SAP [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A Nota SAP [1999351] tem informações de solução de problemas adicionais para a Extensão de Monitoramento Avançado do Azure para SAP.
* [WIKI da comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as Notas SAP necessárias para Linux.
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux (este artigo)][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
* [Cenário otimizado de desempenho de SAP HANA SR][suse-hana-ha-guide] O guia contém todas as informações necessárias para definir a configuração local da Replicação do Sistema SAP HANA. Use este guia como uma linha de base.

## <a name="deploying-linux"></a>Implantação do Linux

O agente de recursos para HANA SAP está incluído no SUSE Linux Enterprise Server for SAP Applications.
O Azure Marketplace contém uma imagem para SUSE Linux Enterprise Server for SAP Applications 12 com BYOS (Traga sua própria assinatura) que você pode usar para implantar novas máquinas virtuais.

### <a name="manual-deployment"></a>Implantação manual

1. Criar um grupo de recursos
1. Criar uma rede virtual
1. Crie duas Contas de Armazenamento
1. Crie um Conjunto de Disponibilidade  
   Defina o máximo de domínio de atualização
1. Crie um Balanceador de Carga (interno)  
   Selecione a VNET da etapa acima
1. Crie a Máquina Virtual 1  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS)  
   Escolha a Conta de Armazenamento 1  
   Escolha o Conjunto de Disponibilidade  
1. Crie a Máquina Virtual 2  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS)  
   Escolha a Conta de Armazenamento 2   
   Escolha o Conjunto de Disponibilidade  
1. Adicione Discos de Dados
1. Configure o balanceador de carga
    1. Crie um pool de IPs de front-end
        1. Abra o balanceador de carga, escolha o pool de IPs de front-end e clique em Adicionar
        1. Digite o nome do novo pool de IPs de front-end (por exemplo hana-front-end)
       1. Clique em OK
        1. Depois que o novo pool de IPs de front-end for criado, anote seu endereço IP
    1. Crie um pool de back-end
        1. Abra o balanceador de carga, escolha os pools de back-end e clique em Adicionar
        1. Digite o nome do novo pool de back-end (por exemplo hana-back-end)
        1. Clique em Adicionar uma máquina virtual
        1. Selecione o Conjunto de Disponibilidade criado anteriormente
        1. Selecione as máquinas virtuais do cluster SAP HANA
        1. Clique em OK
    1. Criar uma investigação de integridade
       1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
        1. Digite o nome da nova investigação de integridade (por exemplo, hana-hp)
        1. Escolha TCP como protocolo, porta 625**03**, mantenha o Intervalo como 5 e o Limite de não integridade como 2
        1. Clique em OK
    1. Criar regras de balanceamento de carga
        1. Clique no balanceador de carga, escolha as regras de balanceamento de carga e clique em Adicionar
        1. Insira o nome da nova regra de balanceador de carga (por exemplo hana-lb-3**03**15)
        1. Escolha o endereço IP de front-end, o pool de back-end e a investigação de integridade criados anteriormente (por exemplo hana-front-end)
        1. Mantenha o protocolo TCP, insira a porta 3**03**15
        1. Aumente o tempo limite de ociosidade para 30 minutos
       1. **Habilite o IP Flutuante**
        1. Clique em OK
        1. Repita as etapas acima para a porta 3**03**17

### <a name="deploy-with-template"></a>Implantar com modelo
Você pode usar um dos modelos de início rápido no github para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade etc. Siga estas etapas para implantar o modelo:

1. Abra o [modelo de banco de dados][template-multisid-db] ou o [modelo convergido][template-converged] no portal do Azure. O modelo de banco de dados cria apenas as regras de balanceamento de carga para um banco de dados, enquanto o modelo convergido também cria as regras de balanceamento de carga para uma instância ASCS/SCS e ERS (apenas Linux). Se você planeja instalar um sistema baseado no SAP NetWeaver e também quer instalar a instância ASCS/SCS nas mesmas máquinas, use o [modelo convergido][template-converged].
1. Defina os seguintes parâmetros
    1. ID do sistema SAP  
       Insira a ID do sistema SAP do sistema SAP, que você deseja instalar. A Id será usada como um prefixo para os recursos que serão implantados.
    1. Tipo de pilha (aplicável somente se você usar o modelo convergido)  
       Selecionar o tipo de pilha do SAP NetWeaver
    1. Tipo de sistema operacional  
       Selecione uma das distribuições do Linux. Para este exemplo, selecione SLES 12 BYOS
    1. Tipo de banco de dados  
       Selecionar HANA
    1. Tamanho do sistema SAP  
       A quantidade de SAPs que o novo sistema fornecerá. Se você não tiver certeza de quantos SAPs o sistema precisará, pergunte ao seu Parceiro de tecnologia SAP ou Integrador de sistemas
    1. Disponibilidade do sistema  
       Selecione HA
    1. Nome de Usuário de Administrador e Senha do Administrador  
       É criado um novo usuário que pode ser usado para fazer logon no computador.
    1. Sub-rede Nova ou Existente  
       Determina se uma nova rede virtual e sub-rede devem ser criadas ou se uma sub-rede existente deve ser usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione a rede existente.
    1. ID da Sub-rede  
    A ID da sub-rede à qual as máquinas virtuais devem estar conectadas. Selecione a sub-rede da sua VPN ou a rede virtual da ExpressRoute para conectar a máquina virtual à sua rede local. A ID geralmente é semelhante a /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

## <a name="setting-up-linux-ha"></a>Configuração de HA do Linux

Os itens a seguir são prefixados com [A] – aplicável a todos os nós, [1] – aplicável somente ao nó 1 ou [2] – aplicável somente ao nó 2.

1. [A] Apenas SLES for SAP BYOS – registre o SLES para poder usar os repositórios
1. [A] Apenas SLES for SAP BYOS – adicione o módulo de nuvem pública
1. [A] Atualize o SLES
    ```bash
    sudo zypper update

    ```

1. [1] Habilite o acesso ssh
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] Habilite o acesso ssh
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] Habilite o acesso ssh
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] Instale a extensão HA
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] Configure o layout do disco
    1. LVM  
    Normalmente, é recomendável usar LVM para volumes que armazenam arquivos de log e dados. O exemplo abaixo pressupõe que as máquinas virtuais tenham quatro discos de dados anexados que devem ser usados para criar dois volumes.
        * Crie volumes físicos para todos os discos que queira usar.
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * Crie um grupo de volume para os arquivos de dados, um grupo de volume para os arquivos de log e um para o diretório compartilhado do SAP HANA
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * Criar os volumes lógicos
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * Crie os diretórios de montagem e copie a UUID de todos os volumes lógicos
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * Crie entradas fstab para os três volumes lógicos
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    Insira esta linha para /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * Monte os novos volumes
    <pre><code>
    sudo mount -a
    </code></pre>
    1. Discos simples  
       Para sistemas pequenos ou de demonstração, você pode colocar os arquivos de log e dados do HANA em um disco. Os comandos a seguir criam uma partição em /dev/sdc e a formatam com xfs.
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>escreva a id de /dev/sdc1
    sudo /sbin/blkid  sudo vi /etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] Configure a resolução de nome do host para todos os hosts  
    Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir
    ```bash
    sudo vi /etc/hosts
    ```
    Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] Instale o Cluster
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] Adicione nó ao cluster
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] Altere a senha hacluster para a mesma senha
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] Configure corosync para usar outro transporte e adicione nodelist. Caso contrário, o cluster não funcionará.
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    Adicione o conteúdo em negrito a seguir ao arquivo.
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of node 1 >
      }
      node {
        ring0_addr:     < ip address of node 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    Em seguida, reinicie o serviço corosync

    ```bash
    sudo service corosync restart
    
    ```

1. [A] Instale os pacotes HANA HA  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>Instalando o SAP HANA

Siga o capítulo 4 do [guia Cenário Otimizado para Desempenho da SR SAP HANA][suse-hana-ha-guide] para instalar a Replicação do Sistema SAP HANA.

1. [A] Execute hdblcm no DVD do HANA
    * Escolha instalação -> 1
    * Selecione os componentes adicionais para instalação -> 1
    * Insira o caminho de instalação [/hana/shared]: -> ENTER
    * Insira o Nome do Host Local [..]: -> ENTER
    * Você deseja adicionar outros hosts ao sistema? (s/n) [n]: -> ENTER
    * Insira a ID do Sistema SAP HANA: <SID of HANA e.g. HDB>
    * Insira o Número da Instância [00]:   
  Número da instância do HANA. Use 03 se você tiver usado o Modelo do Azure ou se tiver seguido o exemplo acima
    * Selecione Modo de Banco de Dados/Inserir Índice [1]: -> ENTER
    * Selecione o Uso do Sistema/Inserir Índice [4]:  
  Selecione o Uso do sistema
    * Informe o Local dos Volumes de Dados [/data/hana/HDB]: -> ENTER
    * Informe o Local dos Volumes de Log [/hana/log/HDB]: -> ENTER
    * Restringir a alocação máxima de memória? [n]: -> ENTER
    * Insira o Nome do Host do Certificado para o Host '...' [...]: -> ENTER
    * Insira a Senha de Usuário do Agente do Host SAP (sapadm):
    * Confirme a Senha de Usuário do Agente do Host SAP (sapadm):
    * Insira a Senha do Administrador de Sistema (hdbadm):
    * Confirme a Senha do Administrador de Sistema (hdbadm):
    * Insira o Diretório Base do Administrador de Sistema [/usr/sap/HDB/home]: -> ENTER
    * Insira o Shell de Logon do Administrador de Sistema [/bin/sh]: -> ENTER
    * Insira a ID de Usuário do Administrador de Sistema [1001]: -> ENTER
    * Insira a ID do Grupo de Usuários (sapsys) [79]: -> ENTER
    * Insira a Senha de Usuário do Banco de Dados (SYSTEM):
    * Confirme a Senha de Usuário do Banco de Dados (SYSTEM):
    * Reiniciar o sistema após a reinicialização do computador? [n]: -> ENTER
    * Deseja continuar? (s/n):  
  Valide o resumo e digite s para continuar
1. [A] Atualize o Agente do Host do SAP  
  Baixe o arquivo mais recente do Agente do Host do SAP em [SAP Softwarecenter][sap-swcenter] e execute o comando a seguir para fazer upgrade do agente. Substitua o caminho do arquivo de modo a apontar para o arquivo que você baixou.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] Crie uma replicação HANA (como raiz)  
    Execute o comando a seguir. Não se esqueça de substituir cadeias de caracteres em negrito (HDB da ID do Sistema HANA e o número de instância 03) pelos valores da sua instalação SAP HANA.
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] Criar entrada de repositório de chaves (como raiz)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] Faça o backup do banco de dados backup (como raiz)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] Alterne para o usuário sapsid (por exemplo, hdbadm) e crie o site primário.
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] Alterne para o usuário sapsid (por exemplo, hdbadm) e crie o site secundário.
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>Configurar a estrutura do cluster

Altere as configurações padrão

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-defaults.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>Criar dispositivo STONITH

O dispositivo STONITH usa uma Entidade de Serviço para autorização no Microsoft Azure. Siga estas etapas para criar uma Entidade de Serviço.

1. Acesse <https://portal.azure.com>
1. Abra a folha Azure Active Directory  
   Vá para Propriedades e anote a ID do Diretório. Essa é a **id de locatário**.
1. Clique em Registros do Aplicativo
1. Clique em Adicionar
1. Digite um Nome, selecione Tipo de Aplicativo "Aplicativo Web/API", insira uma URL de logon (por exemplo, http://localhost) e clique em Criar
1. A URL de logon não é usada e pode ser qualquer URL válida
1. Selecione o novo Aplicativo e clique em Chaves na guia Configurações
1. Insira uma descrição para uma nova chave, selecione "Nunca expira" e clique em Salvar
1. Anote o Valor. Ele é usado como **senha** da Entidade de Serviço
1. Anote a ID do Aplicativo. Ela é usada como nome de usuário (**id de logon** nas etapas abaixo) da Entidade de Serviço

A Entidade de Serviço não tem permissões para acessar os recursos do Azure por padrão. Você precisa fornecer as permissões da Entidade de Serviço para iniciar e parar (desalocar) todas as máquinas virtuais do cluster.

1. Acesse https://portal.azure.com
1. Abra a folha Todos os recursos
1. Selecione a máquina virtual
1. Clique em Controle de acesso (IAM)
1. Clique em Adicionar
1. Selecione a função Proprietário
1. Digite o nome do aplicativo criado acima
1. Clique em OK

Depois de editar as permissões das máquinas virtuais, você pode configurar os dispositivos STONITH no cluster.

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription id, resource group, tenant id, service principal id and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>Criar recursos do SAP HANA

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system id
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>now we load the file to the cluster
sudo crm configure load update crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>Testar configuração do cluster
O capítulo a seguir descreve como você pode testar sua configuração. Cada teste supõe que você é a raiz e o mestre do SAP HANA está em execução na máquina virtual saphanavm1.

#### <a name="fencing-test"></a>Teste de isolamento

Você pode testar a configuração do agente de isolamento desabilitando a interface de rede no nó saphanavm1.

<pre><code>
sudo ifdown eth0
</code></pre>

A máquina virtual agora deve ser reiniciada ou interrompida, dependendo a configuração do cluster.
Se você definiu a ação de stonith como desativada, a máquina virtual será interrompida e os recursos serão migrados para a máquina virtual em execução.

Depois de instalar a máquina virtual novamente, haverá falha na inicialização do recurso SAP HANA como secundário se você tiver definido AUTOMATED_REGISTER="false". Nesse caso, será preciso configurar a instância HANA como secundária executando o seguinte comando:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Teste de um failover manual

Você pode testar um failover manual interrompendo o serviço pacemaker no nó saphanavm1.
<pre><code>
service pacemaker stop
</code></pre>

Após o failover, você pode iniciar o serviço novamente. O recurso SAP HANA em saphanavm1 falhará ao inicializar como secundário se você tiver definido AUTOMATED_REGISTER="false". Nesse caso, será preciso configurar a instância HANA como secundária executando o seguinte comando:

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>Teste de uma migração

Você pode migrar o nó mestre SAP HANA executando o seguinte comando
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

Isso deve migrar o nó mestre SAP HANA e o grupo que contém o endereço IP virtual para saphanavm2.
O recurso SAP HANA em saphanavm1 falhará ao inicializar como secundário se você tiver definido AUTOMATED_REGISTER="false". Nesse caso, será preciso configurar a instância HANA como secundária executando o seguinte comando:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

A migração cria restrições de local que precisam ser excluídas novamente.

<pre><code>
crm configure edited

# delete location contraints that are named like the following contraint. You should have two contraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

Também é necessário limpar o estado do recurso de nó secundário

<pre><code>
# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>Próximas etapas
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e planejar a recuperação de desastre do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastre do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md). 
