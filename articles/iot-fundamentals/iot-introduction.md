---
title: Introdução ao Azure IoT (Internet das Coisas)
description: Introdução explicando os conceitos básicos do Azure IoT e os serviços de IoT, incluindo exemplos que ajudam a ilustrar o uso de IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f1053062302c9a00cf49cee1cd8de5ca6652b745
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167695"
---
# <a name="what-is-azure-internet-of-things-iot"></a>O que é o Azure IoT (Internet das Coisas)?

O Azure IoT (Internet das Coisas) é uma coleção de serviços de nuvem gerenciados pela Microsoft que conectam, monitoram e controlam bilhões de ativos de IoT. Simplificando, uma solução de IoT é composta por um ou mais dispositivos IoT que se comunicam com um ou mais serviços de back-end hospedados na nuvem. 

## <a name="iot-devices"></a>Dispositivos IoT

Normalmente, um dispositivo IoT é composto por uma placa de circuito com sensores anexados que usam o Wi-Fi para se conectar à Internet. Por exemplo:

* Um sensor de pressão em uma bomba de óleo remota.
* Sensores de temperatura e umidade em uma unidade de ar-condicionado.
* Um acelerômetro em um elevador.
* Sensores de presença em uma sala.

Há uma ampla variedade de dispositivos disponíveis de diferentes fabricantes para criar a sua solução. Para obter uma lista de dispositivos certificados para trabalhar com o Hub IoT do Azure, confira o [catálogo de dispositivos do Azure Certified para IoT](https://devicecatalog.azure.com). Para a criação de protótipos, use dispositivos como um [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) ou um [Raspberry Pi](https://www.raspberrypi.org/). O DevKit tem sensores internos de temperatura, pressão, umidade e um giroscópio, um acelerômetro e um magnetômetro. O Raspberry Pi permite anexar muitos tipos diferentes de sensor. 

A Microsoft fornece [SDKs do dispositivo](../iot-hub/iot-hub-devguide-sdks.md) open-source que você pode usar para criar os aplicativos que são executados em seus dispositivos. Esses [SDKs simplificam e aceleram](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) o desenvolvimento de suas soluções de IoT.

## <a name="communication"></a>Comunicação

Normalmente, os dispositivos IoT enviam a telemetria dos sensores para serviços de back-end na nuvem. No entanto, outros tipos de comunicação são possíveis, como um serviço de back-end que envia comandos para os seus dispositivos. Estes são alguns exemplos de comunicação de dispositivo para nuvem e de nuvem para dispositivo:

* Um caminhão de refrigeração móvel envia a temperatura a cada 5 minutos para um Hub IoT. 

* O serviço de back-end envia um comando a um dispositivo a fim de alterar a frequência em que ele envia a telemetria para ajudar a diagnosticar um problema. 

* Um dispositivo envia alertas com base nos valores obtidos nos sensores. Por exemplo, um dispositivo que monitora um reator de lotes em uma usina química envia um alerta quando a temperatura excede determinado valor.

* Seu dispositivo envia as informações a serem exibidas em um painel para visualização de operadores humanos. Por exemplo, uma sala de controle de uma refinaria poderá mostrar a temperatura, a pressão e os volumes de fluxo em cada cano, permitindo que os operadores monitorem a instalação. 

Os [SDKs do dispositivo IoT](../iot-hub/iot-hub-devguide-sdks.md) e o Hub IoT dão suporte a [protocolos de comunicação](../iot-hub/iot-hub-devguide-protocols.md) comuns, como HTTP, MQTT e AMQP.

Os dispositivos IoT têm características diferentes quando comparados a outros clientes, como navegadores e aplicativos móveis. Os SDKs do dispositivo ajudam você a resolver os desafios de conectar dispositivos de maneira segura e confiável ao serviço de back-end.  Especificamente, os dispositivos IoT:

* Frequentemente são sistemas internos sem operadores humanos (diferente de um telefone).
* Eles podem ser implantados em locais remotos, nos quais o acesso físico é caro.
* Só podem ser acessados por meio do back-end da solução.
* Podem ter recursos de energia e de processamento limitados.
* Podem ter conectividade de rede intermitente, lenta ou cara.
* Talvez precisem de protocolos de aplicativo proprietários, personalizados ou específicos do setor.

## <a name="back-end-services"></a>Serviços de back-end 

Em uma solução de IoT, o serviço de back-end fornece funcionalidades como:

* Receber telemetria em escala de seus dispositivos e determinar como processar e armazenar os dados.
* Analisar a telemetria para fornecer insights, seja em tempo real ou após o ocorrido.
* Enviar comandos da nuvem para um dispositivo específico. 
* Provisionar dispositivos e controlar quais dispositivos podem se conectar à sua infraestrutura.
* Controlar o estado dos dispositivos e monitorar as atividades deles.
* Gerenciar o firmware instalado nos dispositivos.

Por exemplo, em uma solução de monitoramento remoto de uma estação de bombeamento de petróleo, o back-end de nuvem usa a telemetria das bombas para identificar um comportamento anormal. Quando o serviço de back-end identifica uma anomalia, ele pode enviar de modo automático um comando novamente ao dispositivo para executar uma ação corretiva. Esse processo gera um loop de comentários automatizado entre o dispositivo e a nuvem, o que aumenta a eficiência da solução.

## <a name="azure-iot-examples"></a>Exemplos de IoT do Azure

Para obter exemplos da vida real de como as organizações usam o IoT do Azure, confira [Estudos de casos técnicos da Microsoft sobre IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

Para ver uma análise detalhada da arquitetura IoT, confira a [Arquitetura de Referência do IoT do Microsoft Azure](/azure/architecture/reference-architectures/iot).

## <a name="next-steps"></a>Próximas etapas

Para ver alguns casos de negócios reais e a arquitetura usada, confira os [Estudos de casos técnicos do Microsoft Azure IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Para alguns projetos de exemplo que você pode experimentar com um Kit de desenvolvimento de IoT, confira o [Catálogo de projetos do Kit de desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Para obter uma explicação mais abrangente dos diferentes serviços e como eles são usados, confira [Tecnologias e serviços de IoT do Azure](iot-services-and-technologies.md).