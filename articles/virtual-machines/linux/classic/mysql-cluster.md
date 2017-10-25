---
title: Clusterizar MySQL com conjuntos com balanceamento de carga | Microsoft Docs
description: "Configurar um cluster MySQL do Linux com alta disponibilidade e balanceamento de carga criado com o modelo clássico de implantação no Azure"
services: virtual-machines-linux
documentationcenter: 
author: bureado
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
ms.openlocfilehash: 4eaf86c9ac3e4dc2b51b88383626eda774cab0e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Use os conjuntos de carga balanceada para clusterizar MySQL no Linux
> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager](../../../resource-manager-deployment-model.md) e Clássico. Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Um [modelo do Resource Manager](https://azure.microsoft.com/documentation/templates/mysql-replication/) estará disponível se você precisar implantar um cluster do MySQL.

Este artigo explora e ilustra as diferentes abordagens disponíveis para implantar serviços com base em Linux, altamente disponíveis no Microsoft Azure, explorando a alta disponibilidade do MySQL Server como elemento principal. Há um vídeo ilustrando essa abordagem disponível no [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Descrevemos uma solução de alta disponibilidade do MySQL de mestre único, dois nós e sem compartilhar nada, com base em DRBD, Corosync e Pacemaker. Apenas um nó executa o MySQL por vez. A leitura e a gravação do recurso DRBD também está limitada a apenas um nó por vez.

Não há necessidade de uma solução VIP como LVS porque você usará os conjuntos de balanceamento de carga no Microsoft Azure para fornecer a funcionalidade de round robin e detecção do ponto de extremidade, além da remoção e recuperação normal do VIP. O VIP é um endereço IPv4 roteável globalmente, atribuído pelo Microsoft Azure ao criarmos o serviço de nuvem pela primeira vez.

Existem outras arquiteturas possíveis para MySQL, inclusive NBD Cluster, Percona e Galera, bem como diversas soluções de middleware, incluindo pelo menos uma disponível como uma VM no [Repositório de VM](http://vmdepot.msopentech.com). Como essas soluções podem replicar em unicast X multicast ou difusão e não dependem de armazenamento compartilhado ou de várias interfaces de rede, os cenários devem ser fáceis de implantar no Microsoft Azure.

Essas arquiteturas de clustering podem ser estendidas até outro produtos como PostgreSQL e OpenLDAP de maneira semelhante. Por exemplo, esse procedimento de balanceamento de carga sem compartilhar nada foi testado com êxito com o OpenLDAP multimestres e é possível ver isso em nosso blog Channel 9.

## <a name="get-ready"></a>Prepare-se
É necessário ter os seguintes recursos e capacidades:

  - Uma conta do Microsoft Azure com uma assinatura válida, capaz de criar pelo menos duas VMs (XS foi usado neste exemplo)
  - Uma rede e uma sub-rede
  - Um grupo de afinidades
  - Um conjunto de disponibilidade
  - A capacidade de criar VHDs na mesma região que o serviço de nuvem e anexá-los às VMs Linux

### <a name="tested-environment"></a>Ambiente testado
* Ubuntu 13.10
  * DRBD
  * MySQL Server
  * Corosync e Pacemaker

### <a name="affinity-group"></a>Grupo de afinidade
Crie um grupo de afinidades para a solução de entrando no Portal Clássico do Azure, selecionando **Configurações** e criando um novo grupo de afinidades. Os recursos alocados criados posteriormente serão atribuídos a esse grupo de afinidade.

### <a name="networks"></a>Redes
Uma nova rede é criada, e uma sub-rede é criada dentro da rede. Este exemplo usa uma rede 10.10.10.0/24 com apenas uma sub-rede /24 interna.

### <a name="virtual-machines"></a>Máquinas virtuais
A primeira VM do Ubuntu 13.10 é criada usando-se uma imagem da Endorsed Ubuntu Gallery, chamada `hadb01`. Um novo serviço de nuvem é criado no processo, chamado hadb. Esse nome para ilustrar a natureza compartilhada e de balanceamento de carga que o serviço terá quando adicionarmos mais recursos. A criação de `hadb01` ocorre sem erros e é concluída usando o portal. Um ponto de extremidade para SSH é criado automaticamente e a nova rede criada é selecionada. Agora você pode criar um conjunto de disponibilidade para as VMs.

Depois que a primeira VM for criada (tecnicamente, quando o serviço de nuvem é criado), crie a segunda VM, `hadb02`. Para a segunda VM, usaremos a VM do Ubuntu 13.10 na Galeria usando o portal, porém use um serviço de nuvem existente, `hadb.cloudapp.net`, em vez de criar um novo. A rede e o conjunto de disponibilidade deverão ser selecionados automaticamente. Também será criado um ponto de extremidade SSH.

Depois que ambas as VMs forem criadas, anotaremos a porta SSH de `hadb01` (TCP 22) e de `hadb02` (atribuída automaticamente pelo Azure).

### <a name="attached-storage"></a>Armazenamento anexado
Anexe um novo disco a ambas as VMs e crie discos de 5 GB no processo. Os discos serão hospedados no contêiner VHD em uso para os discos do sistema operacional principal. Depois que os discos forem criados e anexados não há necessidade de reiniciar o Linux, pois o kernel verá o novo dispositivo. Este dispositivo é normalmente `/dev/sdc`. Verifique `dmesg` para ver a saída.

Em cada VM, crie uma partição usando `cfdisk` (primária, partição do Linux) e grave a nova tabela de partição. Não crie um sistema de arquivos nessa partição.

## <a name="set-up-the-cluster"></a>Configurar o cluster
Use o APT para instalar Corosync, Pacemaker e DRBD em ambas as VMs Ubuntu. Para fazer isso com `apt-get`, execute o seguinte código:

    sudo apt-get install corosync pacemaker drbd8-utils.

Não instale o MySQL neste momento. Os scripts de instalação do Debian e do Ubuntu inicializarão um diretório de dados do MySQL em `/var/lib/mysql`, mas como o diretório será substituído por um sistema de arquivos DRBD, será necessário instalar o MySQL mais tarde.

Verifique (usando `/sbin/ifconfig`) se ambas as VMs estão usando endereços na sub-rede 10.10.10.0/24 e se podem executar ping uma na outra pelo nome. Você também pode usar `ssh-keygen` e `ssh-copy-id` para verificar se ambas as VMs conseguem se comunicar via SSH sem exigir uma senha.

### <a name="set-up-drbd"></a>Configurar DRBD
Crie um recurso DRBD que usa a partição subjacente `/dev/sdc1` para produzir um recurso `/dev/drbd1` capaz de ser formatado usando ext3 e usado em nós primários e secundários.

1. Abra `/etc/drbd.d/r0.res` e copie a definição de recurso a seguir em ambas as VMs:

        resource r0 {
          on `hadb01` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.4:7789;
            meta-disk internal;
          }
          on `hadb02` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.5:7789;
            meta-disk internal;
          }
        }

2. Inicialize o recurso usando `drbdadm` em ambas as VMs:

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. Na VM primária (`hadb01`), force a propriedade (primária) do recurso DRBD:

        sudo drbdadm primary --force r0

Se examinar o conteúdo de /proc/drbd (`sudo cat /proc/drbd`) em ambas as VMs, você deve ver `Primary/Secondary` em `hadb01` e `Secondary/Primary` em `hadb02`, consistente com a solução até o momento. O disco de 5 GB será sincronizado na rede 10.10.10.0/24 sem encargos para os clientes.

Assim que o disco for sincronizado, será possível criar o sistema de arquivos em `hadb01`. Usamos ext2 para fins de testes, mas a seguinte instrução criará um sistema de arquivos ext3:

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>Montando o recurso DRBD
Agora você está pronto para montar os recursos DRBD no `hadb01`. Debian e derivativos usam `/var/lib/mysql` como diretório de dados do MySQL. Como você não instalou o MySQL, criaremos o diretório e montaremos o recurso DRBD. Para executar essa opção, execute o seguinte código `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>Configurar MySQL
Agora você está pronto para instalar o MySQL em `hadb01`:

    sudo apt-get install mysql-server

Você tem duas opções para `hadb02`. Você pode instalar o mysql-server, que criará /var/lib/mysql e preenchê-lo com um novo diretório de dados, para então remover o conteúdo. Para executar essa opção, execute o seguinte código `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

A segunda opção é realizar failover para `hadb02` e, em seguida, instalar mysql-server lá. Scripts de instalação perceberão a instalação existente e não tocarão nela.

Execute o código a seguir no `hadb01`:

    sudo drbdadm secondary –force r0

Execute o código a seguir no `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Se não pretende usar failover no DRBD agora, a primeira opção é mais fácil, embora indiscutivelmente menos elegante. Depois de configurá-lo, você pode começar a trabalhar no banco de dados do MySQL. Execute o código a seguir em `hadb02` (ou qualquer um dos servidores ativos, de acordo com o DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> Esta última instrução desabilita efetivamente a autenticação para o usuário raiz nessa tabela. Ela deve ser substituída pelas instruções GRANT de nível de produção e só é incluída para fins de ilustração.

Caso queira fazer consultas externas às VMs (que é a finalidade deste guia), você também precisará habilitar a rede para o MySQL. Em ambas as VMs, abra `/etc/mysql/my.cnf` e acesse `bind-address`. Altere o endereço de 127.0.0.1 para 0.0.0.0. Depois de salvar o arquivo, emita uma `sudo service mysql restart` no primário atual.

### <a name="create-the-mysql-load-balanced-set"></a>Criar o conjunto de balanceamento de carga do MySQL
Volte para o portal, acesse `hadb01` e escolha **Pontos de Extremidade**. Para criar um ponto de extremidade, escolha MySQL (TCP 3306) na lista suspensa e selecione **Criar novo conjunto de balanceamento de carga**. Nomeie o ponto de extremidade do balanceamento de carga como `lb-mysql`. Defina **Time** para 5 segundos no mínimo.

Depois de criar o ponto de extremidade, acesse `hadb02`, escolha **Pontos de Extremidade** e crie um ponto de extremidade. Escolha `lb-mysql` e selecione MySQL na lista suspensa. Também é possível usar a CLI do Azure nesta etapa.

Agora você tem tudo o que precisa para uma operação manual do cluster.

### <a name="test-the-load-balanced-set"></a>Testar o conjunto de balanceamento de carga
É possível executar testes de um computador externo usando qualquer cliente do MySQL ou por meio de determinados aplicativos, como phpMyAdmin em execução como um site do Azure. Nesse caso, você usou a ferramenta de linha de comando do MySQL em outra caixa do Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Executando failover manualmente
Você pode simular failovers desativando o MySQL, alternando o primário do DRBD e reiniciando o MySQL.

Para executar essa tarefa, execute o seguinte código em hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Em seguida, em hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Depois de executar failover manualmente, você poderá repetir a consulta remota e ela deverá funcionar perfeitamente.

## <a name="set-up-corosync"></a>Configurar Corosync
Corosync é a infraestrutura de cluster subjacente necessária ao funcionamento do Pacemaker. Para Heartbeat (e outras metodologias como Ultramonkey), o Corosync é uma divisão das funcionalidades de CRM, enquanto o Pacemaker continua mais semelhante ao Heartbeat em funcionalidade.

A restrição principal do Corosync no Azure é que o Corosync prefere comunicação multicast via broadcast via unicast, mas a rede do Microsoft Azure só é compatível com unicast.

Felizmente, o Corosync tem um modo unicast funcional. A única restrição real é que, como todos os nós não estão se comunicando entre si, você precisa definir os nós em arquivos de configuração, inclusive seus endereços IP. Podemos usar os arquivos de exemplo do Corosync para Unicast e alterar o endereço de associação, as listas de nós e os diretórios de registro em log (o Ubuntu usa `/var/log/corosync` e os arquivos de exemplo usam `/var/log/cluster`), além de habilitar as ferramentas de quorum.

> [!NOTE]
> Use a diretiva `transport: udpu` a seguir e os endereços IP definidos manualmente para ambos os nós.

Execute o código a seguir no `/etc/corosync/corosync.conf` em ambos os nós:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Copie esse arquivo de configuração em ambas as VMs e inicie o Corosync em ambos os nós:

    sudo service start corosync

Logo depois de iniciar o serviço, o cluster deverá estar estabelecido no anel atual e o quorum deverá estar constituído. Podemos verificar essa funcionalidade examinando logs ou executando o código a seguir:

    sudo corosync-quorumtool –l

Você verá saídas semelhantes à seguinte imagem:

![corosync-quorumtool -l sample output](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Configurando o Pacemaker
O Pacemaker usa o cluster para monitorar recursos, definir quando os primários são desativados e mudar esses recursos para os secundários. Os recursos podem ser definidos com base em um conjunto de scripts disponíveis ou de scripts LSB (init-like), entre outras opções.

Queremos que o Pacemaker seja o “proprietário” do recurso DRBD, do ponto de montagem e do serviço MySQL. Se o Pacemaker puder ativar e desativar o DRBD, montar e desmontá-lo, e iniciar e parar o MySQL na ordem certa quando algo ruim acontecer com o primário, então a instalação estará concluída.

Ao instalar o Pacemaker pela primeira vez, a configuração deve ser bem simples, algo como:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. Verifique a configuração executando `sudo crm configure show`.
2. Crie, então, um arquivo (como `/tmp/cluster.conf`) com os seguintes recursos:

        primitive drbd_mysql ocf:linbit:drbd \
              params drbd_resource="r0" \
              op monitor interval="29s" role="Master" \
              op monitor interval="31s" role="Slave"

        ms ms_drbd_mysql drbd_mysql \
              meta master-max="1" master-node-max="1" \
                clone-max="2" clone-node-max="1" \
                notify="true"

        primitive fs_mysql ocf:heartbeat:Filesystem \
              params device="/dev/drbd/by-res/r0" \
              directory="/var/lib/mysql" fstype="ext3"

        primitive mysqld lsb:mysql

        group mysql fs_mysql mysqld

        colocation mysql_on_drbd \
               inf: mysql ms_drbd_mysql:Master

        order mysql_after_drbd \
               inf: ms_drbd_mysql:promote mysql:start

        property stonith-enabled=false

        property no-quorum-policy=ignore

3. Carregar o arquivo para a configuração. Você só precisa fazer isso em um nó.

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. Verifique se o Pacemaker é iniciado durante a inicialização em ambos os nós:

        sudo update-rc.d pacemaker defaults

5. Usando `sudo crm_mon –L`, verifique se um dos nós se tornou o mestre do cluster e está executando todos os recursos. Você pode usar montar e verificar se os recursos estão em execução.

A captura de tela a seguir mostra `crm_mon` com um nó parado (saia selecionando Control+C):

![crm_mon node stopped](./media/mysql-cluster/image002.png)

Essa captura de tela mostra ambos os nós, um mestre e um subordinado:

![crm_mon operational master/slave](./media/mysql-cluster/image003.png)

## <a name="testing"></a>Testando
Você está pronto para uma simulação de failover automático. Existem duas maneiras de fazer isso: a fácil e a difícil.

A maneira fácil é usar a função de desligamento do cluster: ``crm_standby -U `uname -n` -v on``. Se você usar isso no mestre, o subordinado assumirá. Lembre-se de defini-lo novamente como desativado. Caso contrário, o crm_mon mostrará um nó em espera.

A maneira mais difícil é desligar a VM primária (hadb01) por meio do portal ou alterar o nível de execução na VM (ou seja, parada e desligamento). Isso ajuda a Corosync e o Pacemaker, sinalizando que o mestre será desativado. Você pode testar isso (útil para janelas de manutenção), mas também é possível forçar o cenário apenas congelando a VM.

## <a name="stonith"></a>STONITH
Deve ser possível emitir um desligamento da VM por meio da CLI do Azure, em vez de um script STONITH que controle um dispositivo físico. É possível usar `/usr/lib/stonith/plugins/external/ssh` como base e habilitar STONITH na configuração do cluster. A CLI do Azure deve estar instalada globalmente e as configurações e perfil de publicação devem ser carregadas para o usuário do cluster.

Código de exemplo para o recurso está disponível no [GitHub](https://github.com/bureado/aztonith). Altere a configuração do cluster adicionando o seguinte a `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> O script não realiza verificações de ligado/desligado. O recurso SSH original apresentava 15 verificações de ping, mas o tempo de recuperação de uma VM do Azure pode ser mais variável.

## <a name="limitations"></a>Limitações
As seguintes limitações se aplicam:

* O script do recurso DRBD linbit que gerencia DRBD como um recurso no Pacemaker usa `drbdadm down` durante a desativação de um nó, mesmo que o nó esteja apenas em modo de espera. Isso não é o ideal, pois o subordinado não sincronizará o recurso DRBD enquanto o mestre receber gravações. Se o mestre não falhar normalmente, o escravo poderá assumir um estado do sistema de arquivos mais antigo. Existem duas maneiras em potencial de resolver isso:
  * Impor um `drbdadm up r0` a todos os nós de cluster por meio de um watchdog local (não clusterizado)
  * Editando o script DRBD linbit, verificando se `down` não é chamado em `/usr/lib/ocf/resource.d/linbit/drbd`
* O balanceador de carga precisa de pelo menos cinco segundos para responder, por isso os aplicativos devem reconhecer o cluster e ser mais tolerantes quanto ao tempo limite. Outras arquiteturas, como filas no aplicativo e middleware de consulta também podem ajudar.
* O ajuste do MySQL é necessário para garantir que a gravação ocorra em um ritmo gerenciável e que os caches sejam liberados para o disco com a maior frequência possível a fim de minimizar a perda de memória.
* O desempenho da gravação dependerá da interconexão da VM no comutador virtual, pois esse é o mecanismo usado pelo DRBD para replicar o dispositivo.
