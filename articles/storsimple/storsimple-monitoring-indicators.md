---
title: Indicadores de monitoramento do StorSimple | Microsoft Docs
description: Descreve os LEDs e os alarmes usados para monitorar o status do dispositivo StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: ef8acf1c3c9211168ebacc8d62647f6789c745a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630539"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Usar indicadores de monitoramento do StorSimple para gerenciar seu dispositivo


## <a name="overview"></a>Visão geral
O seu dispositivo StorSimple inclui diodos emissores de luz (LEDs) e alarmes que você pode usar para monitorar os módulos e o status geral do dispositivo StorSimple. Os indicadores de monitoramento podem ser encontrados nos componentes de hardware do compartimento primário do dispositivo e o do compartimento EBOD. Os indicadores de monitoramento podem ser LEDs ou alarmes audíveis.

Há três estados de LED usados para indicar o status de um módulo: verde, verde intermitente a vermelho-âmbar ou vermelho-âmbar.  

* LEDs verdes representam o status de funcionamento adequado.  
* Verde intermitente a vermelho-âmbar representam a presença de condições não críticas que podem exigir a intervenção do usuário.  
* LEDs vermelho-âmbar indicam que há uma falha crítica presente no módulo.  

O restante deste artigo descreve os vários LEDs indicadores de monitoramento, seus locais no dispositivo StorSimple, o status do dispositivo com base em estados do LED e quaisquer alarmes audíveis associados.

## <a name="front-panel-indicator-leds"></a>LEDs indicadores no painel frontal
O painel frontal, também conhecido como o *painel de operações* ou *painel ops*, exibe o status da agregação de todos os módulos no sistema. O painel frontal é idêntico no compartimento StorSimple primário e no compartimento EBOD, e é ilustrado abaixo.  

   ![ Painel frontal do dispositivo][1]

O painel frontal contém os seguintes indicadores:  

1. Botão silenciar
2. LED Indicador de energia (verde/vermelho-âmbar)
3. LED indicador de falha de módulo (aceso vermelho-âmbar/apagado)
4. LED indicador de falha lógica (aceso vermelho-âmbar/apagado)
5. Exibição da ID da unidade  

A principal diferença entre os LEDs do painel frontal do dispositivo e do compartimento EBOD é o **Número de Identificação da Unidade de Sistema** mostrado na tela do LED. A ID de unidade padrão exibida no dispositivo é **00**, enquanto que a ID de unidade padrão exibida no compartimento EBOD é **01**. Isso permite que você possa diferenciar rapidamente entre o dispositivo e o compartimento EBOD quando o dispositivo está ativado. Se o dispositivo estiver desligado, use as informações fornecidas em [Ligar um novo dispositivo](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) para diferenciar o dispositivo do compartimento EBOD.  

## <a name="front-panel-led-status"></a>Status do LED do painel frontal
Use a tabela a seguir para identificar o status indicado pelos LEDs no painel frontal do dispositivo ou do compartimento EBOD.  

| Energia do sistema | Falha do módulo | Falha lógica | Alarme | Status |
| --- | --- | --- | --- | --- |
| Vermelho-âmbar |DESATIVADO |DESATIVADO |N/D |Perda de energia CA, operando com energia reserva ou energia CA ativada e os módulos do controlador foram removidos. |
| Verde |ATIVADO |ATIVADO |N/D |Estado de teste do painel de operações ligado (5s) |
| Verde |DESATIVADO |DESATIVADO |N/D |Energia ligada, todas as funções em boas condições |
| Verde |ATIVADO |N/D |LEDs de falha do ventilador, LEDs de falha de PCM |Qualquer falha de PCM, falha do ventilador, temperatura acima ou abaixo do recomendado |
| Verde |ATIVADO |N/D |LEDs de módulo de E/S |Qualquer falha do módulo do controlador |
| Verde |ATIVADO |N/D |N/D |Falha lógica no compartimento |
| Verde |Piscando |N/D |LED de status de módulo no módulo do controlador. LEDs de falha do ventilador, LEDs de falha de PCM |Tipo de módulo controlador desconhecido instalado, falha de barramento I2C, erro de configuração dos dados vitais do produto (VPD) do módulo do controlador |

## <a name="power-cooling-module-pcm-indicator-leds"></a>LEDs indicadores de refrigeração do módulo de energia (PCM)
LEDs indicadores de refrigeração do módulo de energia (PCM) podem ser encontrados na parte traseira do compartimento principal ou do compartimento EBOD em cada módulo PCM. Este tópico discute como usar os seguintes LEDs para monitorar o status do seu dispositivo StorSimple.  

* LEDs de PCM para o compartimento principal
* LEDs de PCM para o compartimento EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>LEDs de PCM para o compartimento principal
O dispositivo StorSimple tem um módulo PCM de 764W com uma bateria adicional. A ilustração a seguir mostra o painel de LED para o dispositivo.  

   ![LEDs de PCM no compartimento principal][2]

Legenda do LED:

1. Falha de energia CA
2. Falha do ventilador
3. Falha de bateria
4. PCM OK
5. Falha de CC
6. Bateria boa  

O status do PCM é indicado no painel do LED O painel do LED do PCM do dispositvo possui seis LEDs. Quatro destes LEDs exibem o status da fonte de energia e do ventilador. Os outros dois LEDs indicam o status do módulo da bateria de backup no PCM. Você pode usar as tabelas a seguir para determinar o status do PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>LEDs indicadores do PCM para a fonte de energia e para o ventilador
| Status | PCM OK (verde) | Falha de CA (âmbar) | Falha do ventilador (âmbar) | Falha de DC (âmbar) |
| --- | --- | --- | --- | --- |
| Sem energia de CA (para o compartimento) |DESATIVADO |DESATIVADO |DESATIVADO |DESATIVADO |
| Sem energia de CA (somente este PCM) |DESATIVADO |ATIVADO |DESATIVADO |ATIVADO |
| CA presente PCM ligado - OK |ATIVADO |DESATIVADO |DESATIVADO |DESATIVADO |
| Falha no PCM (falha do ventilador) |DESATIVADO |DESATIVADO |ATIVADO |N/D |
| Falha no PCM (excesso de amperagem, sobretensão, sobrecorrente) |DESATIVADO |ATIVADO |ATIVADO |ATIVADO |
| PCM (ventilador fora da tolerância) |ATIVADO |DESATIVADO |DESATIVADO |ATIVADO |
| Modo standby |Piscando |DESATIVADO |DESATIVADO |DESATIVADO |
| Download de firmware do PCM |DESATIVADO |Piscando |Piscando |Piscando |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>LEDs indicadores do PCM para a bateria de backup
| Status | Bateria boa (verde) | Falha na bateria (âmbar) |
| --- | --- | --- |
| Bateria ausente |DESATIVADO |DESATIVADO |
| Bateria presente e carregada |ATIVADO |DESATIVADO |
| Bateria carregando ou descarga de manutenção |Piscando |DESATIVADO |
| Falha “pequena” na bateria (recuperável) |DESATIVADO |Piscando |
| Falha “grave” na bateria (não recuperável) |DESATIVADO |ATIVADO |
| Bateria desarmada |Piscando |DESATIVADO |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>LEDs de PCM para o compartimento EBOD
O compartimento EBOD possui um PCM de 580W e nenhuma bateria adicional. O painel do PCM para o compartimento EBOD possui LEDs indicadores para as fontes de energia e para o ventilador. A ilustração a seguir mostra estes LEDs.

   ![LEDs de PCM no compartimento EBOD][3] 

Você pode usar a tabela a seguir para determinar o status do PCM.  

| Status | PCM OK (verde) | Falha de CA (âmbar) | Falha do ventilador (âmbar) | Falha de DC (âmbar) |
| --- | --- | --- | --- | --- |
| Sem energia de CA (para o compartimento) |DESATIVADO |DESATIVADO |DESATIVADO |DESATIVADO |
| Sem energia de CA (somente este PCM) |DESATIVADO |ATIVADO |DESATIVADO |ATIVADO |
| CA presente PCM ligado - OK |ATIVADO |DESATIVADO |DESATIVADO |DESATIVADO |
| Falha no PCM (falha do ventilador) |DESATIVADO |DESATIVADO |ATIVADO |X |
| Falha no PCM (excesso de amperagem, sobretensão, sobrecorrente |DESATIVADO |ATIVADO |ATIVADO |ATIVADO |
| PCM (ventilador fora da tolerância) |ATIVADO |DESATIVADO |DESATIVADO |ATIVADO |
| Modelo standby |Piscando |DESATIVADO |DESATIVADO |DESATIVADO |
| Download de firmware do PCM |DESATIVADO |Piscando |Piscando |Piscando |

## <a name="controller-module-indicator-leds"></a>LEDs indicadores do módulo do controlador
O dispositivo StorSimple contém LEDs para o controlador primário e para os módulos do controlador EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>LEDs de monitoramento para o controlador primário.
A ilustração a seguir ajuda a identificar os LEDs no controlador primário. (Todos os componentes são listados para ajudar na orientação.)  

   ![LEDs de monitoramento - controlador primário][4]

Use a tabela a seguir para determinar se o módulo do controlador está operando corretamente.  

### <a name="controller-indicator-leds"></a>LEDs indicadores do controlador
| LED | DESCRIÇÃO |
| --- | --- |
| LED de ID (azul) |Indica se o módulo está sendo identificado. Se o LED azul estiver piscando em um controlador em execução, então o controlador é o controlador ativo e o outro é o controlador standby. Para saber mais, consulte [Identificar o controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| LED de falha (âmbar) |Indica uma falha no controlador. |
| LED de OK (verde) |Um verde estável indica que o controlador está OK. Verde piscando indica um erro de configuração de VPD do controlador. |
| LEDs de atividade de SAS (verde) |Verde estável indica uma conexão sem atividade. Verde piscando indica que a conexão possui atividade em andamento. |
| LEDs de status de Ethernet |O lado direito indica atividade do link/rede: (verde estável) link ativo, (verde piscando) atividade de rede. O lado esquerdo indica a velocidade da rede: (amarelo) 1000 Mb/s, (verde) 100 Mb/s e (OFF) 10 Mb/s Dependendo do modelo do componente essa luz pode piscar mesmos se a interface de rede não estiver habilitada. |
| LEDs de POST |Indica se o andamento da inicialização quando o controlador estiver ligado. Se o dispositivo StorSimple falhar durante a inicialização, esse LED ajudará a Microsoft a identificar o ponto no processo de incialização onde ocorreu a falha. |

> [!IMPORTANT]
> Se o LED de falha estiver aceso, há um problema com o módulo do controlador que pode ser resolvido através da reinicialização do controlador. Entre em contato com o Suporte da Microsoft se a reinicialização do controlador não resolver esse problema.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitorando LEDs para o EBOD (compartimento EBOD)
Cada um dos controladores EBOD de SAS de 6 Gb/s possui LEDs que indicam o seu status conforme mostrado na ilustração a seguir.  

  ![LEDs de monitoramento - compartimento EBOD][5]

Use a tabela a seguir para determinar se o módulo do controlador EBOD está operando normalmente.  

### <a name="ebod-controller-module-indicator-leds"></a>LEDs indicadores do módulo do controlador EBOD
| Status | Módulo de E/S OK (verde) | Falha no módulo de E/S (âmbar) | Atividade da porta do host (verde) |
| --- | --- | --- | --- |
| Módulo do controlador OK |ATIVADO |DESATIVADO |- |
| Falha do módulo do controlador |DESATIVADO |ATIVADO |- |
| Nenhuma conexão externa de porta de host |- |- |DESATIVADO |
| Conexão externa de porta de host – sem atividade |- |- |ATIVADO |
| Conexão externa de porta de host – com atividade |- |- |Piscando |
| Erro de metadados do módulo do controlador |Piscando |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>LEDs indicadores do drive de disco para o compartimento primário e para o compartimento EBOD
O dispositivo StorSimple possui drives de disco localizados no compartimento primário e no compartimento EBOD. Cada drive de disco contém LEDs de indicador de monitoramento, conforme descrito nesta seção. 

Para os drives de disco, o status do drive é indicado por um LED verde e um LED vermelho-âmbar montado na parte da frente de cada módulo portador do drive. A ilustração a seguir mostra estes LEDs.

  ![LEDs de unidade de disco][6]

Use a tabela a seguir para determinar o estado de cada drive de disco que, por sua vez, afeta o status geral do LED do painel frontal.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>LEDs do indicador do drive de disco para o compartimento EBOD
| Status | LED de Atividade OK (verde) | LED de falha (vermelho) | LED do painel de operações associado |
| --- | --- | --- | --- |
| Nenhum driver instalado |DESATIVADO |DESATIVADO |Nenhum |
| Driver instalado e operacional |Piscando com atividade |X |Nenhum |
| Conjunto de identidades do dispositivo Enclosure Services do SCSI (SES) |ATIVADO |Piscando 1 segundo ligado/1 segundo desligado |Nenhum |
| Conjunto de bits de falha do dispositivo SES |ATIVADO |ATIVADO |Falha lógica (vermelha) |
| Falha no circuito de controle de energia |DESATIVADO |ATIVADO |Falha no módulo (vermelho) |

## <a name="audible-alarms"></a>Alarmes audíveis
Um dispositivo StorSimple contém alarmes audíveis associados com o compartimento primário e com o compartimento EBOD. Um alarme audível é localizando no painel frontal (também chamado de painel de operações) de ambos os compartimentos. O alarme audível indica quando uma condição de falha está presente. As condições a seguir ativarão o alarme:  

* Falha do ventilador
* Tensão fora dos limites
* Temperatura acima ou abaixo dos limites
* Saturação térmica
* Falha no sistema
* Falha lógica
* Falha no fornecimento de energia
* Remoção de um módulo de refrigeração de energia (PCM)  

A tabela a seguir descreve os vários estados de alarme.  

### <a name="alarm-states"></a>Estados de alarme.
| Estado de alarme | Ação | Ação com o botão de mudo pressionado |
| --- | --- | --- |
| S0 |Modo normal: silencioso |Dois bipes |
| S1 |Modo com falha: 1 segundo em/1 segundo desligado |Transição para S2 ou S3 (consulte as observações) |
| S2 |Modo lembrete: bipe intermitente |Nenhum |
| S3 |Modo mudo: silencioso |Nenhum |
| S4 |Modo de falha crítica: alarme contínuo |Não disponível: mudo não ativo |

> [!NOTE]
> * No estado de alarme S1, se você não apertar mudo em até 2 minutos, o estado faz uma transição automática para S2 ou S3.  
> * Os estados de alarme S1 a S4 retornam para S0 após a condição de falha ter sido resolvida.  
> * O estado de falha crítica S4 pode ser inserido a partir de qualquer outro estado.  


Você pode silenciar o alarme audível apertando o botão de mudo no painel de operações. Um silenciament automático ocorrerá após dois minutos se o botão de mudo não for manualmente acionado. Quando o alarme estiver mudo, ele continuará a soar através de bipes curtos e intermitentes para indicar que ainda existe um problema. O alarme será silenciado quando todos os problemas forem resolvidos.

A tabela a seguir descreve as várias condições de alarme.

### <a name="alarm-conditions"></a>Condições de alarme.
| Status | Severity | Alarme | LED do painel de operações |
| --- | --- | --- | --- |
| Alerta de PCM – perda de energia de CC de um único PCM |Falha – nenhuma perda de redundância |S1 |Falha do módulo |
| Alerta de PCM – perda de energia de CC de um único PCM |Falha – perda de redundância |S1 |Falha do módulo |
| Falha do ventilador do PCM |Falha – perda de redundância |S1 |Falha do módulo |
| Falha do PCM detectada no módulo do SBB |Falha |S1 |Falha do módulo |
| PCM removido |Erro de configuração |Nenhum |Falha do módulo |
| Erro de configuração do compartimento |Falha – crítica |S1 |Falha do módulo |
| Alerta aviso de temperatura baixa |Aviso |S1 |Falha do módulo |
| Alerta aviso de temperatura alta |Aviso |S1 |Falha do módulo |
| Alarme de sobretemperatura |Falha – crítica |S1 |Falha do módulo |
| Falha do barramento I2C |Falha – perda de redundância |S1 |Falha do módulo |
| Erro de comunicação do painel de operações (I2C) |Falha – crítica |S1 |Falha do módulo |
| Erro no controlador |Falha – crítica |S1 |Falha do módulo |
| Falha do módulo da interface SBB |Falha – crítica |S1 |Falha do módulo |
| Falha no módulo da interface SBB – Nenhum módulo em funcionamento restante |Falha – crítica |S4 |Falha do módulo |
| Módulo da interface SBB removido |Aviso |Nenhum |Falha do módulo |
| Falha de controle de energia do drive |Aviso – nenhuma perda de energia no drive |S1 |Falha do módulo |
| Falha de controle de energia do drive |Falha – crítica; perda de energia do drive |S1 |Falha do módulo |
| Drive removido |Aviso |Nenhum |Falha do módulo |
| Energia insuficiente disponível |Aviso |Nenhum |Falha do módulo |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os [componentes e o status de hardware do StorSimple](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


