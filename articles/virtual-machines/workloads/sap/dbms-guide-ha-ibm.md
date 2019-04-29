---
title: Configurar o IBM Db2 HADR em máquinas virtuais (VMs) | Microsoft Docs
description: Estabeleça a alta disponibilidade do IBM Db2 LUW em máquinas virtuais (VMs).
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
ms.openlocfilehash: 3c1d0e252b5c658ab6da2b3932918f05ba651d52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835903"
---
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
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Alta disponibilidade do IBM Db2 LUW em VMs do Azure no SUSE Linux Enterprise Server com o Pacemaker

IBM Db2 LUW (Linux, Unix e Windows) no [configuração HADR](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) consistem em um nó que executa uma instância de banco de dados primário e pelo menos um nó que executa uma instância de banco de dados secundário. As alterações para a instância de banco de dados primário são replicadas a instância de banco de dados secundário de forma síncrona ou assíncrona, depende de sua configuração. 

Este artigo descreve como implantar e configurar as máquinas virtuais, instalar a estrutura de cluster e instalar e configurar o IBM Db2 LUW na configuração HADR. O artigo não explicará como instalar e configurar o IBM Db2 LUW na instalação do software SAP ou HADR. Referências a SAP e IBM manuais de instalação são fornecidas para realizar essas tarefas. Foco é em partes que são específicos ao ambiente do Azure. 

Versões do IBM Db2 com suporte são 10.5 e superior, conforme documentado na nota SAP n º[1928533].

Leia as seguintes notas SAP e a documentação antes de se aproximando uma instalação:

| Nota do SAP | DESCRIÇÃO |
| --- | --- |
| [1928533] | Aplicativos SAP no Azure: Produtos suportados e tipos de VM do Azure |
| [2015553] | SAP no Microsoft Azure: pré-requisitos de suporte |
| [2178632] | Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [2191498] | SAP no Linux com o Azure: Monitoramento Avançado |
| [2243692] | Linux na VM (IaaS) do Microsoft Azure: Problemas de licença do SAP |
| [1984787] |SUSE Linux Enterprise Server 12: Notas de instalação |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2233094] |DB6: Aplicativos SAP no Azure usando o IBM Db2 para Linux, UNIX e Windows - informações adicionais |
| [1612105] |DB6: Perguntas Frequentes sobre a recuperação de desastres de alta disponibilidade do Db2 (HADR) |


| Documentação | 
| --- |
| [WIKI da comunidade SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP para Linux |
| [Máquinas virtuais de planejamento e implementação para SAP no Linux do Azure] [ planning-guide] guia |
| [Implantação de máquinas virtuais do Azure para SAP no Linux] [ deployment-guide] (Este artigo) |
| [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux] [ dbms-guide] guia |
| [Carga de trabalho do SAP na lista de verificação de planejamento e implantação do Azure][azr-sap-plancheck] |
| [Guias de melhores práticas do SUSE Linux Enterprise Server for SAP Applications 12 SP3][sles-for-sap-bp] |
| [SUSE Linux Enterprise alta disponibilidade extensão 12 SP3][sles-ha-guide] |
| [Implantação de DBMS de máquinas virtuais do IBM Db2 do Azure para carga de trabalho do SAP][dbms-db2] |
| [Recuperação de desastres do IBM Db2 alta disponibilidade 11.1][db2-hadr-11.1] |
| [Recuperação de desastres do IBM Db2 alta disponibilidade 10.5 R][db2-hadr-10.5] |

## <a name="overview"></a>Visão geral
Para obter alta disponibilidade, IBM Db2 LUW com HADR é instalado em pelo menos duas máquinas virtuais do Azure, que são implantadas em um [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ou de vários [zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). Os gráficos a seguir mostra uma configuração de servidor de banco de dados de duas VMs do Azure. Tanto o servidor de banco de dados as VMs do Azure têm seu próprio armazenamento anexado e estão em execução. No HADR, uma instância do banco de dados em uma das VMs do Azure tem a função da instância primária. Todos os clientes estão conectados a esta instância primária. Todas as alterações em transações de banco de dados mantidas localmente no log de transações do Db2. Conforme os registros de log de transação são mantidos localmente, os registros são transferidos por meio de TCP/IP para a instância de banco de dados no segundo servidor de banco de dados, o servidor em espera ou instância em espera. A instância em espera atualiza o banco de dados local, roll forward da transação transferida registros de log. Portanto, o servidor em espera é mantido em sincronizado com o servidor primário.

HADR é apenas uma funcionalidade de replicação. Ele possui nenhuma detecção de falhas e nenhum recurso de tomada de controle ou o failover automático. Uma tomada de controle ou a transferência para o modo de espera deve ser iniciada manualmente por um administrador de banco de dados. Para alcançar uma tomada de controle automático e detecção de falha, você pode usar o recurso de cluster do Linux Pacemaker. Pacemaker monitora as banco de dados de dois servidores/instâncias. Quando o servidor/instância do banco de dados primário falhar, inicia Pacemaker uma **automática** tomada de controle HADR pelo servidor em espera e também garante que o endereço IP virtual é atribuído ao novo servidor primário.

![Visão geral do IBM Db2 alta disponibilidade](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Para o SAP servidores de aplicativos para se conectar ao banco de dados primário, você precisam de um nome de host virtual e um endereço IP virtual. No caso de um failover, os servidores de aplicativos SAP se conectará à nova instância de banco de dados primário. Em um ambiente do Azure, uma [balanceador de carga do Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) é necessário para usar um endereço IP virtual da forma necessária para HADR do IBM Db2. 

Para entender completamente, como IBM Db2 LUW com HADR e o Pacemaker se adapta a uma configuração do sistema SAP altamente disponível, a imagem a seguir apresenta uma visão geral de uma configuração altamente disponível de um sistema SAP com base no banco de dados IBM Db2. Este artigo aborda somente o IBM Db2 e as referências a outros artigos sobre como configurar outros componentes do sistema SAP.

![Visão geral do ambiente IBM DB2 HA completo](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-steps-needed"></a>Visão geral de alto nível das etapas necessárias
Para implantar uma configuração do IBM Db2, essas etapas precisam ser abordados:

  + Plano de ambiente
  + Implantar VMs
  + Atualizar o SUSE Linux e configurar sistemas de arquivos
  + Instalar e configurar o Pacemaker
  + Instalar [NFS altamente disponível][nfs-ha]
  + Instalar [ASCS/ERS no cluster separado][ascs-ha] 
  + Instalar o banco de dados do IBM Db2 com a opção Distributed/alta disponibilidade (SWPM)
  + Instalar/criar nó de banco de dados secundário e a instância e configurar HADR
  + Confirme se o HADR está funcionando
  + Aplicar a configuração do Pacemaker para controle IBM Db2
  + Configurar o Azure Load Balancer 
  + Instalar primário + diálogo servidores de aplicativos
  + Seleção/adapte a configuração de servidores de aplicativos SAP
  + Executar failover / testa a tomada de controle



## <a name="planning-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planejamento de infraestrutura do Azure para hospedar o IBM Db2 LUW com HADR

Percorra o planejamento da antes de executar a implantação. Ele está criando a base para a implantação de uma configuração do Db2 com HADR no Azure. Elementos principais que precisam ser parte do planejamento de IMB Db2 LUW (parte do banco de dados do ambiente do SAP).

| Tópico | Descrição breve |
| --- | --- |
| Definir grupos de recursos do Azure | Grupos de recursos em que você implantar a VM, rede virtual, balanceador de carga do Azure e outros recursos. Pode ser novo ou existente |
| Rede virtual / definição de sub-rede | Onde as VMs para IBM Db2 e o balanceador de carga do Azure estão recebendo a ser implantado. Pode ser existente ou recém-criado |
| Máquinas virtuais que hospedam LUW do IBM Db2 | VM size, storage, networking, IP address |
| Nome de host virtual & IP virtual para o banco de dados IBM Db2| IP/nome de host virtual que é usado para conexão de servidores de aplicativos SAP. **db-virt-hostname**, **db-virt-ip** |
| Isolamento do Azure | Isolamento do Azure ou o isolamento do SBD (altamente recomendado). Método para evitar situações de dupla personalidade de divisão é impedido |
| VM DE SBD | Tamanho da máquina Virtual de SBD, armazenamento, rede |
| Azure Load Balancer | Uso de Basic ou Standard (recomendado), a investigação de porta para o banco de dados do Db2 (nossa recomendação 62500) **porta de investigação** |
| Resolução de nomes| Como funciona a resolução de nome no ambiente. Serviço DNS é altamente recomendável. Arquivo de hosts local pode ser usado. |
    
Para obter mais detalhes sobre o uso do Linux Pacemaker no Azure podem ser encontradas nesses artigos:

- [Configurar Pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)



## <a name="deployment-on-suse-linux"></a>Implantação no SUSE Linux

O agente de recursos para IBM Db2 LUW está incluído no SUSE Linux Enterprise Server para aplicativos SAP. Para a configuração descrita neste documento, é obrigatório que você use o servidor do Linux SUSE para aplicativos SAP. O Azure Marketplace contém uma imagem para SUSE Enterprise Server for SAP Applications 12 que você pode usar para implantar novas máquinas virtuais do Azure. Lembre-se dos modelos diferentes de serviço/suporte oferecidos pelo SUSE por meio de galerias do Azure ao escolher a imagem VM na Galeria de VM do Azure. 

### <a name="hosts---dns-updates"></a>Hosts - atualizações de DNS
Faça uma lista de todos os nomes de host, incluindo nomes de host virtual e atualizar seus servidores DNS para habilitar o endereço IP apropriado para a resolução de nome de host. Nesse caso, um servidor DNS não existe ou não é possível atualizar e criar entradas DNS, é necessário para aproveitar os arquivos de hosts local das VMs individuais que estão participando nesse cenário. No caso de uso de entradas de arquivos de host, você precisa certificar-se de que as entradas sejam aplicadas a todas as VMs no ambiente do sistema SAP. No entanto, a recomendação é usar seu DNS que o ideal é que é estendido para o Azure


### <a name="manual-deployment"></a>Implantação manual

Certifique-se de que o sistema operacional selecionado é suportado pela IBM/SAP LUW do IBM Db2. A lista de versões de sistema operacional com suporte para versões do Db2 e VMs do Azure está disponível na nota do SAP [1928533]. A lista de versões do sistema operacional por versões individuais de Db2 está disponível na matriz de disponibilidade de produto SAP. É altamente recomendável que um mínimo de SLES 12 SP3 devido aos aprimoramentos de desempenho relacionados do Azure neste ou posterior versões do SUSE Linux.

1. Criar/selecionar um grupo de recursos
2. Criar/selecionar uma rede virtual e sub-rede
3. Criar conjunto de disponibilidade do Azure ou implantar na zona de disponibilidade
    + Disponibilidade definir - definir domínios de atualização máxima para dois
4. Crie a máquina Virtual 1.
    + Use o SLES para SAP image na Galeria do Azure
    + Selecione o conjunto criado na etapa 3, ou selecione zona de disponibilidade de disponibilidade do Azure
5.  Crie a máquina Virtual 2.
    + Use o SLES para SAP image na Galeria do Azure
    + Selecione o conjunto criado na etapa 3 de disponibilidade do Azure. ou selecione a zona de disponibilidade - não a mesma zona como na etapa 3.
6. Adicionar discos de dados para as máquinas virtuais - Verifique a recomendação da instalação do sistema de arquivos no artigo [implantação de DBMS de máquinas virtuais do IBM Db2 do Azure para carga de trabalho do SAP][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster do Pacemaker
    
Siga as etapas em [configurar Pacemaker no SUSE Linux Enterprise Server no Azure][sles-pacemaker] para criar um cluster Pacemaker básico para esse servidor IBM Db2. 

## <a name="install-ibm-db2-luw-and-sap-environment"></a>Instalar o ambiente de SAP e IBM Db2 LUW

Antes de iniciar a instalação de um ambiente do SAP com base em IBM Db2 LUW, examine (links fornecidos no início do artigo):

+ Documentação do Azure
+ Documentação do SAP
+ Documentação do IBM

Verifique os manuais de instalação do SAP sobre como instalar aplicativos NetWeaver com base em IBM Db2 LUW.

Você pode encontrar os guias sobre como usar o portal SAP para o [localizador de guia de instalação do SAP][sap-instfind]

Você pode filtrar a pesquisa para reduzir o número de guias disponíveis com os filtros de configuração:

+ Eu quero: "Instalar um novo sistema"
+ Meu banco de dados: "IBM Db2 para Linux, Unix e Windows"
+ Filtros adicionais para as versões do SAP Netweaver, configuração da pilha ou sistema operacional.

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Dicas de instalação para configurar o IBM Db2 LUW com HADR

Configure a instância de banco de dados IBM Db2 LUW primária:

- Use a opção distribuída ou alta disponibilidade
- Instalar instância ASCS/ERS do SAP e o banco de dados
- Faça um backup do banco de dados recentemente instalado


> [!IMPORTANT] 
> Anote a "porta de comunicação de banco de dados" definidas durante a instalação. Ele deve ser o mesmo número de porta para ambas as instâncias de banco de dados

Para configurar o servidor de banco de dados em espera usando o procedimento de cópia homogênea de sistema do SAP, execute estas etapas:

  - Usar a opção de cópia do sistema - sistemas - distribuídos - instância de banco de dados de destino.
  - Como o método de cópia, escolha a cópia homogênea do sistema para que você pode usar o backup para restaurar um backup de servidor/instância em espera
  - Quando você chegar à etapa de saída para restaurar o banco de dados para cópia homogênea de sistema, sair do instalador. Restaure o banco de dados de um backup do host primário. Todas as fases subsequentes de instalação já foram executadas no servidor de banco de dados primário
- Configurar HADR para IBM Db2

> [!NOTE]
> Instalação/configuração específica do Azure e o Pacemaker. Durante o procedimento de instalação por meio do SAP Software Provisioning Manager, há uma questão explícita sobre a alta disponibilidade para LUW do IBM Db2:
>+ Não selecione pureScale IBM Db2
>+ Não selecione "instalar o IBM Tivoli / sistema de automação para Multiplatforms
>+ Não selecione "Gerar arquivos de configuração de cluster"

> [!NOTE]
>Ao usar um dispositivo SBD para Linux Pacemaker, defina os parâmetros de Db2 HADR
>+ Duração da janela de ponto a ponto HADR (segundos) (HADR_PEER_WINDOW) = 300  
>+ O valor de tempo limite HADR (HADR_TIMEOUT) = 60

> [!NOTE]
>Usando o agente de isolamento do Pacemaker do Azure:
>+ Duração da janela de ponto a ponto HADR (segundos) (HADR_PEER_WINDOW) = 900  
>+ O valor de tempo limite HADR (HADR_TIMEOUT) = 60

Parâmetros são recomendados com base nos testes de tomada de controle/failover inicial. É obrigatório para testar a funcionalidade correta do failover com essas configurações de parâmetro e a tomada de controle. Uma vez que as configurações individuais podem variar, esses parâmetros podem exigir ajuste. 

> [!IMPORTANT]
> Específico para o IBM Db2 na configuração de HADR com a inicialização normal - a instância de banco de dados secundário/em espera deve estar em execução antes de poder iniciar instância de banco de dados primário.

Para fins de demonstração e os procedimentos documentados neste documento, o banco de dados SID é **PTR**.

##### <a name="ibm-db2-hadr-check"></a>Seleção HADR do IBM Db2
Uma vez configurado o HADR e o status é par e o conectado em nós primários e em espera.

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



## <a name="db2-pacemaker-configuration"></a>Configuração do Pacemaker do Db2

Quando você usa o Pacemaker para failover automático no caso de uma falha de nó, você precisa configurar suas instâncias de Db2 e o Pacemaker adequadamente. Esta seção descreve esse tipo de configuração.

Os itens a seguir são prefixados com:

- **[A]**  – aplicável a todos os nós
- **[1]**  – aplicável somente ao nó 1 
- **[2]**  – aplicável somente ao nó 2.

<!-- Yast is a fixed term in Linux and not a spelling error -->

**[A]**  Pré-requisitos para configuração do Pacemaker:
1. Desligar de ambos os servidor de banco de dados com o usuário db2\<sid > com db2stop
2. Ambiente de shell de alteração para db2\<sid > usuário "/ bin/ksh" - recomendável para usar a ferramenta Yast 


### <a name="pacemaker-configuration"></a>Configuração do pacemaker:

**[1]**  Configuração específica de Pacemaker HADR do IBM Db2
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Recursos criar IBM Db2
<pre><code># Replace **bold strings** with your instance name db2sid, database SID and virtual IP address/Azure Load Balancer

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virutal IP - same as Azure Load Balancer IP
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

**[1]**  Recursos iniciar IBM Db2 - colocados Pacemaker do modo de manutenção
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Certifique-se de que o status do cluster é okey e que todos os recursos sejam iniciados. Não é importante saber em qual nó os recursos estão sendo executados.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nós configurados
# <a name="5-resources-configured"></a>5 recursos configurados

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Lista completa de recursos:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd): Azibmdb02 iniciado
#  <a name="resource-group-gipdb2ptrptr"></a>Grupo de recursos: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Azibmdb02 iniciado
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat: nada):      Azibmdb02 iniciado
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Conjunto de mestre/escravo: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Mestres: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Servidores subordinados: [azibmdb01]
</pre>

> [!IMPORTANT]
> Gerenciando o Pacemaker instância clusterizada do Db2 deve ser feita por meio de ferramentas do Pacemaker. Usando comandos de db2 (como db2stop) será detectado pelo Pacemaker como falha de recurso. No caso de manutenção, você pode colocar os nós ou recursos para o modo de manutenção e Pacemaker suspender o monitoramento de recursos e comandos de administração do db2 normal podem ser usados.


### <a name="configure-azure-load-balancer"></a>Configurar o Azure Load Balancer
É recomendável usar o [SKU do Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. Configure o balanceador de carga do Azure (por meio do portal do Azure). Primeiro, crie um pool de IP de front-end:

   1. No portal do Azure, abra o balanceador de carga do Azure, selecione **pool IP de front-end**e selecione **Add**.
   2. Insira o nome do novo pool de IPS de front-end (por exemplo, **conexão Db2**).
   3. Defina as **atribuição** para **estático** e insira o endereço IP **IP Virtual** definido no início.
   4. Selecione **OK**.
   5. Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.

2. Próxima etapa é criar um pool de back-end:

   1. No portal do Azure, abra o balanceador de carga do Azure, selecione **pools de back-end**e selecione **Add**.
   2. Insira o nome do novo pool de back-end (por exemplo, **back-end do Db2**).
   3. Selecione **Adicionar uma máquina virtual**.
   4. Selecione as máquinas virtuais/conjunto de disponibilidade hospeda o banco de dados do IBM Db2 criado na etapa 3.
   5. Selecione as máquinas virtuais do cluster IBM Db2.
   6. Selecione **OK**.

3. Terceira etapa é criar uma investigação de integridade:

   1. No portal do Azure, abra o balanceador de carga do Azure, selecione **investigações de integridade**e selecione **Add**.
   2. Insira o nome da nova investigação de integridade (por exemplo, **Db2-hp**).
   3. Selecione **TCP** como o protocolo e porta **62500**. Mantenha o valor do **Intervalo** como 5 e o valor **Limite não íntegro** como 2.
   4. Selecione **OK**.

4. Crie as regras de balanceamento de carga:

   1. No portal do Azure, abra o balanceador de carga do Azure, selecione **regras de balanceamento de carga**e selecione **Add**.
   2. Insira o nome da nova regra de Balanceador de carga (por exemplo, **Db2 SID**).
   3. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **front-end de Db2**).
   4. Manter o **protocolo** definido como **TCP**e insira a porta *porta de comunicação de banco de dados*.
   5. Aumente o **tempo limite de ociosidade** para 30 minutos.
   6. Certifique-se de **habilitar IP Flutuante**.
   7. Selecione **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Fazer alterações em perfis SAP para usar o IP virtual para conexão
A camada de aplicativo SAP precisa usar o endereço IP virtual definido e configurado para o balanceador de carga do Azure conectar-se à instância primária da configuração HADR. As seguintes alterações são necessárias.

/sapmnt/\<SID >/perfil/padrão. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalação principal e servidores de aplicativo de caixa de diálogo

Quando instalar primário e servidores de aplicativos de caixa de diálogo em relação a uma configuração HADR do Db2, você deve usar nome de host virtual que você escolheu para a configuração. 

No caso de você executou a instalação antes de criar a configuração HADR do Db2, você precisará fazer alterações, conforme descrito no parágrafo anterior e para as pilhas de Java do SAP, da seguinte maneira.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Seleção de sistemas de pilha ABAP + Java ou Java URL JDBC

Use a ferramenta de configuração do J2EE para verificar ou atualizar a URL do JDBC. O a ferramenta de configuração do J2EE é a ferramenta gráfica, como resultado, você precisa **X servidor** instalado:
 
1. Entre no servidor de aplicativos principal da instância do J2EE e execute:
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
2. No quadro esquerdo, escolha o repositório de segurança.
2. No quadro à direita, escolha o chave/pool dejdbc/<SAPSID>/url.
2. Altere o nome do host na URL do JDBC para o nome de host virtual <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
5. Escolha Adicionar.
5. Para salvar suas alterações, clique no ícone de disco no canto superior esquerdo.
5. Feche a ferramenta de configuração.
5. Reinicie a instância de Java.

## <a name="configuration-of-log-archiving-for-hadr-setup"></a>Configuração de arquivamento de log para a instalação do HADR
Para configurar o log de Db2 para a instalação do HADR de arquivamento, é recomendável que você configure o primário e o banco de dados em espera ter a capacidade de recuperação de log automático de todos os locais de arquivo de log. O principal e o banco de dados em espera devem ser capazes de recuperar arquivos de log de todos os locais de arquivo de log na qual qualquer um do banco de dados instâncias podem arquivar os arquivos de log. 

O arquivamento de log só é executado pelo banco de dados primário. Se você alterar as funções HADR dos servidores de banco de dados ou se ocorrer uma falha, o novo banco de dados primário é responsável para arquivamento de log. Se você tiver definido os locais de arquivo de log diferente, os logs podem ser arquivados duas vezes e, no caso de ajuste de local ou remoto, você talvez precise copiar manualmente os logs arquivados do servidor principal antigo para o local do log ativo do novo servidor primário.

Recomendamos configurar o compartilhamento NFS comuns em que os logs são gravados nos dois nós. NFS deve ser altamente disponível. 

Você pode usar existente NFS altamente disponível, usado para transportes, diretório de perfil. Ler:

- [Alta disponibilidade do NFS em VMs do Azure no SUSE Linux Enterprise Server][nfs-ha] 
- [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com arquivos do Azure NetApp para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) sobre como usar [arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) para criar compartilhamentos NFS


## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta seção descreve como você pode testar a instalação do HADR do Db2. **Cada teste supõe que você efetuou logon como usuário raiz** e o IBM Db2 primário está em execução no **azibmdb01** máquina virtual.

O status inicial para todos os casos de teste é explicado aqui: (crm_mon - r ou crm status)

- **status de CRM** é um status de Pacemaker de instantâneo no tempo de execução 
- **crm_mon - r** contínua de saída do status do Pacemaker

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

O status original em um sistema SAP está documentado no DBACOCKPIT de transação--> Configuração--> Visão geral, como:

![DBACockpit - anterior à migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Tomada de controle de teste do IBM Db2.


> [!IMPORTANT] 
> Antes de iniciar o teste, certifique-se de que Pacemaker não tem quaisquer ações com falha (status do crm) e não há nenhuma restrição de local (sobras do teste de migração) e a sincronização do IBM Db2 HADR está funcionando. Entre em contato com o usuário db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migre o nó que executa o banco de dados primário do Db2, executando o seguinte comando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Quando a migração estiver concluída, a saída de status de crm é semelhante:
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

O status original em um sistema SAP está documentado no DBACOCKPIT de transação--> Configuração--> Visão geral, como: ![DBACockpit - após a migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Recursos de migração com "migrar recursos de crm" cria restrições de local. Restrições de local devem ser excluídas. Se restrições de local não são excluídas, o recurso não é possível realizar o failback, ou você pode enfrentar aquisições indesejadas. 

Migrar recurso de volta para **azibmdb01** e desmarque as restrições de local
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>


- recursos de CRM migrar < res_name > <host> – cria restrições de local e pode causar problemas com a tomada de controle
- limpa de CRM recurso claro < res_name - > restrições de local
- Limpeza de recursos de CRM < res_name > - limpa todos os erros do recurso


### <a name="test-the-fencing-agent"></a>O agente de isolamento de teste

Nesse caso, podemos testar o isolamento do SBD, que é recomendado para uso com o SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Nó de cluster **azibmdb01** deve ser reinicializado. Função do IBM Db2 primária HADR vai ser movido para **azibmdb02**. Quando **azibmdb01** é voltam a ficar online, o Db2 instância está prestes a mover a função de uma instância de banco de dados secundário. 

Para o caso em que o serviço Pacemaker não for iniciado automaticamente no primeiro reinicializado primário, certifique-se de iniciá-lo manualmente com:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testar uma tomada de controle manual

Você pode testar uma tomada de controle manual interrompendo o serviço Pacemaker no **azibmdb01** nó:
<pre><code>service pacemaker stop</code></pre>

status em **azibmdb02**
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

Após o failover, você pode iniciar o serviço novamente no **azibmdb01**.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-db2-process-on-the-node-running-the-hadr-primary-database"></a>Interromper o processo do Db2 no nó que executa o banco de dados primário de HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

A instância do Db2 está prestes a falhar e o Pacemaker relatará o status a seguir:

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

Pacemaker vai reiniciar a instância de banco de dados primário do Db2 no mesmo nó ou o failover para o nó que executa o banco de dados secundário de instância e um erro será relatado.

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


### <a name="kill-db2-process-on-node-that-runs-the-secondary-database-instance"></a>Interromper o processo do Db2 no nó que executa a instância de banco de dados secundário

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

O nó obtém em falha indicado e erro relatado
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

O get de instância de Db2 reiniciado na função secundária de que tivesse atribuída antes

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



### <a name="stop-db-via-db2stop-force-on-node-running-the-hadr-primary-database-instance"></a>Parar o banco de dados por meio de força db2stop no nó que executa a instância de banco de dados primário de HADR

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

Como usuário db2\<sid > Executar força de db2stop de comando:
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

A instância de banco de dados secundário HADR Db2 foi promovida para a função primária
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


### <a name="crash-vm-with-restart-on-node-running-the-hadr-primary-database-instance"></a>Falhas VM com a reinicialização no nó que executa a instância de banco de dados primário de HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker promoverá a instância secundária para a função da instância primária. A instância primária antiga moverá para a função secundária após a VM e todos os serviços sejam totalmente restaurados após a reinicialização de VM:

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



### <a name="crash-the-vm-running-the-hadr-primary-database-instance-with-halt"></a>A VM que executa a instância de banco de dados primário de HADR com "halt" de falhas

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Nesse caso, o Pacemaker detectará que o nó que executa a instância de banco de dados primário não está respondendo.

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

a próxima etapa é verificar se há um **divisão de dupla personalidade** situação. Depois que o nó sobrevivente for-se de que o nó, que a instância de banco de dados primário for executado pela última vez, está inativo, um failover de recursos está prestes a ser executado
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


No caso de "parar" no nó, o nó com falha deve ser reiniciado por meio de ferramentas de gerenciamento do Azure (Portal, PowerShel, AzureCLI,...). O nó com falha será iniciar a instância do Db2 em função secundária, quando ele estiver online novamente.

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
Consulte esta documentação:

- [Arquitetura de alta disponibilidade e cenários para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurar Pacemaker no insira do SUSE Linux
- pri
- Se o servidor no Azure] (https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

