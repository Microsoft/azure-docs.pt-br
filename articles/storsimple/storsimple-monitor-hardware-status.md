---
title: Componentes de hardware e status do StorSimple | Microsoft Docs
description: "Saiba como monitorar os componentes de hardware do dispositivo StorSimple por meio do serviço StorSimple Manager."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 0d56a2ba-daf0-45ad-9610-8b8712dd5750
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: alkohli
ms.openlocfilehash: 93df79c9d349c294b692148a19c9d881c4de4f7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-monitor-hardware-components-and-status"></a>Usar o serviço StorSimple Manager para monitorar componentes e status de hardware
## <a name="overview"></a>Visão geral
Este artigo descreve os vários componentes físicos e lógicos do dispositivo StorSimple no local. Ele também explica como monitorar o status de componente de dispositivo usando a página **Manutenção** no serviço StorSimple Manager. 

A página **Manutenção** mostra o status do hardware de todos os componentes do dispositivo StorSimple. 

Na lista de componentes para 8100, há três seções que descrevem:

* **Componentes compartilhados** –Nnão fazem parte dos controladores, como unidades de disco, compartimentos, componentes de PCM e temperatura de PCM, tensão de linha e sensores de corrente de linha.
* **Componentes do controlador 0** – Os componentes que residem no Controlador 0, como controlador, expansor SAS e conector, sensores de temperatura do controlador e as várias interfaces de rede.
* **Componentes do controlador 1** – Os componentes que constituem o Controlador 1, similar aos detalhados para o Controlador 0.

Um dispositivo 8600 tem componentes adicionais que correspondem ao compartimento estendido de vários discos (EBOD). Na lista de componentes, há cinco seções. Entre elas, há três seções que contêm os componentes no compartimento primário e são idênticas àquelas descritas para 8100. Há duas seções adicionais para o compartimento EBOD que descrevem:

* **Componentes compartilhados do compartimento EBOD** – Os componentes presentes no compartimento EBOD e PCM que não fazem parte do controlador EBOD .
* **Componentes do controlador 0** – Os componentes que residem no compartimento EBOD 0, como controlador EBOD, expansor SAS e conector, sensores de temperatura do controlador.
* **Componentes do controlador 1** – Os componentes que constituem o compartimento 1, similar aos detalhados para o compartimento 0.

> [!NOTE]
> **A seção de status de hardware não está presente na página Manutenção para o dispositivo virtual StorSimple (1100).**
> 
> 

## <a name="monitor-the-hardware-status"></a>Monitorar o status de hardware
Execute as etapas a seguir para exibir o status de hardware de um componente de dispositivo:

1. Navegue até **dispositivos**, selecione um dispositivo StorSimple específico. Clique para ir para o menu de nível de dispositivo e clique na guia **Manutenção**. 
2. Localize a seção **Status de Hardware** e escolha os componentes disponíveis (como descrito acima). Basta clicar em uma seta que precede o rótulo de componente para expandir a lista e exibir o status de vários componentes do dispositivo. Consulte a [lista detalhada de componentes para o compartimento primário](#component-list-for-primary-enclosure-of-storsimple-device) e a [lista detalhada de componentes para o compartimento EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).
3. Use o seguinte esquema de codificação por cor para interpretar o status do componente:
   
   * **Marca verde** – Indica um componente **Íntegro** ou **OK**.
   * **Amarelo** – Indica um componente no estado de **Aviso**.
   * **Ponto de exclamação vermelho** – Indica um componente que tem um status de **Falha** ou **Precisa de atenção**.
   * **Branco com texto preto** – Indica um componente que não está presente.
4. Se você encontrar um componente que não está em um estado **Íntegro** , entre em contato com o Suporte da Microsoft. Se os alertas forem habilitados no seu dispositivo, você receberá um alerta por email. Se você precisar substituir um componente de hardware com falha, consulte [Substituição de componentes de hardware do StorSimple](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista de componentes para o compartimento primário do dispositivo StorSimple
A tabela a seguir descreve os componentes físicos e lógicos contidos no compartimento primário do dispositivo StorSimple local.

| Componente | Módulo | Tipo | Local | Unidade renovável (FRU)? | Descrição |
| --- | --- | --- | --- | --- | --- |
| Unidade no slot [0-11] |Unidades de disco |Físico |Compartilhado |Sim |É apresentada uma linha para cada uma das unidades SSD ou HDD no compartimento primário. |
| Sensor de temperatura ambiente |Compartimento |Físico |Compartilhado |Não |Mede a temperatura dentro do chassi. |
| Sensor de temperatura do plano intermediário |Compartimento |Físico |Compartilhado |Não |Mede a temperatura do plano intermediário. |
| Alarme audível |Compartimento |Físico |Compartilhado |Não |Indica se o subsistema de alarme audível dentro do gabinete é funcional. |
| Compartimento |Compartimento |Físico |Compartilhado |Sim |Indica a presença de um chassi. |
| Configurações de compartimento |Compartimento |Físico |Compartilhado |Não |Refere-se ao painel frontal do chassi. |
| Sensores de tensão de linha |PCM |Físico |Compartilhado |Não |Vários sensores de tensão de linha têm seu estado exibido, que indica se a tensão medida está dentro da tolerância. |
| Sensores de corrente de linha |PCM |Físico |Compartilhado |Não |Vários sensores de corrente de linha têm seu estado exibido, que indica se a corrente medida está dentro da tolerância. |
| Sensores de temperatura em PCM |PCM |Físico |Compartilhado |Não |Vários sensores de temperatura, como de Entrada e Ponto de acesso, têm seu estado exibido, indicando se a temperatura medida está dentro da tolerância. |
| Fonte de alimentação [0-1] |PCM |Físico |Compartilhado |Sim |É apresentada uma linha para cada uma das fontes de alimentação nos dois PCMs localizados na parte posterior do dispositivo. |
| Resfriamento [0-1] |PCM |Físico |Compartilhado |Sim |É apresentada uma linha para cada um dos quatro ventiladores que residem nos dois PCMs. |
| Bateria [0-1] |PCM |Físico |Compartilhado |Sim |É apresentada uma linha para cada um dos módulos de bateria de backup que estão encaixados no PCM. |
| Metis |N/D |Lógico |Compartilhado |N/D |Exibe o estado das baterias: se elas precisam de carregamento e estão no fim do tempo de vida. |
| HDInsight |N/D |Lógico |Compartilhado |N/D |Exibe o estado do cluster que é criado entre os dois módulos do controlador integrado. |
| Nó de cluster |N/D |Lógico |Compartilhado |N/D |Indica o estado do controlador como parte do cluster. |
| Quorum de cluster |N/D |Lógico | |N/D |Indica a presença da maioria da associação do disco no pool de armazenamento de HDD. |
| Espaço de dados do HDD |N/D |Lógico |Compartilhado |N/D |O espaço de armazenamento que é usado para dados no pool de armazenamento de unidade de disco rígido (HDD). |
| Espaço de gerenciamento de HDD |N/D |Lógico |Compartilhado |N/D |O espaço reservado no pool de armazenamento de HDD para tarefas de gerenciamento. |
| Espaço de quorum do HDD |N/D |Lógico |Compartilhado |N/D |O espaço reservado no pool de armazenamento de HDD para quorum de cluster. |
| Espaço de substituição do HDD |N/D |Lógico |Compartilhado |N/D |O espaço reservado no pool de armazenamento do HDD para substituição do controlador. |
| Espaço de dados do SSD |N/D |Lógico |Compartilhado |N/D |O espaço de armazenamento usado para dados no pool de armazenamento de unidade estado sólido (SSD). |
| Espaço SSD NVRAM |N/D |Lógico |Compartilhado |N/D |O espaço de armazenamento no pool de armazenamento SSD dedicado para lógica NVRAM. |
| Pool de armazenamento do HDD |N/D |Lógico |Compartilhado |N/D |Exibe o estado do pool de armazenamento lógico que é criado a partir dos HDDs de dispositivo. |
| Pool de armazenamento do SSD |N/D |Lógico |Compartilhado |N/D |Exibe o estado do pool de armazenamento lógico que é criado a partir dos SSDs de dispositivo. |
| Controller [0-1] [estado] |E/S |Físico |Controller |Sim |Exibe o estado do controlador e se ele está no modo ativo ou em espera dentro do chassi. |
| Sensores de temperatura no controlador |E/S |Físico |Controller |Não |Vários sensores de temperatura, como o módulo de E/S, temperatura da CPU, sensores DIMM e PCI e têm seu estado exibido, que indica se a temperatura encontrada está ou não dentro da tolerância. |
| Expansor SAS |E/S |Físico |Controller |Não |Indica o estado do expansor serial anexado SCSI (SAS), que é usado para conectar o armazenamento integrado ao controlador. |
| Conector SAS [0-1] |E/S |Físico |Controller |Não |Indica o estado de cada conector SAS, que é usado para conectar o armazenamento integrado ao expansor SAS. |
| Interconexão de plano intermediário de SBB |E/S |Físico |Controller |Não |Indica o estado do conector intermediário, que é usado para conectar cada controlador ao plano intermediário. |
| Núcleo do processador |E/S |Físico |Controller |Não |Indica o estado dos núcleos de processador em cada controlador. |
| Energia de eletrônicos do compartimento |E/S |Físico |Controller |Não |Indica o estado do sistema de energia usado pelo compartimento. |
| Diagnósticos de eletrônicos do compartimento |E/S |Físico |Controller |Não |Indica o estado dos subsistemas de diagnóstico fornecidos pelo controlador. |
| Controlador de Gerenciamento de Placa-base (BMC) |E/S |Físico |Controller |Não |Indica o estado do Controlador de Gerenciamento de Placa-base (BMC), que é um processador de serviço especializado que monitora o dispositivo de hardware por meio de sensores e se comunica com o administrador do sistema por meio de uma conexão independente. |
| Ethernet |E/S |Físico |Controller |Não |Indica o estado de cada uma das interfaces de rede, ou seja, o gerenciamento e as portas de dados fornecidas no controlador. |
| NVRAM |E/S |Físico |Controller |Não |Indica o estado da NVRAM, uma memória de acesso aleatório não volátil com a bateria de reserva que serve para guardar informações importantes de aplicativos em caso de falha de energia. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista de componentes para o compartimento EBOD do dispositivo StorSimple
A tabela a seguir descreve os componentes físicos e lógicos contidos no compartimento EBOD do dispositivo StorSimple local.

| Componente | Módulo | Tipo | Local | FRU? | Descrição |
| --- | --- | --- | --- | --- | --- |
| Unidade no slot [0-11] |Unidades de disco |Físico |Compartilhado |Sim |É apresentada uma linha para cada uma das unidades HDD na frente do compartimento EBOD. |
| Sensor de temperatura ambiente |Compartimento |Físico |Compartilhado |Não |Mede a temperatura dentro do chassi. |
| Sensor de temperatura do plano intermediário |Compartimento |Físico |Compartilhado |Não |Mede a temperatura do plano intermediário. |
| Alarme audível |Compartimento |Físico |Compartilhado |Não |Indica se o subsistema de alarme audível dentro do gabinete é funcional. |
| Compartimento |Compartimento |Físico |Compartilhado |Sim |Indica a presença de um chassi. |
| Configurações de compartimento |Compartimento |Físico |Compartilhado |Não |Refere-se ao OPS ou ao painel frontal do chassi. |
| Sensores de tensão de linha |PCM |Físico |Compartilhado |Não |Vários sensores de tensão de linha têm seu estado exibido, que indica se a tensão medida está dentro da tolerância. |
| Sensores de corrente de linha |PCM |Físico |Compartilhado |Não |Vários sensores de corrente de linha têm seu estado exibido, que indica se a corrente medida está dentro da tolerância. |
| Sensores de temperatura em PCM |PCM |Físico |Compartilhado |Não |Vários sensores de temperatura, como de Entrada e Ponto de acesso, têm seu estado exibido, indicando se a temperatura medida está dentro da tolerância. |
| Fonte de alimentação [0-1] |PCM |Físico |Compartilhado |Sim |É apresentada uma linha para cada uma das fontes de alimentação nos dois PCMs localizados na parte posterior do dispositivo. |
| Resfriamento [0-1] |PCM |Físico |Compartilhado |Sim |É apresentada uma linha para cada um dos quatro ventiladores que residem nos dois PCMs. |
| Armazenamento local [HDD] |N/D |Lógico |Compartilhado |N/D |Exibe o estado do pool de armazenamento lógico que é criado a partir dos HDDs de dispositivo. |
| Controller [0-1] [estado] |E/S |Físico |Controller |Sim |Exibe o estado dos controladores no módulo EBOD. |
| Sensores de temperatura no EBOD |E/S |Físico |Controller |Não |Vários sensores de temperatura têm seu estado exibido, indicando se a temperatura encontrada está dentro da tolerância. |
| Expansor SAS |E/S |Físico |Controller |Não |Indica o estado do expansor SAS, que é usado para conectar o armazenamento integrado ao controlador. |
| Conector SAS [0-2] |E/S |Físico |Controller |Não |Indica o estado de cada conector SAS, que é usado para conectar o armazenamento integrado ao expansor SAS. |
| Interconexão de plano intermediário de SBB |E/S |Físico |Controller |Não |Indica o estado do conector intermediário, que é usado para conectar cada controlador ao plano intermediário. |
| Energia de eletrônicos do compartimento |E/S |Físico |Controller |Não |Indica o estado do sistema de energia usado pelo compartimento. |
| Diagnósticos de eletrônicos do compartimento |E/S |Físico |Controller |Não |Indica o estado dos subsistemas de diagnóstico fornecidos pelo controlador. |
| Conexão com o controlador de dispositivo |E/S |Físico |Controller |Não |Indica o estado da conexão entre o módulo E/S do EBOD e o controlador do dispositivo. |

## <a name="next-steps"></a>Próximas etapas
* Para usar o serviço StorSimple Manager para gerenciar seu dispositivo, acesse [Usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
* Se você precisar solucionar problemas de um componente do dispositivo que apresente um status degradado ou com falha, consulte [Indicadores de monitoramento do StorSimple](storsimple-monitoring-indicators.md). 
* Para substituir um componente de hardware com falha, consulte [substituição de componentes de hardware do StorSimple](storsimple-hardware-component-replacement.md).
* Se você continuar enfrentando problemas de dispositivo, [contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md).

