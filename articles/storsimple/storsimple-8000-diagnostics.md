---
title: Ferramenta de Diagnóstico para solucionar problemas de dispositivo do StorSimple 8000 | Microsoft Docs
description: Descreve os modos de dispositivo StorSimple e explica como usar o Windows PowerShell para StorSimple para alterar o modo de dispositivo.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5cce4337e3ef95c6407d46d9b8b6401fe4f6600b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576179"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Use a Ferramenta de Diagnóstico do StorSimple para solucionar problemas de dispositivo da série 8000

## <a name="overview"></a>Visão geral

A Ferramenta de Diagnóstico de StorSimple diagnostica problemas relacionados à integridade de componentes de hardware, desempenho, rede e hardware para um dispositivo StorSimple. A ferramenta de diagnóstico pode ser usada em vários cenários. Esses cenários incluem o planejamento da carga de trabalho, implantação de um dispositivo StorSimple, avaliar o ambiente de rede e determinar o desempenho de um dispositivo operacional. Este artigo fornece uma visão geral da ferramenta de diagnóstico e descreve como a ferramenta pode ser usada com um dispositivo StorSimple.

A ferramenta de diagnóstico destina-se principalmente a dispositivos locais da série StorSimple 8000 (8100 e 8600).

## <a name="run-diagnostics-tool"></a>Execute a ferramenta de diagnóstico

Essa ferramenta pode ser executada através da interface do Windows PowerShell do seu dispositivo StorSimple. Há duas maneiras de acessar a interface local do dispositivo:

* [Use o PuTTY para conectar-se ao console serial do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Acesse remotamente a ferramenta por meio do Windows PowerShell para StorSimple](storsimple-8000-remote-connect.md).

Neste artigo, estamos supondo que você se conectou ao console serial do dispositivo via PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Para executar a ferramenta de diagnóstico

Após conectar-se à interface do Windows PowerShell do dispositivo, execute as seguintes etapas para executar o cmdlet.
1. Faça logon no console serial do dispositivo seguindo as etapas em [Usar o PuTTY para conectar-se ao console serial do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Digite o seguinte comando: 

    `Invoke-HcsDiagnostics`

    Se o parâmetro de escopo não for especificado, o cmdlet executa todos os testes de diagnóstico. Esses testes incluem a integridade de componentes de hardware, sistema, rede e desempenho. 
    
    Para executar somente um teste específico, especifique o parâmetro de escopo. Por exemplo, para executar somente o teste de rede, digite

    `Invoke-HcsDiagnostics -Scope Network`

3. Selecione e copie a saída da janela PuTTY em um arquivo de texto para análise posterior.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Cenários para usar a ferramenta de diagnóstico

Use a ferramenta de diagnóstico para solucionar problemas de integridade de rede, desempenho, sistema e hardware do sistema. Aqui estão alguns cenários possíveis:

* **Dispositivo offline** - Seu dispositivo da série StorSimple 8000 dispositivo está offline. No entanto, na interface do Windows PowerShell, parece que ambos os controladores estão em execução.
    * Você pode usar essa ferramenta para determinar o estado da rede.
         
         > [!NOTE]
         > Não use essa ferramenta para avaliar as configurações de rede e desempenho em um dispositivo antes do registro (ou configurar por meio do assistente de instalação). Um IP válido é atribuído ao dispositivo durante o registro e o assistente de instalação. Você pode executar este cmdlet em um dispositivo que não está registrado para verificar a integridade do hardware e do sistema. Use o parâmetro de escopo, por exemplo:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problemas de dispositivo persistentes** -Você está enfrentando problemas de dispositivo que parecem persistir. Por exemplo, ocorre um erro ao fazer o registro. Você pode também pode estar experimentando problemas com o dispositivo após o dispositivo ter sido registrado com sucesso e estar operacional.
    * Nesse caso, use essa ferramenta para uma solução de problemas preliminar antes de fazer uma solicitação de serviço o Suporte da Microsoft. É recomendável que você execute essa ferramenta e capture a saída dessa ferramenta. Em seguida, você pode fornecer essa saída para o Suporte para acelerar a solução do problema.
    * Se houver qualquer falha de um componente ou cluster de hardware, você deve fazer uma solicitação de suporte.

* **Baixo desempenho do dispositivo** - O dispositivo StorSimple está muito lento.
    * Nesse caso, execute este cmdlet com o parâmetro de escopo definido para o desempenho. Analise a saída. Você obtém as latências de leitura/gravação da nuvem. Use as latências relatadas como o valor máximo que pode ser obtido, leve em consideração alguma sobrecarga de processamento de dados interno e, em seguida, faça a implantação das cargas de trabalho no sistema. Para obter mais informações, vá para [Usar o teste de rede para solucionar problemas de desempenho do dispositivo](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Saídas de exemplo e teste de diagnóstico

### <a name="hardware-test"></a>Teste de hardware

Esse teste determina o status de componentes de hardware, o firmware USM e o firmware do disco em execução no seu sistema.

* Os componentes de hardware relatados são os componentes que falharam no teste ou que não estão presentes no sistema.
* As versões de firmware de disco e firmware USM são relatadas para o Controlador 0, Controlador 1 e para os componentes compartilhados do sistema. Para obter uma lista completa dos componentes de hardware, vá para:

    * [Componentes no compartimento principal](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Componentes no compartimento EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Se o teste de hardware relata componentes com falha, [faça uma solicitação de serviço com o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Exemplo de saída de uma execução de teste de hardware em um dispositivo 8100

Aqui está um exemplo de saída de um dispositivo StorSimple 8100. O compartimento EBOD não está presente no dispositivo do modelo 8100. Portanto, os componentes do controlador EBOD não são relatados.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Teste do sistema

Esse teste relata as informações do sistema, as atualizações disponíveis, as informações de cluster e as informações de serviço para seu dispositivo.

* As informações do sistema incluem o modelo, número de série do dispositivo, fuso horário, status do controlador e a versão do software detalhada em execução no sistema. Para entender os diversos parâmetros de sistema relatados como saída, vá para [Interpretando as informações do sistema](#appendix-interpreting-system-information).

* A disponibilidade de atualização relata se os modos normais e de manutenção estão disponíveis e seus nomes de pacote associados. Se `RegularUpdates` e `MaintenanceModeUpdates` são `false`, isso indica que as atualizações não estão disponíveis. Seu dispositivo está atualizado.
* As informações de cluster contém as informações sobre os vários componentes lógicos de todos os grupos de cluster HCS e seus respectivos status. Se você vir um grupo de cluster offline nesta seção do relatório, [entre em contato com o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).
* As informações de serviço incluem os nomes e o status de todos os serviços HCS e CiS em execução no seu dispositivo. Essas informações são úteis para o Suporte da Microsoft solucionar o problema do dispositivo.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Exemplo de saída de uma execução de teste do sistema em um dispositivo 8100

Aqui está um exemplo de saída de uma execução de teste do sistema em um dispositivo 8100.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Teste de rede

Esse teste valida o status das interfaces de rede, portas, DNS e conectividade de servidor NTP, o certificado SSL, as credenciais da conta de armazenamento, a conectividade com os servidores de atualização e a conectividade do proxy da Web no seu dispositivo StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Exemplo de saída do teste de rede quando somente DATA0 está habilitado

Aqui está um exemplo de saída do dispositivo 8100. Você pode ver na saída que:
* Somente as interfaces de rede DATA 0 e DATA 1 estão ativadas e configuradas.
* As interfaces DATA 2 - 5 não estão habilitadas no portal.
* A configuração do servidor DNS é válida e o dispositivo pode conectar-se por meio do servidor DNS.
* A conectividade do servidor NTP também está boa.
* As portas 80 e 443 estão abertas. No entanto, a porta 9354 está bloqueada. Conforme os [requisitos de rede do sistema](storsimple-system-requirements.md), você precisa abrir essa porta para a comunicação do barramento de serviço.
* A certificação SSL é válida.
* O dispositivo pode conectar-se à conta de armazenamento: _myss8000storageacct_.
* A conectividade com os servidores de atualização é válida.
* O proxy da Web não está configurado neste dispositivo.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Exemplo de saída do teste de rede quando somente DATA0 e DATA1 estão habilitados

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Teste de desempenho

Esse teste relata o desempenho de nuvem por meio das latências de leitura/gravação de nuvem para o seu dispositivo. Essa ferramenta pode ser usada para estabelecer uma linha de base do desempenho de nuvem que você pode obter com o StorSimple. A ferramenta relata o desempenho máximo (melhor cenário para latências de leitura/gravação) que você pode obter para a sua conexão.

Como a ferramenta relata o desempenho máximo que pode ser obtido, podemos usar as latências de leitura/gravação relatadas como referência ao implantar as cargas de trabalho.

O teste simula os tamanhos de blob associados aos diferentes tipos de volume no dispositivo. Volumes normais em camadas e backups de volumes localmente fixados usam um tamanho de blob de 64 KB. Volumes em camadas com opção de arquivamento marcada usam um tamanho de dados de blob de 512 KB. Se o dispositivo tiver volumes em camadas e fixados localmente configurados, somente o teste correspondente ao tamanho de dados de blob de 64 KB é executado.

Para usar essa ferramenta, siga as seguintes etapas:

1.  Primeiro, crie uma mistura de volumes em camadas e em camadas com a opção de arquivamento marcada. Isso garante que a ferramenta executa os testes de tamanho de blob de 64 KB e 512 KB.

2. Execute o cmdlet, depois de criar e configurar os volumes. Digite:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Anote as latências de leitura/gravação relatadas pela ferramenta. Esse teste pode levar vários minutos para ser executado antes de relatar os resultados.

4. Se todas as latências de conexão estiverem abaixo do intervalo esperado as latências relatadas pela ferramenta podem ser usadas como o valor máximo de referência ao implantar as cargas de trabalho. Considere uma sobrecarga para o processamento de dados interno.

    Se as latências de leitura/gravação relatadas pela ferramenta de diagnóstico forem altas:

    1. Configure a Análise de Armazenamento para serviços de blob e analise a saída para entender as latências da conta de armazenamento do Azure. Para obter instruções detalhadas, vá para [habilitar e configurar a Análise de Armazenamento](../storage/common/storage-enable-and-view-metrics.md). Se as latências também forem altas e comparáveis aos números que você recebeu da ferramenta de diagnóstico do StorSimple você precisará fazer uma solicitação de serviço com o armazenamento do Azure.

    2. Se as latências da conta de armazenamento forem baixas, entre em contato com o administrador de rede para investigar problemas de latência na sua rede.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Exemplo de saída de uma execução de teste de desempenho em um dispositivo 8100

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Apêndice: interpretação das informações do sistema

Aqui está uma tabela que descreve o mapeamento dos vários parâmetros do Windows PowerShell das informações do sistema. 

| Parâmetro do PowerShell    | DESCRIÇÃO  |
|-------------------------|------------------|
| ID da instância             | Cada controlador tem um identificador exclusivo ou um GUID associado a ele.|
| NOME                    | O nome amigável do dispositivo conforme configurado por meio do portal do Azure durante a implantação do dispositivo. O nome amigável padrão é o número de série do dispositivo. |
| Modelo                   | O modelo do seu dispositivo StorSimple série 8000. O modelo pode ser 8100 ou 8600.|
| SerialNumber            | O número de série do dispositivo vem configurado de fábrica e possui 15 caracteres. Por exemplo, 8600-SHX0991003G44HT indica:<br> 8600 – É o modelo do dispositivo.<br>SHX – É o local de produção.<br> 0991003 - É um produto específico. <br> G44HT- os últimos cinco dígitos são incrementados para criar números de série exclusivos. Pode ser um conjunto não sequencial de números.|
| timeZone                | O fuso horário do dispositivo conforme configurado no portal do Azure durante a implantação do dispositivo.|
| CurrentController       | O controlador ao qual você está conectado por meio da interface do Windows PowerShell do seu dispositivo StorSimple.|
| ActiveController        | O controlador que está ativo no seu dispositivo e que está controlando todas as operações de rede e disco. Pode ser o Controlador 0 ou Controlador 1.  |
| Controller0Status       | O status do Controlador 0 no seu dispositivo. O status do controlador pode ser normal, modo de recuperação ou inacessível.|
| Controller1Status       | O status do Controlador 1 no seu dispositivo.  O status do controlador pode ser normal, modo de recuperação ou inacessível.|
| SystemMode              | O status geral do seu dispositivo StorSimple. O status do dispositivo pode ser normal, em manutenção ou encerrado (corresponde a desativado no portal do Azure).|
| FriendlySoftwareVersion | A cadeia de caracteres amigável que corresponde à versão do software do dispositivo. Para um sistema executando a Atualização 4, a versão do software amigável seria StorSimple Série 8000 Atualização 4.0.|
| HcsSoftwareVersion      | A versão do software HCS em execução no seu dispositivo. Por exemplo, a versão de software HCS correspondente ao StorSimple Série 8000 Atualização 4.0 é 6.3.9600.17820. |
| ApiVersion              | A versão de software da API do Windows PowerShell do dispositivo HCS.|
| VhdVersion              | A versão do software da imagem de fábrica que acompanha o dispositivo. Se você redefinir seu dispositivo para as configurações de fábrica, ele executa essa versão do software.|
| OSVersion               | A versão do software do sistema operacional Windows Server em execução no dispositivo. O dispositivo StorSimple é baseado no Windows Server 2012 R2 que corresponde à versão 6.3.9600.|
| CisAgentVersion         | A versão do seu agente de Cis em execução no seu dispositivo StorSimple. Esse agente ajuda a se comunicar com o serviço StorSimple Manager em execução no Azure.|
| MdsAgentVersion         | A versão correspondente ao agente Mds em execução no seu dispositivo StorSimple. Esse agente move os dados para o Monitoring and Diagnostics Server (MDS).|
| Lsisas2Version          | A versão correspondente aos drivers LSI no seu dispositivo StorSimple.|
| Capacity                | A capacidade total do dispositivo em bytes.|
| RemoteManagementMode    | Indica se o dispositivo pode ser gerenciado remotamente por meio da sua interface do Windows PowerShell. |
| FipsMode                | Indica se o modo Federal Information Processing Standard (FIPS) dos Estados Unidos está habilitado no seu dispositivo. O padrão FIPS 140 define algoritmos criptográficos aprovados para uso por sistemas de computador do governo federal dos EUA para a proteção de dados confidenciais. Para dispositivos executando a Atualização 4 ou posterior, o modo FIPS está habilitado por padrão. |

## <a name="next-steps"></a>Próximas etapas

* Aprenda a [sintaxe do cmdlet Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Saiba mais sobre como [solucionar problemas de implantação](storsimple-troubleshoot-deployment.md) no seu dispositivo StorSimple.
