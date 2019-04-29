---
title: Instalar o dispositivo Microsoft Azure StorSimple 8100 | Microsoft Docs
description: Descreve como desembalar, montar em rack e cabear do dispositivo do StorSimple 8100 antes de implantar e configurar o software.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: b367b6e7126a442dc68646ff52a29c955f50b798
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631100"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Desembalar, montar em rack e cabear o dispositivo StorSimple 8100.
## <a name="overview"></a>Visão geral
Seu Microsoft Azure StorSimple 8100 é um dispositivo único de compartimento montado em rack. Este tutorial explica como desempacotar, montar em rack e cabear o hardware do dispositivo StorSimple 8100 para que seja possível configurar e implantar o dispositivo StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Desempacotar o dispositivo StorSimple 8100
As etapas a seguir fornecem instruções claras e detalhadas sobre como desempacotar seu dispositivo de armazenamento StorSimple 8100. Este dispositivo é fornecido em uma única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para desempacotar o dispositivo
Antes de desempacotar o dispositivo, leia as informações a seguir.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesado](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **AVISO!**

1. Se for manejar o dispositivo manualmente, assegure-se de que terá a ajuda de duas pessoas que possam aguentar o peso do compartimento. Um compartimento totalmente configurado pode pesar até 32 kg (70 lbs.).
2. Coloque a caixa em uma superfície plana e nivelada.

Conclua as etapas a seguir para desempacotar o dispositivo.

#### <a name="to-unpack-your-device"></a>Para desempacotar o dispositivo
1. Inspecione a caixa e a espuma da embalagem para verificar se não há amassados, cortes, danos por água ou qualquer outro dano visível. Se a caixa ou a embalagem estiver muito danificada, não a abra. [Contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para receber ajuda e saber se o dispositivo está em boas condições de funcionamento.
2. Abra a caixa. A imagem a seguir mostra a exibição desempacotada do dispositivo StorSimple.
   
     ![Desempacotar o dispositivo de armazenamento](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Exibição do dispositivo de armazenamento desempacotado**
   
   | Rótulo | DESCRIÇÃO |
   | --- | --- |
   |   1 |Embalagem |
   |   2 |Espuma inferior |
   |   3 |Dispositivo |
   |   4 |Espuma superior |
   |   5 |Caixa de acessório |
3. Depois de abrir a caixa, verifique se você tem estes itens:
   
   * 1 único dispositivo de compartimento
   * 2 cabos de energia
   * 1 cabo cruzado Ethernet
   * 2 cabos seriais de console
   * 1 conversor serial USB para acesso serial
   * 1 chave de fenda T10 à prova de violação
   * 4 adaptadores QSFP para SFP+ para serem usados com interfaces de rede de 10 GbE
   * 1 kit de montagem em rack (2 trilhos laterais com hardware de montagem)
   * Documentação de Introdução
     
     Se você não tiver recebido algum dos itens listados acima, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

A próxima etapa é montar o dispositivo em rack.

## <a name="rack-mount-your-storsimple-8100-device"></a>Montar em rack o dispositivo StorSimple 8100
Siga as próximas etapas para instalar o dispositivo de armazenamento StorSimple 8100 em um rack padrão de 19 polegadas com colunas frontal e traseira. O dispositivo StorSimple 8100 tem um único compartimento principal.

A instalação consiste em várias etapas, e cada uma delas é discutida nos procedimentos a seguir.

> [!IMPORTANT]
> Os dispositivos StorSimple devem ser montados em rack para a operação apropriada.
> 
> 

### <a name="prepare-the-site"></a>Preparar o local
O dispositivo deve ser instalado em um rack padrão de 19 polegadas (48 cm) com colunas frontal e traseira. Use o procedimento a seguir para preparar a instalação do rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o local para instalação do rack
1. Certifique-se de colocar o dispositivo em uma superfície de trabalho plana, estável e nivelada (ou em local semelhante) de modo que ele fique seguro.
2. Verifique se o local onde você pretende instalar tem energia de corrente alternada padrão de uma fonte independente ou uma PDU (unidade de distribuição de energia) em rack com um no-break.
3. Verifique se um slot de 2U está disponível no rack em que você pretende montar o dispositivo.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone de peso pesado](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **AVISO!**

Se for instalar o dispositivo manualmente, assegure-se de que terá a ajuda de duas pessoas que possam aguentar o peso do dispositivo. Um compartimento totalmente configurado pode pesar até 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Pré-requisitos do rack
O compartimento 8100 foi projetado para instalação em um gabinete padrão de 19 polegadas com:

* Profundidade mínima de 27,84 polegadas de uma coluna a outra do rack.
* Peso máximo de 32 kg para o dispositivo
* Pressão de retorno máxima de 5 Pascal (medidor de água de 0,5 mm).

### <a name="rack-mounting-rail-kit"></a>Kit do trilho de montagem em rack
Um conjunto de trilhos de montagem é fornecido para ser usado com o gabinete de rack de 19 polegadas. Os trilhos foram testados para suportar o peso máximo do compartimento. Esses trilhos também permitirão a instalação de vários compartimentos sem nenhuma perda de espaço dentro do rack.

#### <a name="to-install-the-device-on-the-rails"></a>Para instalar o dispositivo nos trilhos
1. Execute esta etapa somente se não houver trilhos internos instalados em seu dispositivo. Normalmente, os trilhos internos são instalados na fábrica. Caso contrário, instale as corrediças do trilho esquerdo e direito nas laterais dos chassis do compartimento. Elas são presas por seis parafusos métricos em cada lado. Para ajudar com a orientação, as corrediças do trilho são marcadas com **LH – Frontal** e **RH – Frontal**, e a extremidade que é afixada na parte traseira do compartimento é afunilada.<br/>
   
    ![Fixando as corrediças do trilho nos chassis do compartimento](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Fixando corrediças do trilho interno às laterais do compartimento**
   
    Rótulo | DESCRIÇÃO
    ----- | -----------
    1     | Parafusos de cabeça abaulada M 3x4
    2     | Corrediças dos chassis

2. Fixe os módulos dos trilhos direito e esquerdo externos nos membros verticais do gabinete do rack. Os suportes são marcados com **LH**, **RH** e **Este lado para cima** para que você obtenha a orientação correta.
3. Localize os pinos do trilho na parte frontal e traseira do módulo do trilho. Estenda o trilho para que ele se encaixe entre as colunas do rack e insira os pinos nos orifícios do membro vertical da coluna do rack frontal e traseiro. Certifique-se de que o módulo do trilho esteja nivelado.
4. Use dois dos parafusos métricos fornecidos para prender o módulo do trilho nos membros verticais do rack. Use um parafuso na parte frontal e outro na parte traseira.
5. Repita estas etapas para o outro módulo do trilho.<br/>
   
     ![Fixando as corrediças do trilho no gabinete do rack](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Fixando os conjuntos de trilhos internos ao rack**
   
   | Rótulo | DESCRIÇÃO |
   | --- | --- |
   |   1 |Parafuso de travamento |
   |   2 |Parafuso da coluna do rack frontal para orifício quadrado |
   |   3 |Pinos de localização do trilho frontal esquerdo |
   |   4 |Parafuso de travamento |
   |   5 |Pinos de localização do trilho traseiro esquerdo |

### <a name="mounting-the-device-in-the-rack"></a>Montando o dispositivo no rack
Usando os trilhos do rack que acabaram de ser instalados, execute as etapas a seguir para montar o dispositivo no rack.

#### <a name="to-mount-the-device"></a>Para montar o dispositivo
1. Com um assistente, levante o compartimento e alinhe-o com os trilhos do rack.
2. Com cuidado, insira o dispositivo nos trilhos e empurre-o completamente para dentro do gabinete do rack.<br/>
   
    ![Inserindo o dispositivo no rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montando o dispositivo no rack**
3. Remova as tampas do flange frontal direito e esquerdo, soltando-as. As tampas do flange estão apenas encaixadas nos flanges.
4. Fixe o compartimento no rack usando um parafuso Phillips fornecido em cada flange, esquerdo e direito.
5. Instale as tampas do flange pressionando-as na posição e encaixando-as no lugar.<br/>
   
     ![Instalando as tampas do flange](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalando as tampas do flange**
   
   | Rótulo | DESCRIÇÃO |
   | --- | --- |
   |   1 |Parafuso de fixação do compartimento |

A próxima etapa é fazer o cabeamento do dispositivo para recebimento de energia, acesso à rede e acesso serial.

## <a name="cable-your-storsimple-8100-device"></a>Cabear o dispositivo StorSimple 8100
Os procedimentos a seguir explicam como cabear o dispositivo StorSimple 8100 para conexões de energia, rede e serial.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar o cabeamento do dispositivo, você precisará dos seguintes itens:

* Seu dispositivo de armazenamento totalmente desempacotado e montado no rack.
* 2 cabos de energia que acompanham o dispositivo
* Acesso a 2 unidades de distribuição de energia (recomendado).
* Cabos de rede
* Cabos seriais fornecidos
* Conversor USB serial com o driver apropriado instalado em seu computador (se necessário)
* Quatro adaptadores QSFP para SFP+ para serem usados com interfaces de rede de 10 GbE
* [Suporte para hardware de interfaces de rede de 10 GbE em seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Cabeamento de energia
Seu dispositivo inclui PCMs (Módulos de Energia e Refrigeração) redundantes. Ambos os PCMs devem ser instalados e conectados a diferentes fontes de energia para garantia de alta disponibilidade.

Execute as etapas a seguir para cabear o dispositivo para recebimento de energia.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Cabeamento de rede
O dispositivo é uma configuração ativa/em espera: em qualquer determinado momento, um módulo do controlador está ativo e processando todas as operações de disco e rede, enquanto o outro módulo do controlador está em espera. Se o controlador falhar, o controlador em espera será ativado imediatamente e dará continuidade a todas as operações de disco e rede.

Para oferecer suporte a este failover de controlador redundante, você precisa cabear a rede do dispositivo, conforme descrito nas etapas a seguir.

#### <a name="to-cable-for-network-connection"></a>Para cabear a conexão de rede
1. Seu dispositivo tem seis interfaces de rede em cada controlador: quatro de 1 Gbps e duas portas Ethernet de 10 Gbps. Identifique as várias portas de dados no backplane do dispositivo.
   
    ![Backplane do dispositivo 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Parte traseira do dispositivo mostrando as portas de dados**
   
   | Rótulo | DESCRIÇÃO |
   | --- | --- |
   |   0,1,4,5 |Interfaces de rede de 1 GbE |
   |   2,3 |Interfaces de rede de 10 GbE |
   |   6 |Portas seriais |
2. Consulte o diagrama a seguir para cabeamento de rede. (A configuração mínima de rede é mostrada pelas linhas azuis sólidas. Uma configuração adicional será necessária para alta disponibilidade e desempenho e é mostrada pelas linhas pontilhadas).

    ![Cabear o dispositivo 2U para rede](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cabeamento de rede para o dispositivo**

   |Rótulo | DESCRIÇÃO |
   |----- | ----------- |
   | O     | LAN com acesso à Internet |
   | B    | Controlador 0 |
   | C    | PCM 0 |
   | D    | Controlador 1 |
   | E    | PCM 1 |
   | F, G | Hosts |
   | 0-5  | Interfaces de rede |



Ao cabear o dispositivo, a configuração mínima exige:

* Pelo menos duas interfaces de rede conectadas em cada controlador com uma para acesso à nuvem e outra para iSCSI. A porta DATA 0 é habilitada e configurada automaticamente por meio do console serial do dispositivo. Além da DATA 0, outra porta de dados também precisa ser configurada por meio do Portal clássico do Azure. Nesse caso, conecte a porta DATA 0 à LAN primária (rede com acesso à Internet). As outras portas de dados podem ser conectadas ao segmento SAN/iSCSI LAN (VLAN) da rede, dependendo da função pretendida.
* Interfaces idênticas em cada controlador conectadas à mesma rede para garantir a disponibilidade caso ocorra um failover do controlador. Por exemplo, se você optar por conectar DATA 0 e DATA 3 em um dos controladores, precisará conectar DATA 0 e DATA 3 correspondentes no outro controlador.

Lembre-se disso no caso de alta disponibilidade e desempenho:

* Quando for possível, configure um par de interfaces de rede para acesso à nuvem (1 GbE) e outro par para iSCSI (10 GbE recomendados) em cada controlador.
* Quando for possível, conecte as interfaces de rede de cada controlador a pelo menos dois comutadores diferentes para garantir disponibilidade no caso de falha do comutador. A figura ilustra duas interfaces de rede de 10 GbE, DATA 2 e DATA 3, de cada controlador conectado a dois comutadores diferentes.

Para saber mais, consulte **Interfaces de rede** em [Requisitos de alta disponibilidade para seu dispositivo StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Se você estiver usando transceptores SFP+ com suas interfaces de rede 10 GbE, use os adaptadores QSFP-SFP+ fornecidos. Para obter mais informações, acesse [Suporte para hardware de interfaces de rede de 10 GbE em seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Cabeamento de porta serial
Execute as etapas a seguir para cabear a porta serial.

#### <a name="to-cable-for-serial-connection"></a>Para cabear a conexão serial
1. O dispositivo tem uma porta serial em cada controlador que é identificada por um ícone de chave inglesa. Consulte a ilustração na seção [Cabeamento de rede](#network-cabling) para localizar as portas seriais no backplane do dispositivo.
2. Identifique o controlador ativo no backplane do dispositivo. Um LED azul piscante indica que o controlador está ativo.
3. Use os cabos seriais fornecidos (se necessário, o conversor serial USB para seu laptop) e conecte o console ou o computador (com emulação de terminal para o dispositivo) com a porta serial do controlador ativo.
4. Instale os drivers de USB seriais (fornecidos com o dispositivo) em seu computador.
5. Configure a conexão serial como se segue: 115.200 bauds, 8 bits de dados, 1 bit de parada, sem paridade e fluxo de controlam definida como nenhum.
6. Verifique se a conexão está funcionando pressionando Enter no console. Um menu de console serial deve aparecer.

> [!NOTE]
> **Gerenciamento noturno**: Quando o dispositivo é instalado em um data center remoto ou em uma sala de computadores com acesso limitado, certifique-se de que as conexões seriais com ambos os controladores sempre são conectadas a um comutador de console serial ou equipamento similar. Isso permite operações de suporte e controle remoto fora de banda se houver interrupções na rede ou falhas inesperadas.
> 
> 

Agora o dispositivo está cabeado para energia, acesso à rede e conectividade serial. A próxima etapa será configurar o software e implantar o dispositivo.

## <a name="next-steps"></a>Próximas etapas
Saiba como [implantar e configurar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).

