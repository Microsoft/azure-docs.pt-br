---
title: Configurar o IBM DB2 HADR em VMs (máquinas virtuais) do Azure | Microsoft Docs
description: Estabeleça alta disponibilidade do IBM DB2 LUW em VMs (máquinas virtuais) do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: 754eb063f82344e72bece8fb0ac5708dbc8ab791
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249126"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Alta disponibilidade do IBM DB2 LUW em VMs do Azure em SUSE Linux Enterprise Server com pacemaker

O IBM DB2 para Linux, UNIX e Windows (LUW) na [configuração de alta disponibilidade e recuperação de desastres (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) consiste em um nó que executa uma instância de banco de dados primário e pelo menos um nó que executa uma instância de banco de dados secundária. As alterações na instância do banco de dados primário são replicadas para uma instância de banco de dados secundária de forma síncrona ou assíncrona, dependendo de sua configuração. 

Este artigo descreve como implantar e configurar as VMs (máquinas virtuais) do Azure, instalar a estrutura de cluster e instalar o IBM DB2 LUW com a configuração do HADR. 

O artigo não aborda como instalar e configurar o IBM DB2 LUW com o HADR ou a instalação do software SAP. Para ajudá-lo a realizar essas tarefas, fornecemos referências a manuais de instalação do SAP e IBM. Este artigo se concentra em partes específicas do ambiente do Azure. 

As versões do IBM DB2 com suporte são 10,5 e posteriores, conforme documentado em SAP Note [1928533].

Antes de iniciar uma instalação, consulte as seguintes notas e documentação do SAP:

| Nota SAP | DESCRIÇÃO |
| --- | --- |
| [1928533] | Aplicativos SAP no Azure: produtos com suporte e tipos de VM do Azure |
| [2015553] | SAP no Azure: pré-requisitos de suporte |
| [2178632] | Principais métricas de monitoramento para SAP no Azure |
| [2191498] | SAP no Linux com o Azure: monitoramento aprimorado |
| [2243692] | VM do Linux no Azure (IaaS): Problemas de licença do SAP |
| [1984787] | SUSE Linux Enterprise Server 12: Notas de instalação |
| [1999351] | Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2233094] | DB6: Aplicativos SAP no Azure que usam IBM DB2 para Linux, UNIX e Windows-informações adicionais |
| [1612105] | DB6: Perguntas frequentes sobre DB2 com HADR |


| Documentação | 
| --- |
| [Wiki da Comunidade SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Tem todas as notas SAP necessárias para o Linux |
| [Planejamento e implementação de máquinas virtuais do Azure para SAP no][planning-guide] guia do Linux |
| [Implantação de máquinas virtuais do Azure para SAP no Linux][deployment-guide] (este artigo) |
| [Implantação do sistema de gerenciamento de banco de dados (DBMS) de máquinas virtuais do Azure para SAP no Linux][dbms-guide] |
| [Lista de verificação de carga de trabalho do SAP no planejamento e implantação do Azure][azr-sap-plancheck] |
| [Guias de práticas recomendadas do SUSE Linux Enterprise Server para aplicativos SAP 12 SP3][sles-for-sap-bp] |
| [Extensão de alta disponibilidade do SUSE Linux Enterprise 12 SP3][sles-ha-guide] |
| [Implantação de DBMS de máquinas virtuais do Azure do IBM DB2 para carga de trabalho do SAP][dbms-db2] |
| [IBM DB2 HADR 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Visão geral
Para obter alta disponibilidade, o IBM DB2 LUW com HADR é instalado em pelo menos duas máquinas virtuais do Azure, que são implantadas em um [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ou entre [zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Os gráficos a seguir exibem uma configuração de duas VMs do Azure do servidor de banco de dados. Ambas as VMs do Azure do servidor de banco de dados têm seu próprio armazenamento anexado e estão em execução. No HADR, uma instância de banco de dados em uma das VMs do Azure tem a função da instância primária. Todos os clientes estão conectados a esta instância primária. Todas as alterações nas transações de banco de dados são mantidas localmente no log de transações do DB2. Como os registros de log de transações são persistidos localmente, os registros são transferidos via TCP/IP para a instância do banco de dados no segundo servidor de banco de dados, no servidor em espera ou na instância em espera. A instância em espera atualiza o banco de dados local rolando os registros de log de transações transferidos. Dessa forma, o servidor em espera é mantido em sincronia com o servidor primário.

O HADR é apenas uma funcionalidade de replicação. Ele não tem detecção de falha e nenhum recurso de tomada ou failover automático. Uma tomada ou transferência para o servidor em espera deve ser iniciada manualmente por um administrador de banco de dados. Para obter uma detecção automática de tomada e falha, você pode usar o recurso de clustering do Linux pacemaker. Pacemaker monitora as duas instâncias de servidor de banco de dados. Quando a instância do servidor de banco de dados primário falha, o pacemaker inicia um tomada HADR *automático* pelo servidor em espera. O pacemaker também garante que o endereço IP virtual seja atribuído ao novo servidor primário.

![Visão geral de alta disponibilidade do IBM DB2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Para que os servidores de aplicativos SAP se conectem ao banco de dados primário, você precisa de um nome de host virtual e de um endereço IP virtual. No caso de um failover, os servidores de aplicativos SAP se conectarão à nova instância de banco de dados primário. Em um ambiente do Azure, um balanceador de [carga do Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) é necessário para usar um endereço IP virtual da maneira necessária para o HADR do IBM DB2. 

Para ajudá-lo a entender totalmente como o IBM DB2 LUW com o HADR e o pacemaker se encaixa em uma configuração de sistema SAP altamente disponível, a imagem a seguir apresenta uma visão geral de uma configuração altamente disponível de um sistema SAP com base no banco de dados IBM DB2. Este artigo aborda apenas o IBM DB2, mas fornece referências a outros artigos sobre como configurar outros componentes de um sistema SAP.

![Visão geral do ambiente completo de alta disponibilidade do IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Visão geral de alto nível das etapas necessárias
Para implantar uma configuração do IBM DB2, você precisa seguir estas etapas:

  + Planeje seu ambiente.
  + Implante as VMs.
  + Atualize o SUSE Linux e configure os sistemas de arquivos.
  + Instalar e configurar o pacemaker.
  + Instale o [NFS altamente disponível][nfs-ha].
  + Instale [o ASCS/ers em um cluster separado][ascs-ha].
  + Instale o banco de dados IBM DB2 com a opção distribuído/alta disponibilidade (SWPM).
  + Instale e crie um nó de banco de dados secundário e uma instância e configure o HADR.
  + Confirme se o HADR está funcionando.
  + Aplique a configuração pacemaker para controlar o IBM DB2.
  + Configurar Azure Load Balancer.
  + Instale os servidores de aplicativo primário e de caixa de diálogo.
  + Verifique e adapte a configuração dos servidores de aplicativos SAP.
  + Executar testes de failover e tomada.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planejar a infraestrutura do Azure para hospedar o IBM DB2 LUW com HADR

Conclua o processo de planejamento antes de executar a implantação. O planejamento cria a base para implantar uma configuração do DB2 com o HADR no Azure. Os elementos principais que precisam fazer parte do planejamento do IMB DB2 LUW (parte do banco de dados do ambiente SAP) estão listados na tabela a seguir:

| Tópico | Descrição breve |
| --- | --- |
| Definir grupos de recursos do Azure | Grupos de recursos em que você implanta VM, VNet, Azure Load Balancer e outros recursos. Pode ser existente ou novo. |
| Definição de rede virtual/sub-rede | Onde as VMs para IBM DB2 e Azure Load Balancer estão sendo implantadas. Pode ser existente ou criado recentemente. |
| Máquinas virtuais que hospedam o IBM DB2 LUW | Tamanho da VM, armazenamento, rede, endereço IP. |
| Nome de host virtual e IP virtual para banco de dados IBM DB2| O IP virtual ou o nome do host que é usado para conexão de servidores de aplicativos SAP. **db-virt-hostname**, **db-virt-ip**. |
| Isolamento do Azure | Isolamento do Azure ou isolamento SBD (altamente recomendado). O método para evitar situações de divisão Brain é impedido. |
| VM SBD | Tamanho da máquina virtual SBD, armazenamento, rede. |
| Azure Load Balancer | Uso de básico ou padrão (recomendado), porta de investigação para banco de dados DB2 (nossa recomendação 62500) **porta-investigação**. |
| Resolução de nomes| Como a resolução de nomes funciona no ambiente. O serviço DNS é altamente recomendado. O arquivo de hosts local pode ser usado. |
    
Para obter mais informações sobre o pacemaker do Linux no Azure, consulte [Configurar o pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Implantação no SUSE Linux

O agente de recursos para IBM DB2 LUW está incluído em SUSE Linux Enterprise Server para aplicativos SAP. Para a configuração descrita neste documento, você deve usar o servidor SUSE Linux para aplicativos SAP. O Azure Marketplace contém uma imagem para o SUSE Enterprise Server para aplicativos SAP 12 que você pode usar para implantar novas máquinas virtuais do Azure. Lembre-se dos vários modelos de suporte ou de serviço que são oferecidos pelo SUSE por meio do Azure Marketplace quando você escolhe uma imagem de VM no Marketplace de VM do Azure. 

### <a name="hosts-dns-updates"></a>Hospedar Atualizações DNS
Faça uma lista de todos os nomes de host, incluindo nomes de host virtuais, e atualize seus servidores DNS para habilitar o endereço IP apropriado para a resolução de nome de host. Se um servidor DNS não existir ou se você não puder atualizar e criar entradas DNS, será necessário usar os arquivos de host locais das VMs individuais que participam desse cenário. Se você estiver usando entradas de arquivos de host, certifique-se de que as entradas sejam aplicadas a todas as VMs no ambiente do sistema SAP. No entanto, é recomendável que você use o DNS que, idealmente, se estenda para o Azure


### <a name="manual-deployment"></a>Implantação manual

Verifique se o sistema operacional selecionado tem suporte do IBM/SAP para IBM DB2 LUW. A lista de versões de sistema operacional com suporte para VMs do Azure e versões do DB2 está disponível na observação do SAP [1928533]. A lista de versões do sistema operacional por versão individual do DB2 está disponível na matriz de disponibilidade do produto SAP. É altamente recomendável um mínimo de SLES 12 SP3 devido a melhorias de desempenho relacionadas ao Azure neste ou em versões posteriores do SUSE Linux.

1. Crie ou selecione um grupo de recursos.
1. Crie ou selecione uma rede virtual e uma sub-rede.
1. Crie um conjunto de disponibilidade do Azure ou implante uma zona de disponibilidade.
    + Para o conjunto de disponibilidade, defina o máximo de domínios de atualização como 2.
1. Crie a máquina virtual 1.
    + Use o SLES for SAP Image no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure que você criou na etapa 3 ou selecione zona de disponibilidade.
1.  Crie a máquina virtual 2.
    + Use o SLES for SAP Image no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure que você criou na etapa 3 ou selecione zona de disponibilidade (não a mesma zona que na etapa 3).
1. Adicione discos de dados às VMs e, em seguida, verifique a recomendação de uma configuração do sistema de arquivos no artigo [implantação de DBMS de máquinas virtuais do Azure DB2 para carga de trabalho do SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster pacemaker
    
Para criar um cluster pacemaker básico para este servidor IBM DB2, consulte [Configurar o pacemaker em SuSE Linux Enterprise Server no Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instalar o ambiente IBM DB2 LUW e SAP

Antes de iniciar a instalação de um ambiente SAP baseado em IBM DB2 LUW, examine a seguinte documentação:

+ Documentação do Azure
+ Documentação do SAP
+ Documentação da IBM

Links para esta documentação são fornecidos na seção introdutória deste artigo.

Verifique os manuais de instalação do SAP sobre a instalação de aplicativos baseados no NetWeaver no IBM DB2 LUW.

Você pode encontrar os guias no portal de ajuda do SAP usando o [localizador de guia de instalação do SAP][sap-instfind].

Você pode reduzir o número de guias exibidos no portal definindo os seguintes filtros:

- Eu quero: "Instalar um novo sistema"
- Meu banco de dados: "IBM DB2 para Linux, UNIX e Windows"
- Filtros adicionais para versões do SAP NetWeaver, configuração de pilha ou sistema operacional

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Dicas de instalação para configurar o IBM DB2 LUW com HADR

Para configurar a instância de banco de dados do IBM DB2 LUW principal:

- Use a opção alta disponibilidade ou distribuída.
- Instale o SAP ASCS/ERS e a instância de banco de dados.
- Faça um backup do banco de dados recém-instalado.


> [!IMPORTANT] 
> Anote a "porta de comunicação do banco de dados" que está definida durante a instalação. Deve ser o mesmo número de porta para ambas as instâncias de banco de dados

Para configurar o servidor de banco de dados em espera usando o procedimento de cópia do sistema SAP homogêneo, execute estas etapas:

1. Selecione a opção de **cópia do sistema** > instância de**banco de dados** **distribuído** > de **sistemas** > de destino.
1. Como um método de cópia, selecione **sistema homogêneo** para que você possa usar o backup para restaurar um backup na instância do servidor em espera.
1. Quando você chegar à etapa sair para restaurar o banco de dados para a cópia homogênea do sistema, saia do instalador. Restaure o banco de dados de um backup do host primário. Todas as fases de instalação subsequentes já foram executadas no servidor de banco de dados primário.
1. Configure o HADR para IBM DB2.

   > [!NOTE]
   > Para instalação e configuração específicas do Azure e pacemaker: Durante o procedimento de instalação por meio do Gerenciador de provisionamento de software SAP, há uma pergunta explícita sobre a alta disponibilidade do IBM DB2 LUW:
   >+ Não selecione **IBM DB2 pureScale**.
   >+ Não selecione **instalar IBM Tivoli System Automation para**multiplataformas.
   >+ Não selecione **gerar arquivos de configuração de cluster**.

   Ao usar um dispositivo SBD para Linux pacemaker, defina os seguintes parâmetros do DB2 HADR:
   + Duração da janela do par HADR (segundos) (HADR_PEER_WINDOW) = 300  
   + Valor de tempo limite HADR (HADR_TIMEOUT) = 60

   Ao usar um agente de isolamento de pacemaker do Azure, defina os seguintes parâmetros:
   + Duração da janela do par HADR (segundos) (HADR_PEER_WINDOW) = 900  
   + Valor de tempo limite HADR (HADR_TIMEOUT) = 60

Recomendamos os parâmetros anteriores com base no teste inicial de failover/tomada. É obrigatório que você teste a funcionalidade adequada de failover e tomada com essas configurações de parâmetro. Como as configurações individuais podem variar, os parâmetros podem exigir ajuste. 

> [!IMPORTANT]
> Específico do IBM DB2 com a configuração HADR com inicialização normal: A instância de banco de dados secundária ou em espera deve estar ativa e em execução antes que você possa iniciar a instância do banco de dados primário.

Para fins de demonstração e os procedimentos descritos neste artigo, o SID do banco de dados é **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Verificação HADR do IBM DB2
Depois de configurar o HADR e o status for par e conectado nos nós primário e em espera, execute a seguinte verificação:

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



## <a name="db2-pacemaker-configuration"></a>Configuração do DB2 pacemaker

Ao usar o pacemaker para failover automático no caso de uma falha de nó, você precisa configurar suas instâncias do DB2 e pacemaker de acordo. Esta seção descreve esse tipo de configuração.

Os seguintes itens são prefixados com um:

- **[A]** : Aplicável a todos os nós
- **[1]** : Aplicável somente ao nó 1 
- **[2]** : Aplicável somente ao nó 2

**[A]** pré-requisitos para a configuração do pacemaker:
1. Desligue os servidores de banco de dados com\<o SID do DB2 do usuário > com db2stop.
1. Altere o ambiente do Shell para\<DB2 Sid > usuário para */bin/ksh*. Recomendamos que você use a ferramenta YaST. 


### <a name="pacemaker-configuration"></a>Configuração do pacemaker

**[1]** configuração de pacemaker específica do IBM DB2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** criar recursos do IBM DB2:
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
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** iniciar recursos do IBM DB2:
* Coloque pacemaker fora do modo de manutenção.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão sendo executados.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nós configurados
# <a name="5-resources-configured"></a>5 recursos configurados

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Lista completa de recursos:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-SBD (stonith: External/SBD): Azibmdb02 iniciado
#  <a name="resource-group-gipdb2ptrptr"></a>Grupo de recursos: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (OCF:: Heartbeat: IPaddr2):       Azibmdb02 iniciado
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (OCF:: Heartbeat: qualquer coisa):      Azibmdb02 iniciado
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Conjunto mestre/escravo: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Mestres: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Servidores subordinados: [azibmdb01]
</pre>

> [!IMPORTANT]
> Você deve gerenciar a instância do DB2 clusterizado do pacemaker usando as ferramentas do pacemaker. Se você usar comandos do DB2 como db2stop, o pacemaker detectará a ação como uma falha de recurso. Se estiver executando a manutenção, você poderá colocar os nós ou recursos no modo de manutenção. O pacemaker suspende os recursos de monitoramento e você pode usar os comandos normais de administração do DB2.


### <a name="configure-azure-load-balancer"></a>Configurar o Azure Load Balancer
Para configurar Azure Load Balancer, recomendamos que você use o [SKU de Standard Load Balancer do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) e, em seguida, faça o seguinte;

1. Criar um pool de IPS de front-end:

   a. No portal do Azure, abra o Azure Load Balancer, selecione **pool de IPS de front-end**e, em seguida, selecione **Adicionar**.

   b. Insira o nome do novo pool de IPS de front-end (por exemplo, **DB2-Connection**).

   c. Defina a **atribuição** como **estática**e insira o endereço IP **virtual-IP** definido no início.

   d. Selecione **OK**.

   e. Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.

1. Criar um pool de back-ends:

   a. No portal do Azure, abra o Azure Load Balancer, selecione pools de **back-end**e, em seguida, selecione **Adicionar**.

   b. Insira o nome do novo pool de back-end (por exemplo, **DB2-backend**).

   c. Selecione **Adicionar uma máquina virtual**.

   d. Selecione o conjunto de disponibilidade ou as máquinas virtuais que hospedam o banco de dados IBM DB2 criado na etapa anterior.

   e. Selecione as máquinas virtuais do cluster IBM DB2.

   f. Selecione **OK**.

1. Criar uma investigação de integridade:

   a. No portal do Azure, abra o Azure Load Balancer, selecione **investigações de integridade**e selecione **Adicionar**.

   b. Insira o nome da nova investigação de integridade (por exemplo, **DB2-HP**).

   c. Selecione **TCP** como o protocolo e a porta **62500**. Mantenha o valor de **intervalo** definido como **5**e mantenha o valor de **limite não íntegro** definido como **2**.

   d. Selecione **OK**.

1. Crie as regras de balanceamento de carga:

   a. No portal do Azure, abra o Azure Load Balancer, selecione **regras de balanceamento de carga**e, em seguida, selecione **Adicionar**.

   b. Insira o nome da nova regra de Load Balancer (por exemplo, **DB2-Sid**).

   c. Selecione o endereço IP de front-end, o pool de back-ends e a investigação de integridade que você criou anteriormente (por exemplo, **DB2-frontend**).

   d. Mantenha o **protocolo** definido como **TCP**e insira porta de *comunicação do banco de dados*de porta.

   e. Aumente o **tempo limite de ociosidade** para 30 minutos.

   f. Certifique-se de **habilitar IP Flutuante**.

   g. Selecione **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Fazer alterações nos perfis SAP para usar o IP virtual para conexão
Para se conectar à instância primária da configuração do HADR, a camada do aplicativo SAP precisa usar o endereço IP virtual que você definiu e configurou para o Azure Load Balancer. As seguintes alterações são necessárias:

/sapmnt/\<Sid >/Profile/default. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<Sid >/global/DB6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalar servidores de aplicativos primários e de diálogo

Quando você instala servidores de aplicativos primários e de diálogo em uma configuração do DB2 HADR, use o nome de host virtual que você escolheu para a configuração. 

Se você executou a instalação antes de criar a configuração do DB2 HADR, faça as alterações conforme descrito na seção anterior e da seguinte maneira para as pilhas Java do SAP.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Verificação de URL JDBC de sistemas de pilha ABAP + Java ou Java

Use a ferramenta de configuração do J2EE para verificar ou atualizar a URL JDBC. Como a ferramenta de configuração do J2EE é uma ferramenta gráfica, você precisa ter o X Server instalado:
 
1. Entre no servidor de aplicativos primário da instância J2EE e execute:`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. No quadro à esquerda, escolha **armazenamento de segurança**.
1. No quadro à direita, escolha a chave JDBC/pool/\<sapsid >/URL.
1. Altere o nome do host na URL JDBC para o nome do host virtual.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Selecione **Adicionar**.
1. Para salvar suas alterações, selecione o ícone de disco no canto superior esquerdo.
1. Feche a ferramenta de configuração do.
1. Reinicie a instância do Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurar o arquivamento de log para a instalação do HADR
Para configurar o arquivamento de log do DB2 para a instalação do HADR, recomendamos que você configure o banco de dados primário e o em espera para ter a capacidade de recuperação de log automática de todos os locais de arquivamento de log. O banco de dados primário e o em espera devem ser capazes de recuperar arquivos de log mortos de todos os locais de arquivamento de log para os quais qualquer uma das instâncias de banco de dados pode arquivar arquivos de log. 

O arquivamento de log é executado somente pelo banco de dados primário. Se você alterar as funções HADR dos servidores de banco de dados ou se ocorrer uma falha, o novo banco de dados primário será responsável pelo arquivamento de log. Se você tiver configurado vários locais de arquivamento de log, os logs poderão ser arquivados duas vezes. No caso de uma atualização local ou remota, você também pode precisar copiar manualmente os logs arquivados do servidor primário antigo para o local de log ativo do novo servidor primário.

É recomendável configurar um compartilhamento NFS comum em que os logs são gravados de ambos os nós. O compartilhamento NFS precisa estar altamente disponível. 

Você pode usar compartilhamentos NFS altamente disponíveis existentes para transportes ou um diretório de perfil. Para obter mais informações, consulte:

- [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server][nfs-ha] 
- [Alta disponibilidade para SAP NetWeaver em VMs do Azure em SUSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (para criar compartilhamentos NFS)


## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta seção descreve como você pode testar a configuração do DB2 HADR. *Cada teste pressupõe que você está conectado como raiz do usuário* e o IBM DB2 primário está em execução na máquina virtual *azibmdb01* .

O status inicial de todos os casos de teste é explicado aqui: (crm_mon-r ou CRM status)

- o **status do CRM** é um instantâneo do status do pacemaker no tempo de execução 
- **crm_mon-r** é uma saída contínua do status do pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

O status original em um sistema SAP está documentado em transação DBACOCKPIT > Configuração > Visão geral, conforme mostrado na imagem a seguir:

![DBACockpit-pré-migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Testar tomada do IBM DB2


> [!IMPORTANT] 
> Antes de iniciar o teste, verifique se:
> * Pacemaker não tem nenhuma ação com falha (status do CRM).
> * Não há restrições de local (sobras de teste de migração)
> * A sincronização do IBM DB2 HADR está funcionando. Verificar com o >\<de SID do DB2 do usuário <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migre o nó que está executando o banco de dados DB2 primário executando o seguinte comando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Depois que a migração for concluída, a saída do status do CRM será parecida com esta:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

O status original em um sistema SAP está documentado em transação DBACOCKPIT > Configuração > Visão geral, conforme mostrado na imagem a seguir:

![DBACockpit-pós-migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

A migração de recursos com a "migração de recursos do CRM" cria restrições de local. As restrições de local devem ser excluídas. Se as restrições de local não forem excluídas, o recurso não poderá realizar failback ou você poderá experimentar takeovers indesejados. 

Migre o recurso de volta para *azibmdb01* e desmarque as restrições de local
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **RES_NAME do host \<de migração \<de recursos do CRM > >:** Cria restrições de local e pode causar problemas com tomada
- **> \<RES_NAME de recursos do CRM**: Limpa as restrições de local
- **> RES_NAME de \<limpeza de recursos CRM**: Limpa todos os erros do recurso

### <a name="test-the-fencing-agent"></a>Testar o agente de isolamento

Nesse caso, testamos o isolamento SBD, que recomendamos que você faça ao usar o SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

O nó de cluster *azibmdb01* deve ser reinicializado. A função HADR principal do IBM DB2 será movida para *azibmdb02*. Quando o *azibmdb01* estiver online novamente, a instância do DB2 será movida na função de uma instância de banco de dados secundária. 

Se o serviço pacemaker não iniciar automaticamente no primário reinicializado anterior, certifique-se de iniciá-lo manualmente com:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testar um tomada manual

Você pode testar um tomada manual interrompendo o serviço pacemaker no nó *azibmdb01* :
<pre><code>service pacemaker stop</code></pre>

status em *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Após o failover, você pode iniciar o serviço novamente em *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Eliminar o processo do DB2 no nó que executa o banco de dados primário HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

A instância do DB2 irá falhar e o pacemaker relatará o seguinte status:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

O pacemaker reiniciará a instância do banco de dados primário do DB2 no mesmo nó ou fará failover para o nó que está executando a instância do banco de dados secundário e um erro será relatado.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Eliminar o processo do DB2 no nó que executa a instância do banco de dados secundário

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

O nó entra em um erro declarado e foi reportado
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

A instância do DB2 é reiniciada na função secundária que foi atribuída anteriormente.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Parar o BD via db2stop Force no nó que executa a instância do banco de dados primário HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Como o usuário\<DB2 Sid > execute o comando db2stop Force:
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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

A instância do banco de dados secundário do DB2 HADR foi promovida para a função primária
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Falha na VM com reinicialização no nó que executa a instância do banco de dados primário HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker promoverá a instância secundária para a função de instância primária. A antiga instância primária será movida para a função secundária depois que a VM e todos os serviços forem totalmente restaurados após a reinicialização da VM:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Falhar a VM que executa a instância do banco de dados primário HADR com "Halt"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Nesse caso, o pacemaker detectará que o nó que está executando a instância do banco de dados primário não está respondendo.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

A próxima etapa é verificar uma situação de *divisão Brain* . Depois que o nó sobrevivente determinar que o nó executado pela última vez a instância do banco de dados primário está inativo, um failover dos recursos será executado.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


No caso de uma "parada" do nó, o nó com falha precisa ser reiniciado por meio das ferramentas de gerenciamento do Azure (no portal do Azure, no PowerShell ou no CLI do Azure). Depois que o nó com falha estiver online novamente, ele iniciará a instância do DB2 na função secundária.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Próximas etapas
- [Arquitetura e cenários de alta disponibilidade para o SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurar o pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

