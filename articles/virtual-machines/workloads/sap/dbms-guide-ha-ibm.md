---
title: Configurar o IBM Db2 HADR em VMs (máquinas virtuais) do Azure | Microsoft Docs
description: Estabeleça alta disponibilidade do IBM Db2 LUW em VMs (máquinas virtuais) do Azure.
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 10/16/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: faafce32c3452a5c4ff08783ec2edd28f7f961e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671892"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Alta disponibilidade do IBM Db2 LUW nas VMs do Azure no SUSE Linux Enterprise Server com o Pacemaker

O IBM Db2 para Linux, UNIX e Windows (LUW) na [configuração de HADR (alta disponibilidade e recuperação de desastres)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) consiste em um nó que executa uma instância do banco de dados primário e m pelo menos um nó que executa uma instância do banco de dados secundário. As alterações na instância do banco de dados primário são replicadas para uma instância de banco de dados secundário de modo síncrono ou assíncrono, dependendo da sua configuração. 

> [!NOTE]
> Este artigo contém referências aos termos *mestre* e *escravo*, os quais a Microsoft não usa mais. Quando esses termos forem removidos do software, nós os removeremos deste artigo.
   
Este artigo descreve como implantar e configurar as VMs (máquinas virtuais) do Azure, instalar a estrutura de cluster e instalar o IBM Db2 LUW com a configuração da HADR. 

O artigo não aborda como instalar e configurar o IBM Db2 LUW com HADR ou a instalação do software SAP. Para ajudar você a realizar essas tarefas, fornecemos referências aos manuais de instalação do SAP e da IBM. Este artigo se concentra em partes específicas ao ambiente do Azure. 

As versões do IBM Db2 com suporte são 10.5 e posteriores, conforme documentado na nota do SAP [1928533].

Antes de iniciar uma instalação, confira as seguintes notas e documentação do SAP:

| Nota do SAP | DESCRIÇÃO |
| --- | --- |
| [1928533] | Aplicativos SAP no Azure: produtos com suporte e tipos de VM do Azure |
| [2015553] | SAP no Azure: pré-requisitos de suporte |
| [2178632] | Métricas-chave de monitoramento para SAP no Azure |
| [2191498] | SAP no Linux com o Azure: monitoramento aprimorado |
| [2243692] | VM do Linux no Azure (IaaS): problemas na licença do SAP |
| [1984787] | SUSE Linux Enterprise Server 12: Notas de instalação |
| [1999351] | Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2233094] | DB6: aplicativos SAP no Azure que usam o IBM Db2 para Linux, UNIX e Windows – Informações adicionais |
| [1612105] | DB6: perguntas frequentes sobre Db2 com HADR |


| Documentação | 
| --- |
| [Wiki da comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): tem todas as Notas do SAP necessárias para o Linux |
| Guia de [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide] |
| [Implantação de Máquinas Virtuais do Azure para SAP no Linux][deployment-guide] (este artigo) |
| Guia de [Implantação do DBMS (gerenciador de banco de dados) das Máquinas Virtuais do Azure para SAP no Linux][dbms-guide] |
| [Lista de verificação de planejamento e implantação de carga de trabalho SAP no Azure][azr-sap-plancheck] |
| [Guias de práticas recomendadas do SUSE Linux Enterprise Server for SAP Applications 12 SP4][sles-for-sap-bp] |
| [Extensão de Alta Disponibilidade do SUSE Linux Enterprise 12 SP4][sles-ha-guide] |
| [Implantação do DBMS de Máquinas Virtuais do IBM Db2 Azure para carga de trabalho do SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Visão geral
Para obter alta disponibilidade, o IBM Db2 LUW com HADR é instalado em pelo menos duas máquinas virtuais do Azure, que são implantadas em um [conjunto de disponibilidade do Azure](../../windows/tutorial-availability-sets.md) ou em [Zonas de Disponibilidade do Azure](./sap-ha-availability-zones.md). 

Os gráficos a seguir exibem uma configuração de duas VMs do Azure do servidor de banco de dados. Ambas as VMs do Azure do servidor de banco de dados têm o próprio armazenamento anexado e estão em execução. Na HADR, uma instância de banco de dados em uma das VMs do Azure tem a função da instância primária. Todos os clientes estão conectados a esta instância primária. Todas as alterações nas transações de banco de dados são persistidas localmente no log de transações do Db2. Como os registros de log de transações são persistidos localmente, os registros são transferidos via TCP/IP para a instância do banco de dados no segundo servidor de banco de dados, no servidor em espera ou na instância em espera. A instância em espera atualiza o banco de dados local efetuando roll forward dos registros de log de transações transferidos. Dessa forma, o servidor em espera é mantido em sincronia com o servidor primário.

A HADR é apenas uma funcionalidade de replicação. Ele não tem detecção de falha e nenhum recurso automático de failover ou tomada de controle. Uma tomada de controle ou transferência para o servidor em espera precisa ser iniciada manualmente por um administrador de banco de dados. Para obter uma detecção automática de falha e tomada de controle, você pode usar o recurso de clustering do Linux Pacemaker. O Pacemaker monitora as duas instâncias de servidor de banco de dados. Quando a instância do servidor de banco de dados primário falha, o Pacemaker inicia uma tomada de controle de HADR *automática* com o servidor em espera. O Pacemaker também garante que o endereço IP virtual seja atribuído ao novo servidor primário.

![Visão geral de alta disponibilidade do IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Para que os servidores de aplicativos SAP se conectem ao banco de dados primário, você precisará de um nome do host virtual e de um endereço IP virtual. No caso de um failover, os servidores de aplicativos SAP se conectarão à nova instância do banco de dados primário. Em um ambiente do Azure, um [balanceador de carga do Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) precisa usar um endereço IP virtual da maneira exigida para a HADR do IBM Db2. 

Para ajudar você a entender totalmente como o IBM Db2 LUW com HADR e o Pacemaker se encaixa em uma configuração de sistema SAP altamente disponível, a imagem a seguir apresenta uma visão geral de uma configuração altamente disponível de um sistema SAP com base no banco de dados IBM Db2. Este artigo aborda apenas o IBM Db2, mas faz referência a outros artigos que abordam como configurar outros componentes de um sistema SAP.

![Visão geral do ambiente completo de alta disponibilidade do IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Visão geral de alto nível das etapas necessárias
Para implantar uma configuração do IBM Db2, você precisa seguir estas etapas:

  + Planeje o seu ambiente.
  + Implante as VMs.
  + Atualize o SUSE Linux e configure os sistemas de arquivos.
  + Instale e configure o Pacemaker.
  + Instale o [NFS altamente disponível][nfs-ha].
  + Instale o [ASCS/ERS em um cluster separado][ascs-ha].
  + Instale o banco de dados IBM Db2 com a opção Distribuído/Alta Disponibilidade (SWPM) selecionada.
  + Instale e crie um nó de banco de dados secundário e uma instância e configure a HADR.
  + Confirme se a HADR está funcionando.
  + Aplique a configuração do Pacemaker para controlar o IBM Db2.
  + Configure o Azure Load Balancer.
  + Instale os servidores de aplicativo primário e de diálogo.
  + Verifique e adapte a configuração dos servidores de aplicativos SAP.
  + Execute testes de failover e de tomada de controle.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planejar a infraestrutura do Azure para hospedar o IBM Db2 LUW com HADR

Conclua o processo de planejamento antes de executar a implantação. O planejamento cria a base para implantar uma configuração do Db2 com HADR no Azure. Os elementos principais que precisam fazer parte do planejamento do IMB Db2 LUW (parte do banco de dados do ambiente SAP) estão listados na seguinte tabela:

| Tópico | Descrição breve |
| --- | --- |
| Definir grupos de recursos do Azure | Os grupos de recursos em que você implanta a VM, a VNet, o Azure Load Balancer e outros recursos. Pode ser existente ou novo. |
| Definição de sub-rede/rede virtual | Local em que as VMs para IBM Db2 e Azure Load Balancer estão sendo implantadas. Pode ser existente ou criado recentemente. |
| Máquinas virtuais que hospedam o IBM Db2 LUW | Tamanho da VM, armazenamento, rede, endereço IP. |
| Nome do host virtual e IP virtual para banco de dados IBM Db2| O IP virtual ou o nome do host que é usado para conexão de servidores de aplicativos SAP. **db-virt-hostname**, **db-virt-ip**. |
| Isolamento do Azure | Isolamento do Azure ou isolamento SBD (altamente recomendado). Método para evitar situações de atenção dividida. |
| VM do SBD | Tamanho, armazenamento e rede da máquina virtual do SBD. |
| Azure Load Balancer | Uso de Básico ou Standard (recomendado), porta de investigação para **probe-port** do banco de dados Db2 (nossa recomendação é a 62500). |
| Resolução de nomes| Como a resolução de nomes funciona no ambiente. O serviço DNS é altamente recomendado. O arquivo de hosts local pode ser usado. |
    
Para obter mais informações sobre o Linux Pacemaker no Azure, confira [Configurar o Pacemaker no SUSE Linux Enterprise Server no Azure](./high-availability-guide-suse-pacemaker.md).

## <a name="deployment-on-suse-linux"></a>Implantação no SUSE Linux

O agente de recursos para IBM Db2 LUW está incluído no SUSE Linux Enterprise Server for SAP Applications. Na configuração descrita neste documento, você precisa usar o SUSE Linux Server for SAP Applications. O Azure Marketplace contém uma imagem para SUSE Enterprise Server for SAP Applications 12 que você pode usar para implantar novas máquinas virtuais do Azure. Lembre-se dos vários modelos de suporte ou de serviço que são oferecidos pelo SUSE por meio do Azure Marketplace ao escolher uma imagem de VM no Marketplace da VM do Azure. 

### <a name="hosts-dns-updates"></a>Hosts: atualizações de DNS
Faça uma lista de todos os nomes de host, incluindo nomes de host virtuais, e atualize os seus servidores DNS para habilitar o endereço IP apropriado para a resolução de nome de host. Se um servidor DNS não existir ou se não for possível atualizar e criar entradas DNS, será necessário usar os arquivos de host locais das VMs individuais que participam desse cenário. Se você estiver usando entradas de arquivos de host, verifique se as entradas são aplicadas a todas as VMs no ambiente do sistema SAP. No entanto, é recomendável que você use o DNS que, idealmente, inclua o Azure


### <a name="manual-deployment"></a>Implantação manual

Verifique se o sistema operacional selecionado tem suporte no IBM/SAP para IBM Db2 LUW. A lista de versões do sistema operacional com suporte para VMs do Azure e versões do Db2 está disponível na nota do SAP [1928533]. A lista de versões do sistema operacional por versão individual do Db2 está disponível na Matriz de Disponibilidade de Produto SAP. É altamente recomendável no mínimo a versão SLES 12 SP4 devido a aprimoramentos de desempenho relacionadas ao Azure nessa ou em versões posteriores do SUSE Linux.

1. Crie ou selecione um grupo de recursos.
1. Crie ou selecione uma rede virtual e uma sub-rede.
1. Crie um conjunto de disponibilidade do Azure ou implante uma zona de disponibilidade.
    + Para o conjunto de disponibilidade, defina o máximo de domínios de atualização como 2.
1. Crie a Máquina Virtual 1.
    + Use a imagem do SLES for SAP no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure que você criou na etapa 3 ou selecione a Zona de Disponibilidade.
1.  Crie a Máquina Virtual 2.
    + Use a imagem do SLES for SAP no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure que você criou na etapa 3 ou selecione a Zona de Disponibilidade (uma zona diferente daquela da etapa 3).
1. Adicione discos de dados às VMs e verifique a recomendação de uma configuração do sistema de arquivos no artigo [Implantação do DBMS de Máquinas Virtuais do Azure do IBM Db2 para carga de trabalho do SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster do Pacemaker
    
Para criar um cluster do Pacemaker básico neste servidor IBM Db2, confira [Configurar o Pacemaker no SUSE Linux Enterprise Server no Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instalar o IBM Db2 LUW e o ambiente SAP

Antes de iniciar a instalação de um ambiente SAP baseado no IBM Db2 LUW, examine a seguinte documentação:

+ Documentação do Azure
+ Documentação do SAP
+ Documentação da IBM

Os links para essa documentação são fornecidos na seção introdutória deste artigo.

Verifique os manuais de instalação do SAP para saber como instalar os aplicativos baseados no NetWeaver no IBM Db2 LUW.

Você pode encontrar os guias no portal de Ajuda do SAP usando o [Localizador de Guia de Instalação do SAP][sap-instfind].

Você pode reduzir o número de guias exibidos no portal definindo os seguintes filtros:

- Desejo: "Instalar um novo sistema"
- Meu banco de dados: "IBM Db2 para Linux, Unix e Windows"
- Filtros adicionais para as versões do SAP NetWeaver, configuração de pilha ou sistema operacional

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Dicas de instalação para configurar o IBM Db2 LUW com HADR

Para configurar a instância do banco de dados do IBM Db2 LUW primário:

- Use a opção alta disponibilidade ou distribuída.
- Instale o ASCS/ERS do SAP e a instância do banco de dados.
- Faça um backup do banco de dados recém-instalado.


> [!IMPORTANT] 
> Anote a "Porta de Comunicação do Banco de Dados" que foi configurada durante a instalação. É necessário que ambas as instâncias de banco de dados tenham o mesmo número da porta

Para configurar o servidor de banco de dados em espera usando o procedimento de cópia do sistema homogêneo do SAP, execute estas etapas:

1. Selecione a opção de **Cópia do sistema** > **Sistemas de destino** > **Distribuído** > **Instância do Banco de Dados**.
1. Como um método de cópia, selecione **Sistema Homogêneo** para que você possa usar o backup para restaurar um backup na instância do servidor em espera.
1. Quando você chegar à etapa de saída para restaurar o banco de dados para a cópia homogênea do sistema, saia do instalador. Restaure o banco de dados de um backup do host primário. Todas as fases de instalação subsequentes já foram executadas no servidor de banco de dados primário.
1. Configure a HADR para IBM Db2.

   > [!NOTE]
   > Para realizar a instalação e a configuração específicas do Azure e do Pacemaker: durante o procedimento de instalação por meio do SAP Software Provisioning Manager, há uma pergunta explícita sobre a alta disponibilidade do IBM Db2 LUW:
   >+ Não selecione **IBM Db2 pureScale**.
   >+ Não selecione **Instalar o IBM Tivoli System Automation for Multiplatforms**.
   >+ Não selecione **Gerar arquivos de configuração de cluster**.

   Ao usar um dispositivo SBD para o Linux Pacemaker, defina os seguintes parâmetros do Db2 HADR:
   + Duração da janela de emparelhamento de HADR (segundos) (HADR_PEER_WINDOW) = 300  
   + Valor de tempo limite da HADR (HADR_TIMEOUT) = 60

   Ao usar um agente de isolamento do Azure Pacemaker, defina os seguintes parâmetros:
   + Duração da janela de emparelhamento de HADR (segundos) (HADR_PEER_WINDOW) = 900  
   + Valor de tempo limite da HADR (HADR_TIMEOUT) = 60

Recomendamos os parâmetros anteriores com base no teste inicial de failover/tomada de controle. É obrigatório que você teste a funcionalidade adequada de failover e tomada de controle com essas configurações de parâmetro. Como as configurações individuais podem variar, os parâmetros podem precisar de ajuste. 

> [!IMPORTANT]
> Específico ao IBM Db2 com a configuração de HADR com inicialização normal: a instância de banco de dados secundária ou em espera precisa estar em execução para iniciar a instância do banco de dados primário.

Para fins de demonstração e para os procedimentos descritos neste artigo, o SID do banco de dados é **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Verificação do IBM Db2 HADR
Depois de configurar a HADR e o status for PEER e CONNECTED nos nós primário e em espera, execute a seguinte verificação:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Configuração do Db2 Pacemaker

Ao usar o Pacemaker para failover automático caso haja uma falha de nó, você precisa configurar também as suas instâncias do Db2 e do Pacemaker. Esta seção descreve esse tipo de configuração.

Os seguintes itens são prefixados com:

- **[A]** : aplicável a todos os nós
- **[1]** : aplicável somente ao nó 1 
- **[2]** : aplicável somente ao nó 2

**[A]** : pré-requisitos para a configuração do Pacemaker:
1. Desligue ambos os servidores de banco de dados com o usuário db2\<sid> usando db2stop.
1. Altere o ambiente do shell do usuário db2\<sid> para */bin/ksh*. Recomendamos o uso da ferramenta Yast. 


### <a name="pacemaker-configuration"></a>Configuração do Pacemaker

> [!IMPORTANT]
> Testes recentes revelaram situações em que o netcat deixa de responder às solicitações devido à lista de pendências e à limitação dele de manipular apenas uma conexão. O recurso netcat deixa de escutar as solicitações do Azure Load Balancer e o IP flutuante fica não disponível.  
> Para os clusters existentes do Pacemaker, recomendamos a substituição do netcat pelo socat. No momento, é recomendável usar o agente de recursos do azure-lb, que faz parte do pacote resource-agents, com os seguintes requisitos de versão do pacote:
> - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos resource-agents-4.3.018.a7fb5035-3.30.1.  
> - Para o SLES 15/15 SP1, a versão deve ser pelo menos resource-agents-4.3.0184.6ee15eb2-4.13.1.  
>
> Observe que a alteração exigirá um breve tempo de inatividade.  
> Para clusters do Pacemaker existentes, se a configuração já foi alterada para usar socat conforme descrito em [Proteção de Detecção do Azure Load Balancer](https://www.suse.com/support/kb/doc/?id=7024128), não há nenhum requisito para mudar imediatamente para o agente de recursos azure-lb.

**[1]** Configuração de Pacemaker específica para o IBM Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** Criar recursos do IBM Db2:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** Iniciar recursos do IBM Db2:
* Remova o Pacemaker do modo de manutenção.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Verifique se o status do cluster está correto e se todos os recursos foram iniciados. Não é importante saber em qual nó os recursos estão sendo executados.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nodes configured
# <a name="5-resources-configured"></a>5 resources configured

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Full list of resources:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd): Started azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Resource Group: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Masters: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Slaves: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> Você precisa gerenciar a instância do Db2 clusterizado do Pacemaker usando as ferramentas do Pacemaker. Se você usar comandos do db2 como db2stop, o Pacemaker detectará a ação como uma falha de recurso. Se estiver executando a manutenção, você poderá colocar os nós ou recursos no modo de manutenção. O Pacemaker suspende os recursos de monitoramento e você pode usar os comandos normais de administração do db2.


### <a name="configure-azure-load-balancer"></a>Configurar o Azure Load Balancer
Para configurar o Azure Load Balancer, recomendamos que você use o [SKU do Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) e faça o seguinte;

> [!NOTE]
> O SKU do Standard Load Balancer tem restrições para acessar endereços IP públicos dos nós no balanceador de carga. O artigo [Conectividade de ponto de extremidade público para Máquinas Virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade de SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) está descrevendo maneiras de habilitar o acesso desses nós aos endereços IP

> [!IMPORTANT]
> Não há suporte para IP flutuante em uma configuração de IP secundário de adaptador de rede em cenários de balanceamento de carga. Para obter detalhes, confira [Limitações do Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se você precisar de um endereço IP adicional para a VM, implante um segundo adaptador de rede.  

1. Crie um pool de IPs de front-end:

   a. No portal do Azure, abra o Azure Load Balancer, selecione **pool de IPs de front-end** e **Adicionar**.

   b. Insira o nome do novo pool de IPs de front-end (por exemplo, **Db2-connection**).

   c. Defina a **Atribuição** como **Estática** e insira o endereço IP **Virtual-IP** definido no início.

   d. Selecione **OK**.

   e. Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.

1. Crie um pool de back-end:

   a. No portal do Azure, abra o Azure Load Balancer, selecione **pools de back-end** e **Adicionar**.

   b. Insira o nome do novo pool de back-end (por exemplo, **Db2-backend**).

   c. Selecione **Adicionar uma máquina virtual**.

   d. Selecione o conjunto de disponibilidade ou as máquinas virtuais que hospedam o banco de dados IBM Db2 criado na etapa anterior.

   e. Selecione as máquinas virtuais do cluster do IBM Db2.

   f. Selecione **OK**.

1. Crie uma investigação de integridade:

   a. No portal do Azure, abra o Azure Load Balancer, selecione **investigações de integridade** e **Adicionar**.

   b. Digite o nome da nova investigação de integridade (por exemplo, **Db2-hp**).

   c. Selecione **TCP** como o protocolo e a porta **62500**. Mantenha o valor do **Intervalo** definido como **5** e o valor **Limite não íntegro** como **2**.

   d. Selecione **OK**.

1. Crie as regras de balanceamento de carga:

   a. No portal do Azure, abra o Azure Load Balancer, selecione **Regras de balanceamento de carga** e **Adicionar**.

   b. Insira o nome da nova regra do Load Balancer (por exemplo, **Db2-SID**).

   c. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **Db2-frontend**).

   d. Mantenha o **Protocolo** definido como **TCP** e insira a *Porta de Comunicação do Banco de Dados*.

   e. Aumente o **tempo limite de ociosidade** para 30 minutos.

   f. Certifique-se de **habilitar IP Flutuante**.

   g. Selecione **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Fazer alterações nos perfis do SAP para usar o IP virtual para conexão
Para se conectar à instância primária da configuração de HADR, a camada do aplicativo SAP precisa usar o endereço IP virtual que você definiu e configurou no Azure Load Balancer. As seguintes alterações são exigidas:

/sapmnt/\<SID>/profile/DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalar os servidores de aplicativo primário e de diálogo

Quando você instala servidores de aplicativos primários e de diálogo em uma configuração do Db2 HADR, use o nome do host virtual que você escolheu para a configuração. 

Se você executou a instalação antes de criar a configuração do Db2 HADR, faça as alterações conforme descrito na seção anterior e da seguinte maneira para as pilhas Java do SAP.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Verificação de URL do JDBC de sistemas de pilha Java ou ABAP+Java

Use a ferramenta de Configuração do J2EE para verificar ou atualizar a URL do JDBC. Como a ferramenta de Configuração do J2EE é uma ferramenta gráfica, você precisa ter o servidor X instalado:
 
1. Entre no servidor de aplicativos primário da instância do J2EE e execute: `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. No quadro à esquerda, selecione **repositório de segurança**.
1. No quadro à direita, selecione a chave jdbc/pool/\<SAPSID>/url.
1. Altere o nome do host na URL do JDBC para o nome do host virtual.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Selecione **Adicionar**.
1. Para salvar as suas alterações, selecione o ícone de disco no canto superior esquerdo.
1. Feche a ferramenta de configuração.
1. Reinicie a instância do Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurar o arquivamento de log para a instalação da HADR
Para configurar o arquivamento de log do Db2 para a instalação da HADR, recomendamos que você configure que os bancos de dados primário e em espera tenham a capacidade de recuperação de log automática de todas as localizações de arquivos de log. Os bancos de dados primário e em espera precisam ser capazes de recuperar arquivos de log de todas as localizações de arquivos de log para os quais qualquer uma das instâncias do banco de dados pode arquivar arquivos de log. 

O arquivamento de log é executado somente pelo banco de dados primário. Se você alterar as funções de HADR dos servidores de banco de dados ou se ocorrer uma falha, o novo banco de dados primário será responsável pelo arquivamento de log. Se você tiver configurado várias localizações de arquivos de log, os logs poderão ser arquivados duas vezes. Caso haja uma atualização local ou remota, você também pode precisar copiar manualmente os logs arquivados do servidor primário antigo para a localização de log ativo do novo servidor primário.

É recomendável configurar um compartilhamento NFS comum em que os logs são gravados usando ambos os nós. O compartilhamento NFS precisa estar altamente disponível. 

Você pode usar compartilhamentos NFS altamente disponíveis existentes para transportes ou um diretório de perfil. Para obter mais informações, consulte:

- [Alta disponibilidade do NFS em VMs do Azure no SUSE Linux Enterprise Server][nfs-ha] 
- [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com o Azure NetApp Files para Aplicativos SAP](./high-availability-guide-suse-netapp-files.md)
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (para criar compartilhamentos NFS)


## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta seção descreve como é possível testar a configuração do Db2 HADR. *Cada teste pressupõe que você está conectado como usuário raiz* e o IBM Db2 primário está em execução na máquina virtual *azibmdb01*.

O status inicial de todos os casos de teste é explicado aqui: (crm_mon -r ou crm status)

- O **crm status** é um instantâneo do status do Pacemaker no tempo de execução 
- **crm_mon -r** é uma saída contínua do status do Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

O status original em um sistema SAP é documentado em Transação DBACOCKPIT > Configuração > Visão geral, como mostrado na seguinte imagem:

![DBACockpit – Pré-migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Testar tomada de controle do IBM Db2


> [!IMPORTANT] 
> Antes de começar o teste, verifique se:
> * O Pacemaker não tem nenhuma ação com falha (crm status).
> * Não há restrições de localização (sobras de teste de migração)
> * A sincronização do IBM Db2 HADR está funcionando. Verificar com o usuário db2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migre o nó que está executando o banco de dados Db2 primário executando o seguinte comando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Depois que a migração for concluída, a saída do crm status será parecida com esta:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

O status original em um sistema SAP é documentado em Transação DBACOCKPIT > Configuração > Visão geral, como mostrado na seguinte imagem:

![DBACockpit – Pós-migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

A migração de recursos com "crm resource migrate" cria restrições de localização. As restrições de localização devem ser excluídas. Se as restrições de localização não forem excluídas, o recurso não poderá realizar failback ou você poderá experimentar tomadas de controle indesejadas. 

Migre o recurso de volta para *azibmdb01* e desmarque as restrições de localização
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **crm resource migrate \<res_name> \<host>:** cria restrições de localização e pode causar problemas com a tomada de controle
- **crm resource clear \<res_name>** : limpa as restrições de localização
- **crm resource cleanup \<res_name>** : limpa todos os erros do recurso

### <a name="test-the-fencing-agent"></a>Testar o agente de isolamento

Nesse caso, testamos o isolamento de SBD, que recomendamos que você faça ao usar o SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

O nó de cluster *azibmdb01* deve ser reiniciado. A função HADR primária do IBM Db2 será movida para *azibmdb02*. Quando o *azibmdb01* estiver online novamente, a instância do Db2 será movida na função de uma instância de banco de dados secundária. 

Se o serviço Pacemaker não iniciar automaticamente no primário reinicializado anterior, verifique se ele foi iniciado manualmente com:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testar uma tomada de controle manual

Você pode testar uma tomada de controle manual interrompendo o serviço Pacemaker no nó *azibmdb01*:
<pre><code>service pacemaker stop</code></pre>

status no *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Após o failover, você pode iniciar o serviço novamente em *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Encerrar o processo do Db2 no nó que executa o banco de dados primário de HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Isso fará com que a instância do Db2 falhe e o pacemaker relate o seguinte status:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

O Pacemaker reinicia a instância do banco de dados primário do Db2 no mesmo nó ou faz failover para o nó que está executando a instância do banco de dados secundário e um erro será relatado.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Encerrar o processo do Db2 no nó que executa a instância de banco de dados secundário

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

O nó entra no estado com falha e um erro é reportado
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

A instância do Db2 é reiniciada na função secundária que foi atribuída anteriormente.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Pare o BD por meio de "db2stop force" no nó que executa a instância do banco de dados primário de HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Como o usuário db2\<sid> execute o comando db2stop force:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Falha detectada
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

A instância do banco de dados secundário do Db2 HADR foi promovida para a função primária
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Falha na VM com reinicialização no nó que executa a instância do banco de dados primário de HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

O Pacemaker promoverá a instância secundária para a função de instância primária. A antiga instância primária será movida para a função secundária depois que a VM e todos os serviços forem totalmente restaurados após a reinicialização da VM:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Cause uma falha na VM que executa a instância do banco de dados primário de HADR com "halt"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Nesse caso, o Pacemaker detectará que o nó que está executando a instância do banco de dados primário não está respondendo.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

A próxima etapa é verificar uma situação de *Divisão de atenção*. Depois que o nó sobrevivente determinar que o nó executado pela última vez a instância do banco de dados primário está inativo, será executado um failover dos recursos.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


Caso haja uma "parada" do nó, o nó com falha precisa ser reiniciado por meio das ferramentas de Gerenciamento do Azure (no portal do Azure, no PowerShell ou na CLI do Azure). Depois que o nó com falha estiver online novamente, ele iniciará a instância do Db2 na função secundária.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Próximas etapas
- [Arquitetura de alta disponibilidade e cenários para SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
- [Configurar o Pacemaker no SUSE Linux Enterprise Server no Azure](./high-availability-guide-suse-pacemaker.md)

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
