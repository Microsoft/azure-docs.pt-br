---
title: Configuração de alta disponibilidade com STONITH para o SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Estabelecer alta disponibilidade para o SAP HANA no Azure (Instâncias Grandes) no SUSE usando o STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7cbec63cb04075977c167d8b21bf3128e91434f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710046"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Configuração de alta disponibilidade no SUSE usando o STONITH
Este documento fornece instruções passo a passo detalhadas para configurar a Alta Disponibilidade no sistema operacional SUSE usando o dispositivo STONITH.

**Aviso de isenção de responsabilidade:** *este guia deriva do teste da configuração no ambiente Microsoft HANA em Instâncias Grandes, que funciona com êxito. Como a equipe de Gerenciamento de Serviços da Microsoft do HANA em Instâncias Grandes não oferece suporte ao Sistema operacional, talvez seja necessário entrar em contato com o SUSE para solução de problemas ou esclarecimentos sobre a camada do sistema operacional. A equipe de Gerenciamento de Serviço da Microsoft faz a configuração do dispositivo STONITH, dá suporte total e pode ajudar na solução de problemas do dispositivo STONITH.*
## <a name="overview"></a>Visão geral
Para configurar a Alta disponibilidade usando o clustering SUSE, é necessário atender aos pré-requisitos a seguir.
### <a name="pre-requisites"></a>Pré-requisitos
- O HANA em Instâncias Grandes está provisionado
- O sistema operacional está registrado
- Os servidores do HANA em Instâncias Grandes estão conectados ao servidor SMT para obter patches/pacotes
- O sistema operacional tem os patches mais recentes instalados
- O NTP (servidor de horário) está configurado
- Leia e compreenda a versão mais recente da documentação do SUSE na configuração de HA

### <a name="setup-details"></a>Detalhes de configuração
Este guia usa as seguintes configurações:
- Sistema operacional: SLES 12 SP1 para SAP
- HANA em Instâncias Grandes: 2xS192 (quatro soquetes, 2 TB)
- Versão do HANA: HANA 2.0 SP1
- Nomes do Servidor: sapprdhdb95 (node1) e sapprdhdb96 (node2)
- Dispositivo STONITH: dispositivo STONITH baseado em iSCSI
- Configuração do NTP em um nó do HANA em Instâncias Grandes

Quando você configura o HANA em Instâncias Grandes com HSR, pode solicitar à equipe de Gerenciamento de Serviço da Microsoft para configurar o STONITH. Se você já é cliente, tem um HANA em Instâncias Grandes provisionado e precisa configurar o dispositivo STONITH para suas folhas existentes, forneça as informações a seguir para a equipe de Gerenciamento de Serviços da Microsoft no SRF (formulário de solicitação de serviço). Você pode solicitar o formulário SRF ao Gerente Técnico de Conta ou ao seu Contato da Microsoft para integração do HANA em Instâncias Grandes. Os novos clientes podem solicitar o dispositivo STONITH no momento do provisionamento. As entradas estão disponíveis no formulário de solicitação de provisionamento.

- Nome e endereço IP do servidor (por exemplo, myhanaserver1, 10.35.0.1)
- Local (por exemplo, Leste dos EUA)
- Nome do cliente (por exemplo, Microsoft)
- SID – Identificador de sistema do HANA (por exemplo, H11)

Depois que o dispositivo STONITH estiver configurado, a equipe de Gerenciamento de Serviços da Microsoft fornecerá o nome do dispositivo SBD e o endereço IP do armazenamento iSCSI que você poderá utilizar para configurar a instalação do STONITH. 

Para configurar a HA de ponta a ponta usando o STONITH, é necessário seguir as etapas abaixo:

1.  Identificar o dispositivo SBD
2.  Inicializar o dispositivo SBD
3.  Configurar o cluster
4.  Configurar o watchdog Softdog
5.  Unir o nó ao cluster
6.  Validar o cluster
7.  Configurar os recursos para o cluster
8.  Testar o processo de failover

## <a name="1---identify-the-sbd-device"></a>1.   Identificar o dispositivo SBD
Esta seção descreve como determinar o dispositivo SBD para sua configuração depois que a equipe de Gerenciamento de Serviços da Microsoft configurar o STONITH. **Esta seção aplica-se somente a clientes existentes**. Para novos clientes, a equipe de Gerenciamento de Serviços da Microsoft fornecerá o nome do dispositivo SBD para você e esta seção poderá ser ignorada.

1.1 Modifique */etc/iscsi/initiatorname.isci* para 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

O Gerenciamento de Serviços da Microsoft fornece essa cadeia de caracteres. Modifique o arquivo em **ambos** os nós, porém o número de nó é diferente para cada nó.

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Modifique */etc/iscsi/iscsid.conf*: Defina *node.session.timeo.replacement_timeout=5* e *node.startup = automatic*. Modifique o arquivo em **ambos** os nós.

1.3 Executar o comando de descoberta; ele mostra quatro sessões. Execute-o em ambos os nós.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Executar o comando para fazer logon no dispositivo iSCSI; ele mostra quatro sessões. Execute-o em **ambos** os nós.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Executar o script para examinar novamente: *rescan-scsi-bus.sh*.  Esse script mostra os novos discos criados para você.  Execute-o em ambos os nós. Você deve ver um número LUN maior que zero (por exemplo: 1, 2, etc.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 Para obter o nome do dispositivo, execute o comando *fdisk –l*. Execute-o em ambos os nós. Escolha o dispositivo com tamanho **178 MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Inicializar o dispositivo SBD

2.1 Inicializar o dispositivo SBD em **ambos** os nós

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Verificar o que foi gravado no dispositivo. Fazer isso em **ambos** nós

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Configurar o Cluster
Esta seção descreve as etapas para configurar o cluster de HA SUSE.
### <a name="31-package-installation"></a>3.1 Instalação do pacote
3.1.1 Verificar se os padrões ha_sles e SAPHanaSR-doc estão instalados. Se não estiver instalado, instale-os. Instale-o em **ambos** os nós.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Configurar o cluster
3.2.1 Use o comando *ha-cluster-init* ou o assistente yast2 para configurar o cluster. Neste caso, é usado o assistente yast2. Execute esta etapa **somente no nó Primário**.

Siga yast2> Alta Disponibilidade > Cluster ![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Clique em **cancelar**, pois o pacote halk2 já está instalado.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Clique em **Continuar**

Valor esperado = número de nós implantados (neste caso, 2) ![yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) Clique em **Avançar**
![yast-cluster-configure-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Adicione os nomes do nó e clique em “Adicionar arquivos sugeridos”

Clique em “Ligar csync2”

Clique em “Gerar chaves pré-compartilhadas”. Elas aparecem abaixo do pop-up

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Clique em **OK**

A autenticação é realizada usando os endereços IP e as chaves pré-compartilhadas em Csync2. O arquivo de chave é gerado com csync2 -k /etc/csync2/key_hagroup. O arquivo key_hagroup deve ser copiado para todos os membros do cluster manualmente depois de criado. **Não deixe de copiar o arquivo do node1 para o node2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Clique em **Avançar**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Na opção padrão, a Inicialização estava desativada. Altere-a para “ativada” para que o Pacemaker seja iniciado junto com a inicialização. Você pode fazer a escolha de acordo com os requisitos de instalação.
Clique em **Avançar** para concluir a configuração do cluster.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Configurar o watchdog Softdog
Esta seção descreve a configuração do watchdog (softdog).

4.1 Adicionar a seguinte linha a */etc/init.d/boot.local* em **ambos** os nós.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Atualize o arquivo */etc/sysconfig/sbd* em **ambos** os nós, como mostrado abaixo:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Carregar o módulo de kernel em **ambos** os nós, executando o comando a seguir
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Verifique se o softdog está em execução como mostrado abaixo em **ambos** os nós:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Iniciar o dispositivo SBD em **ambos** os nós
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Testar o daemon SBD em **ambos** os nós. Você verá duas entradas depois de configurá-lo em **ambos** os nós
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Enviar uma mensagem de teste para **um** de seus nós
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 No **segundo** nó (node2), verifique o status da mensagem
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Para adotar a configuração sbd, atualize o arquivo */etc/sysconfig/sbd* como a seguir. Atualize o arquivo em **ambos** os nós
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Iniciar o serviço Pacemaker no **nó Primário** (node1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Se o serviço Pacemaker *falhar*, consulte o *Cenário 5: falha do serviço Pacemaker*

## <a name="5---joining-the-cluster"></a>5.   Associação ao cluster
Esta seção descreve como adicionar o nó ao cluster.

### <a name="51-add-the-node"></a>5.1 Adicionar o nó
Execute o comando a seguir no **node2** para permitir que o node2 seja associado ao cluster.
```
ha-cluster-join
```
Se você receber um *erro* durante a associação do cluster, consulte *Cenário 6: não é possível associar o Node2 ao cluster*.

## <a name="6---validating-the-cluster"></a>6.   Validando o cluster

### <a name="61-start-the-cluster-service"></a>6.1 Iniciar o serviço de cluster
Para verificar e, opcionalmente, iniciar o cluster pela primeira vez em **ambos** os nós.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 Monitorar o status
Execute o comando *crm_mon* para fazer com que **ambos** os nós fiquem online. Você pode executá-lo em **qualquer nó** do cluster
```
crm_mon
```
![CRM-MON](media/HowToHLI/HASetupWithStonith/crm-mon.png) você também pode fazer logon HAWK para verificar o status do cluster *https://\<nó IP >: 7630*. O usuário padrão é hacluster e a senha é linux. Se necessário, você pode alterar a senha usando o comando *passwd*.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Configurar Propriedades do Cluster e Recursos 
Esta seção descreve as etapas para configurar os recursos do cluster.
Neste exemplo, configure o recurso a seguir. O restante poderá ser configurado (se necessário), consultando o guia HA do SUSE. Execute esta configuração somente em **um dos nós**. Faça isso no nó primário.

- Inicialização do cluster
- Dispositivo STONITH
- O Endereço IP Virtual


### <a name="71-cluster-bootstrap-and-more"></a>7.1 Inicialização do cluster e outros
Adicionar a inicialização do cluster. Crie o arquivo e adicione o texto como mostrado a seguir:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
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
```
Adicionar a configuração ao cluster.
```
crm configure load update crm-bs.txt
```
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 Dispositivo STONITH
Adicionar o recurso STONITH. Crie o arquivo e adicione o texto como mostrado a seguir.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Adicionar a configuração ao cluster.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 O endereço IP virtual
Adicionar o IP virtual do recurso. Criar o arquivo e adicionar o texto como mostrado abaixo.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Adicionar a configuração ao cluster.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 Validar os recursos

Quando você executa o comando *crm_mon*, pode ver os dois recursos.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Além disso, você pode ver o status em *https://\<endereço IP do nó >: 7630/cib/live/estado*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testar o processo de failover
Para testar o processo de failover, pare o serviço Pacemaker no node1 e o failover dos recursos para o node2.
```
Service pacemaker stop
```
Agora, interrompa o serviço Pacemaker no **node2** e os recursos com failover para o **node1**

**Antes do failover**
![Before-failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**Após o failover**
![after-failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
 ![ crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Solução de problemas
Esta seção descreve alguns cenários de falha que podem ocorrer durante a instalação. Você pode não enfrentar esses problemas necessariamente.

### <a name="scenario-1-cluster-node-not-online"></a>Cenário 1: o nó do cluster não está online
Se algum dos nós não aparecer online no gerenciador de cluster, você pode experimentar as opções a seguir para colocá-lo online.

Iniciar o serviço iSCSI
```
service iscsid start
```

E agora você deve conseguir fazer logon no nó iSCSI
```
iscsiadm -m node -l
```
A saída esperada se parece com o seguinte
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Cenário 2: o yast2 não mostra a exibição gráfica
A tela gráfica do yast2 é usada para configurar o cluster de alta disponibilidade neste documento. Se o yast2 não abrir com a janela gráfica como mostrado e gerar um erro Qt, execute as etapas abaixo. Se ele abre com a janela gráfica, você pode ignorar estas etapas.

**Erro**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Saída Esperada**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Se o yast2 não abrir no modo de exibição gráfico, execute as etapas a seguir.

Instale os pacotes necessários. Você deve estar conectado como usuário “raiz” e ter o SMT configurado para baixar/instalar os pacotes.

Para instalar os pacotes, use yast>Software>Gerenciamento de Software>Dependências> opção “Instalar pacotes recomendados...”. A captura de tela a seguir mostra as telas esperadas.
>[!NOTE]
>É necessário executar as etapas em ambos os nós para poder acessar o modo de exibição gráfico do yast2 em ambos os nós.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Em Dependências, selecione "Instalar Pacotes Recomendados" ![yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Revise as alterações e clique em OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

A instalação do pacote continua ![yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Clique em Avançar

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Clique em Concluir

Você também precisa instalar os pacotes libqt4 e libyui-qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) O yast2 deve conseguir abrir o modo de exibição gráfico agora, conforme mostrado aqui.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Cenário 3: o yast2 não mostra a opção de Alta Disponibilidade
Para a opção de Alta Disponibilidade ficar visível no centro de controle do yast2, você precisará instalar os pacotes adicionais.

Usando Yast2>Software>Gerenciamento de software, selecione os padrões a seguir

- Base do servidor do SAP HANA
- Compilador e ferramentas C/C++
- Alta disponibilidade
- Base do servidor de aplicativos SAP

A tela a seguir mostra as etapas para instalar os padrões.

Usando yast2 > Software > Gerenciamento de Software

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Selecione os padrões

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Clique em **Aceitar**

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Clique em **Continuar**

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Clique em **Avançar** após concluir a instalação

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Cenário 4: a instalação do HANA falha com erro de assemblies gcc
A instalação do HANA falha com o erro a seguir.

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Para corrigir o problema, você precisa instalar bibliotecas (libgcc_sl e libstdc++6) como as mostradas a seguir.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Cenário 5: falha do serviço Pacemaker

Ocorreu o problema a seguir durante a inicialização do serviço Pacemaker.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Para corrigi-lo, exclua a linha a seguir do arquivo */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Cenário 6: não é possível associar o Node2 ao cluster

Ao associar o node2 ao cluster existente usando o comando *ha-cluster-join*, ocorreu o erro a seguir.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Para corrigir, execute as opções a seguir em ambos os nós

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Após a correção anterior, o node2 deve ser adicionado ao cluster

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Documentação Geral
Você pode encontrar mais informações sobre a configuração de HA do SUSE nos seguintes artigos: 

- [Cenário otimizado para desempenho da SR SAP HANA](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Isolamento baseado em armazenamento](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog - Usando o Pacemaker Cluster para SAP HANA - Parte 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog - Usando o Pacemaker Cluster para SAP HANA - Parte 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
