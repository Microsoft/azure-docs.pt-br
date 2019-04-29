---
title: Componentes de hardware e status do StorSimple série 8000 | Microsoft Docs
description: Saiba como monitorar os componentes de hardware do dispositivo StorSimple por meio do serviço Gerenciador de Dispositivos do StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: a987239669e7437a179f5f24034f4dbe45535663
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632788"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Usar o serviço Gerenciador de Dispositivos do StorSimple para monitorar componentes e status de hardware

## <a name="overview"></a>Visão geral
Este artigo descreve os vários componentes físicos e lógicos em seu dispositivo StorSimple série 8000 local. Ele também explica como monitorar o status de componente de dispositivo usando a folha **Integridade e status do hardware** no serviço Gerenciador de Dispositivos do StorSimple.

A folha **Integridade e status do hardware** mostra o status de hardware de todos os componentes do dispositivo StorSimple.

Na lista de componentes para 8100, há três seções que descrevem:

* **Componentes Compartilhados** - Eles não fazem parte dos controladores, como unidades de disco, gabinete, componentes do Módulo de Alimentação e Refrigeração (PCM) e sensores de temperatura PCM, tensão de linha e corrente de linha.
* **Componentes do controlador 0** – Os componentes que residem no Controlador 0, como controlador, expansor SAS e conector, sensores de temperatura do controlador e as várias interfaces de rede.
* **Componentes do controlador 1** – Os componentes que constituem o Controlador 1, similar aos detalhados para o Controlador 0.

Um dispositivo 8600 tem componentes adicionais que correspondem ao compartimento estendido de vários discos (EBOD). Na lista de componentes, há cinco seções. Entre elas, há três seções que contêm os componentes no compartimento primário e são idênticas àquelas descritas para 8100. Há duas seções adicionais para o compartimento EBOD que descrevem:

* **Componentes do controlador 0** – Os componentes que residem no compartimento EBOD 0, como controlador EBOD, expansor SAS e conector, sensores de temperatura do controlador.
* **Componentes do controlador 1** – Os componentes que constituem o compartimento 1, similar aos detalhados para o compartimento 0.
* **Componentes compartilhados do compartimento EBOD** – Os componentes presentes no compartimento EBOD e PCM que não fazem parte do controlador EBOD .

> [!NOTE]
> **O status do hardware não está disponível para um Dispositivo de Nuvem StorSimple (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitorar o status de hardware
Execute as etapas a seguir para exibir o status de hardware de um componente de dispositivo:

1. Navegue até **dispositivos**, selecione um dispositivo StorSimple específico. Acesse **Monitorar > Integridade do hardware**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Localize a seção **Componentes de hardware** e escolha os componentes disponíveis. Basta clicar no rótulo do componente para expandir a lista e exibir o status dos vários componentes do dispositivo. Consulte a [lista detalhada de componentes para o compartimento primário](#component-list-for-primary-enclosure-of-storsimple-device) e a [lista detalhada de componentes para o compartimento EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Use o seguinte esquema de codificação por cor para interpretar o status do componente:
   
   * **Marca verde** – Indica um componente íntegro com status **OK**.
   * **Amarelo** – Indica um componente degradado no estado de **Aviso**.
   * **Ponto de exclamação vermelho** – Indica um componente falho que tem um status de **Falha**.
   * **Branco com texto preto** – Indica um componente que não está presente.
   
   A captura de tela a seguir mostra um dispositivo que tem componentes com o estado **OK**, **Aviso** e **Falha**.
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Expandindo a **Lista de componentes compartilhados**, podemos ver que a NVRAM e o cluster estão degradados.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Expandindo a lista **Componentes do controlador 1**, podemos ver que o nó de cluster falhou.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Se você encontrar um componente que não está em um estado **Íntegro** , entre em contato com o Suporte da Microsoft. Se os alertas forem habilitados no seu dispositivo, você receberá um alerta por email. Se você precisar substituir um componente de hardware com falha, consulte [Substituição de componentes de hardware do StorSimple](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista de componentes para o compartimento primário do dispositivo StorSimple
A tabela a seguir descreve os componentes físicos e lógicos contidos no compartimento primário (presente nos modelos 8100 e 8600) do seu dispositivo StorSimple local.

| Componente | Módulo | Type | Local padrão | Unidade renovável (FRU)? | DESCRIÇÃO |
| --- | --- | --- | --- | --- | --- |
| Unidade no slot [0-11] |Unidades de disco |Físico |Compartilhado |Sim |É apresentada uma linha para cada uma das unidades SSD ou HDD no compartimento primário. |
| Sensor de temperatura ambiente |Compartimento |Físico |Compartilhado |Não  |Mede a temperatura dentro do chassi. |
| Sensor de temperatura do plano intermediário |Compartimento |Físico |Compartilhado |Não  |Mede a temperatura do plano intermediário. |
| Alarme audível |Compartimento |Físico |Compartilhado |Não  |Indica se o subsistema de alarme audível dentro do gabinete é funcional. |
| Compartimento |Compartimento |Físico |Compartilhado |Sim |Indica a presença de um chassi. |
| Configurações de compartimento |Compartimento |Físico |Compartilhado |Não  |Refere-se ao painel frontal do chassi. |
| Sensores de tensão de linha |PCM |Físico |Compartilhado |Não  |Vários sensores de tensão de linha têm seu estado exibido, que indica se a tensão medida está dentro da tolerância. |
| Sensores de corrente de linha |PCM |Físico |Compartilhado |Não  |Vários sensores de corrente de linha têm seu estado exibido, que indica se a corrente medida está dentro da tolerância. |
| Sensores de temperatura em PCM |PCM |Físico |Compartilhado |Não  |Vários sensores de temperatura, como de Entrada e Ponto de acesso, têm seu estado exibido, indicando se a temperatura medida está dentro da tolerância. |
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
| Sensores de temperatura no controlador |E/S |Físico |Controller |Não  |Vários sensores de temperatura, como o módulo de E/S, temperatura da CPU, sensores DIMM e PCI e têm seu estado exibido, que indica se a temperatura encontrada está ou não dentro da tolerância. |
| Expansor SAS |E/S |Físico |Controller |Não  |Indica o estado do expansor serial anexado SCSI (SAS), que é usado para conectar o armazenamento integrado ao controlador. |
| Conector SAS [0-1] |E/S |Físico |Controller |Não  |Indica o estado de cada conector SAS, que é usado para conectar o armazenamento integrado ao expansor SAS. |
| Interconexão de plano intermediário de SBB |E/S |Físico |Controller |Não  |Indica o estado do conector intermediário, que é usado para conectar cada controlador ao plano intermediário. |
| Núcleo do processador |E/S |Físico |Controller |Não  |Indica o estado dos núcleos de processador em cada controlador. |
| Energia de eletrônicos do compartimento |E/S |Físico |Controller |Não  |Indica o estado do sistema de energia usado pelo compartimento. |
| Diagnósticos de eletrônicos do compartimento |E/S |Físico |Controller |Não  |Indica o estado dos subsistemas de diagnóstico fornecidos pelo controlador. |
| Controlador de Gerenciamento de Placa-base (BMC) |E/S |Físico |Controller |Não  |Indica o estado do Controlador de Gerenciamento de Placa-base (BMC), que é um processador de serviço especializado que monitora o dispositivo de hardware por meio de sensores e se comunica com o administrador do sistema por meio de uma conexão independente. |
| Ethernet |E/S |Físico |Controller |Não  |Indica o estado de cada uma das interfaces de rede, ou seja, o gerenciamento e as portas de dados fornecidas no controlador. |
| NVRAM |E/S |Físico |Controller |Não  |Indica o estado da NVRAM, uma memória de acesso aleatório não volátil com a bateria de reserva que serve para guardar informações importantes de aplicativos em caso de falha de energia. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista de componentes para o compartimento EBOD do dispositivo StorSimple
A tabela a seguir descreve os componentes físicos e lógicos contidos no compartimento EBOD (apenas presente no modelo 8600) do seu dispositivo StorSimple local.

| Componente | Módulo | Type | Local padrão | FRU? | DESCRIÇÃO |
| --- | --- | --- | --- | --- | --- |
| Unidade no slot [0-11] |Unidades de disco |Físico |Compartilhado |Sim |É apresentada uma linha para cada uma das unidades HDD na frente do compartimento EBOD. |
| Sensor de temperatura ambiente |Compartimento |Físico |Compartilhado |Não  |Mede a temperatura dentro do chassi. |
| Sensor de temperatura do plano intermediário |Compartimento |Físico |Compartilhado |Não  |Mede a temperatura do plano intermediário. |
| Alarme audível |Compartimento |Físico |Compartilhado |Não  |Indica se o subsistema de alarme audível dentro do gabinete é funcional. |
| Compartimento |Compartimento |Físico |Compartilhado |Sim |Indica a presença de um chassi. |
| Configurações de compartimento |Compartimento |Físico |Compartilhado |Não  |Refere-se ao OPS ou ao painel frontal do chassi. |
| Sensores de tensão de linha |PCM |Físico |Compartilhado |Não  |Vários sensores de tensão de linha têm seu estado exibido, que indica se a tensão medida está dentro da tolerância. |
| Sensores de corrente de linha |PCM |Físico |Compartilhado |Não  |Vários sensores de corrente de linha têm seu estado exibido, que indica se a corrente medida está dentro da tolerância. |
| Sensores de temperatura em PCM |PCM |Físico |Compartilhado |Não  |Vários sensores de temperatura, como de Entrada e Ponto de acesso, têm seu estado exibido, indicando se a temperatura medida está dentro da tolerância. |
| Fonte de alimentação [0-1] |PCM |Físico |Compartilhado |Sim |É apresentada uma linha para cada uma das fontes de alimentação nos dois PCMs localizados na parte posterior do dispositivo. |
| Resfriamento [0-1] |PCM |Físico |Compartilhado |Sim |É apresentada uma linha para cada um dos quatro ventiladores que residem nos dois PCMs. |
| Armazenamento local [HDD] |N/D |Lógico |Compartilhado |N/D |Exibe o estado do pool de armazenamento lógico que é criado a partir dos HDDs de dispositivo. |
| Controller [0-1] [estado] |E/S |Físico |Controller |Sim |Exibe o estado dos controladores no módulo EBOD. |
| Sensores de temperatura no EBOD |E/S |Físico |Controller |Não  |Vários sensores de temperatura têm seu estado exibido, indicando se a temperatura encontrada está dentro da tolerância. |
| Expansor SAS |E/S |Físico |Controller |Não  |Indica o estado do expansor SAS, que é usado para conectar o armazenamento integrado ao controlador. |
| Conector SAS [0-2] |E/S |Físico |Controller |Não  |Indica o estado de cada conector SAS, que é usado para conectar o armazenamento integrado ao expansor SAS. |
| Interconexão de plano intermediário de SBB |E/S |Físico |Controller |Não  |Indica o estado do conector intermediário, que é usado para conectar cada controlador ao plano intermediário. |
| Energia de eletrônicos do compartimento |E/S |Físico |Controller |Não  |Indica o estado do sistema de energia usado pelo compartimento. |
| Diagnósticos de eletrônicos do compartimento |E/S |Físico |Controller |Não  |Indica o estado dos subsistemas de diagnóstico fornecidos pelo controlador. |
| Conexão com o controlador de dispositivo |E/S |Físico |Controller |Não  |Indica o estado da conexão entre o módulo E/S do EBOD e o controlador do dispositivo. |

## <a name="next-steps"></a>Próximas etapas
* Para usar o serviço Gerenciador de Dispositivos do StorSimple para administrar seu dispositivo, acesse [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
* Se você precisar solucionar problemas de um componente do dispositivo que apresente um status degradado ou com falha, consulte [Indicadores de monitoramento do StorSimple](storsimple-monitoring-indicators.md).
* Para substituir um componente de hardware com falha, consulte [substituição de componentes de hardware do StorSimple](storsimple-hardware-component-replacement.md).
* Se você continuar enfrentando problemas de dispositivo, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

