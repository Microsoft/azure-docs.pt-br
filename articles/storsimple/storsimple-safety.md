---
title: Segurança para seu dispositivo StorSimple | Microsoft Docs
description: Descreve considerações, diretrizes e convenções de segurança e explica como instalar e operar seu dispositivo StorSimple com segurança.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: dae6d535-1ca2-4d2b-b221-6819043aa068
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: alkohli
ms.openlocfilehash: 9d0c8c01ae0d87c944922dece74e13f00879b02f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629162"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Instalar e operar seu dispositivo StorSimple com segurança
![Ícone de aviso](./media/storsimple-safety/IC740879.png)
![Leia o ícone de aviso de segurança](./media/storsimple-safety/IC740885.png) **LEIA AS INFORMAÇÕES DE SEGURANÇA E INTEGRIDADE**

Leia todas as informações de segurança e integridade neste artigo, que aplicam-se ao dispositivo do Microsoft Azure StorSimple. Guarde todos os guias impressos fornecidos com o dispositivo StorSimple para referência futura. Não seguir as instruções nem configurar, usar e manter adequadamente este produto pode aumentar o risco de danos graves, morte ou danificar o dispositivo ou dispositivos. Uma [versão para download deste guia](https://www.microsoft.com/download/details.aspx?id=44233) também está disponível.

## <a name="safety-icon-conventions"></a>Convenções de ícones de segurança
Estes são os ícones que você encontrará ao ler as precauções de segurança a serem observadas ao configurar e executar seu dispositivo Microsoft Azure StorSimple.

| ícone | DESCRIÇÃO |
|:--- |:--- |
| ![Ícone de perigo](./media/storsimple-safety/IC740879.png) **PERIGO!** |Indica uma situação perigosa que, se não for evitada, resultará em morte ou lesões graves. Essa palavra limitas-e às situações mais graves. |
| ![Ícone de aviso](./media/storsimple-safety/IC740879.png) **AVISO!** |Indica uma situação perigosa que, se não for evitada, pode causar lesões graves ou de morte. |
| ![Ícone de aviso](./media/storsimple-safety/IC740879.png) **CUIDADO!** |Indica uma situação perigosa que, se não for evitada, pode em uma lesão pequena ou moderada. |
| ![Ícone de observação](./media/storsimple-safety/IC740881.png) **OBSERVAÇÃO:** |Indica informações consideradas importantes, mas não são relacionadas a riscos. |
| ![Ícone de choque elétrico](./media/storsimple-safety/IC740882.png) **Risco de choque elétrico** |Alta tensão |
| ![Ícone de peso pesado](./media/storsimple-safety/IC740883.png) **Peso pesado** | |
| ![Ícone de nenhuma peça operada pelo usuário](./media/storsimple-safety/IC740879.png) **Nenhuma peça é operada pelo usuário** |Não acesse a menos que seja devidamente treinado. |
| ![Leia o ícone de aviso de segurança](./media/storsimple-safety/IC740885.png)**Leia todas as instruções primeiro** | |
| ![Ícone de risco de tombamento](./media/storsimple-safety/IC740886.png) **Risco de tombamento** | |

## <a name="handling-precautions"></a>Precauções de manuseio
![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![Ícone de peso pesado](./media/storsimple-safety/IC740883.png) **AVISO!** 

Para reduzir o risco de lesões:

* Um gabinete completamente configurado pode pesar até 32 kg (70 lbs); não tente erguê-lo por conta própria.
* Antes de mover o compartimento, certifique-se sempre de que duas pessoas estejam disponíveis para dar suporte ao peso. Esteja ciente que uma pessoa que tente erguer esse peso sozinha pode sofrer lesões.
* Não erga o compartimento pelas alças nos PCMs (Módulos de Energia e Resfriamento) localizados na parte traseira da unidade. Eles não se destinam para aguentar esse peso.

## <a name="connection-precautions"></a>Precauções de conexão
![Warning Icon](./media/storsimple-safety/IC740879.png) ![Electrical Shock Icon](./media/storsimple-safety/IC740882.png) **AVISO!**

Para reduzir a probabilidade de lesões, choque elétrico ou morte:

* Quando alimentado por várias fontes de CA, desconecte todas as fontes de energia para um isolamento completo.
* Desconecte permanentemente a unidade antes de movê-la ou se achar que ela foi danificada de alguma forma.
* Forneça uma conexão terra elétrica segura para os cabos de alimentação. Verifique se o aterramento do compartimento cumpre os requisitos locais e nacionais antes de ligar a energia.
* Certifique-se de que a conexão de energia esteja sempre desconectada antes de remover um PCM do compartimento.
* Considerando que o plugue no cabo de alimentação é o dispositivo de desconexão principal, verifique se as saídas de soquete localizadas perto do equipamento e são de fácil acesso.

![Warning Icon](./media/storsimple-safety/IC740879.png) ![Electrical Shock Icon](./media/storsimple-safety/IC740882.png) **AVISO!**

Para reduzir a probabilidade de superaquecimento ou chamas nas conexões elétricas:

* Forneça uma fonte de energia adequada com a proteção contra sobrecarga elétrica para atender aos requisitos detalhados nas especificações técnicas.
* Não use cabos de alimentação bifurcados (conectores em "Y").
* Para atender aos requisitos térmicos, de emissão e de segurança aplicáveis, nenhuma tampa deverá ser removida e todos os compartimentos devem ser preenchidos com ou módulos plug-in ou unidades em branco.
* Certifique-se de que o equipamento seja usado da maneira especificada pelo fabricante. Se este equipamento for usado de maneira não especificada pelo fabricante, a proteção fornecida pelo equipamento poderá ser afetada.

![Ícone de observação](./media/storsimple-safety/IC740881.png) **OBSERVAÇÃO:**

Para a operação adequada do seu equipamento e para evitar danos ao produto:

* As portas RJ45 na parte posterior do dispositivo servem somente para conexão Ethernet. Elas não devem estar conectadas a uma rede de telecomunicações.
* Instale o dispositivo em um rack que possa acomodar um design de resfriamento de frente para trás.
* Todos os módulos de plug-in e placas em branco fazem parte do compartimento do sistema. Eles devem ser removidos somente caso um substituto possa ser adicionado imediatamente. O sistema não deve ser executado sem todos os módulos ou módulos em branco no devido lugar.

## <a name="rack-system-precautions"></a>Precauções do sistema de rack
Os seguintes requisitos de segurança devem ser considerados ao montar o dispositivo em um gabinete de rack.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![Ícone de risco de tombamento](./media/storsimple-safety/IC740886.png) **AVISO!**

Para reduzir a probabilidade de lesões causadas por tombamento:

* O design de rack deve dar suporte ao peso total dos compartimentos instalados e deve incorporar recursos de estabilização adequados para impedir que o rack tombe ou deslize durante a instalação ou uso normal.
* Ao montar um rack, preencha-o de baixo para cima e esvazie-o de cima para baixo.
* Não retire mais de um gabinete do rack por vez para evitar o risco de o rack tombar.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![Ícone de choque elétrico](./media/storsimple-safety/IC740882.png) **AVISO!**

Para reduzir a probabilidade de lesões, choque elétrico ou morte:

* O rack deve ter um sistema de distribuição elétrica seguro. Ele deve fornecer proteção contra sobrecarga para o compartimento e não deve ser sobrecarregado pelo número total de compartimentos instalados. A classificação de consumo de energia elétrica mostrada na placa deve ser observada.
* O sistema de distribuição elétrica deve fornecer aterramento confiável para cada compartimento no rack.
* O design do sistema de distribuição de energia deve levar em consideração a corrente de perda do aterramento total de todas as fontes de alimentação em todos os compartimentos. Observe que cada fonte de alimentação em cada compartimento tem uma corrente de perda de aterramento máximo de mA 1,0 a 60 Hz, 264 v. O rack pode exigir rótulos como "CORRENTE DE ALTA PERDA. O aterramento é essencial para conectar a alimentação."
* O rack, quando configurado com os compartimentos deve atender aos requisitos de segurança UL 60950-1 e IEC 60950-1/EN 60950-1.

![Ícone de observação](./media/storsimple-safety/IC740881.png) **OBSERVAÇÃO:**

Para o resfriamento correto do seu sistema de rack:

* Certifique-se de que o design de rack considere a temperatura ambiente máxima de operação de 35 °C (95 °F).
* O sistema é operado com instalação de exaustor traseiro de baixa pressão (a pressão traseira criada pelas portas e obstáculos do rack não excedem 5 Pascal [0,5 mm no medidor de água]).

## <a name="power-cooling-module-pcm-precautions"></a>Precauções do PCM (Módulo de Resfriamento de Energia)
Este dispositivo foi projetado para operar com dois PCMs. Cada PCMs tem uma fonte de energia e uma ventoinha de dois eixos. Durante uma condição crítica, o sistema dá suporte à falha de uma fonte de alimentação e continua com suas operações normais. Dois PCMs (e, portanto, duas fontes de alimentação) sempre devem estar instalados. Um único PCM não oferece fontes de alimentação redundantes. Por isso, a falha de um dos PCMs pode resultar em tempo de inatividade ou possível perda de dados.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![Ícone de choque elétrico](./media/storsimple-safety/IC740882.png) **AVISO!**

Para reduzir a probabilidade de lesões, choque elétrico ou morte:

* Não remova as tampas do PCM. Há risco de choque elétrico no interior. Para devolver o PCM e obter um substituto, [contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md).

![Ícone de observação](./media/storsimple-safety/IC740881.png) **OBSERVAÇÃO:**

Para a operação adequada do seu equipamento e para evitar danos ao produto:

* Você deve substituir o PCM com falha dentro de 24 horas. Após a remoção de um PCM para substituição, ela deve ser concluída dentro de 10 minutos após a remoção.
* Não remova um PCM a menos que um substituto possa ser instalado imediatamente. O compartimento não deve ser operado sem todos os módulos no lugar.

## <a name="electrostatic-discharge-esd-precautions"></a>Precauções contra ESD (descarga elétrica)
![Ícone de observação](./media/storsimple-safety/IC740881.png) **OBSERVAÇÃO:**

Observe as seguintes precauções relacionada a ESD.

* Instale e confira o funcionamento de uma pulseira ou tornozeleira antiestática adequada.
* Observe todas as precauções contra ESD convencionais ao manusear os módulos e componentes.
* Evite o contato com os componentes de backplane e conectores de módulo.
* Danos de ESD não são cobertos pela garantia.

## <a name="battery-disposal-precautions"></a>Precauções de descarte de bateria
A fonte de alimentação usa uma bateria especial para proteger o conteúdo da memória durante interrupções de energia temporárias e de curto prazo. A bateria está encaixada no PCM. Lembre-se das informações a seguir sobre a bateria.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) **AVISO!**

Para reduzir o risco de curto, incêndio, explosão, lesões ou morte:

* Descarte as baterias usadas de acordo com as regulamentações nacionais/regionais.
* Não desmonte, amasse, aqueça acima de 60 °C (140 °F) ou incinere a bateria. Substitua a bateria do PCM apenas por uma bateria fornecida. Usar outra bateria pode apresentar risco de incêndio ou explosão.
* Use tampas protetoras nas baterias se elas forem removidas da fonte de alimentação.

![Ícone de observação](./media/storsimple-safety/IC740881.png) **OBSERVAÇÃO:**

Ao transportar ou transportar as baterias por via aérea, siga o documento de orientação da bateria de lítio da IATA disponível em [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Depois de revisar esses avisos de segurança, as próximas etapas serão desembalar, montar no rack e cabear o dispositivo.

## <a name="next-steps"></a>Próximas etapas
* Para um dispositivo 8100, acesse [Instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md).
* Para um dispositivo 8600, acesse [Instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

