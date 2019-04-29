---
title: Arquitetura de referência de aceleradores de solução do IoT - Azure | Microsoft Docs
description: Saiba mais sobre a arquitetura de referência dos aceleradores de solução de IoT do Azure. Os aceleradores de solução existentes aproveitam essa arquitetura de referência. Você também pode usar a arquitetura de referência quando você cria suas próprias soluções personalizadas de IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/04/2018
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: philmea
ms.openlocfilehash: ba5eb50dcf800c186124db348ac584ff6f55cebb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450289"
---
# <a name="introduction-to-the-azure-iot-reference-architecture"></a>Introdução à arquitetura de referência do IoT do Azure

Este artigo apresenta a [arquitetura de referência do IoT do Azure](https://aka.ms/iotrefarchitecture) e fornece exemplos de como os [aceleradores de solução de IoT do Azure](about-iot-accelerators.md) seguem suas recomendações.

Os aceleradores de solução [monitoramento remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md) e [Connected Factory](iot-accelerators-connected-factory-sample-walkthrough.md) de código-fonte aberto seguem muitas das recomendações de arquitetura de referência. Você pode usar os aceleradores de solução como o ponto de partida para sua própria solução de IoT ou como ferramentas de aprendizado.

## <a name="overview"></a>Visão geral

A arquitetura de referência descreve os elementos de uma solução de IoT, como os princípios de tecnologia, a composição de serviços do Azure IoT e os dispositivos. A arquitetura também faz recomendações sobre tecnologias de implementação.

No nível mais alto, você pode exibir uma solução de IoT como sendo composta por:

* Coisas que geram e enviam telemetria, como medidas e eventos. No acelerador de solução de monitoramento remoto, dispositivos como caminhões ou elevadores são as coisas que enviam telemetria.
* Insights gerados a partir a telemetria enviada pelas coisas. No acelerador de solução de monitoramento remoto, uma regra gera um insight. Por exemplo, uma regra pode identificar quando a temperatura em um mecanismo atinge um limite.
* Ações, com base em insights, que ajudam a melhorar uma empresa ou processo. No acelerador de solução de monitoramento remoto, uma ação de e-mail pode notificar um operador sobre um possível problema com um mecanismo.

A [arquitetura de referência do IoT do Azure](https://aka.ms/iotrefarchitecture) é um documento dinâmico que atualiza à medida que a tecnologia evolui.

## <a name="core-subsystems"></a>Subsistemas principais

A arquitetura de referência identifica os principais subsistemas mostrados no diagrama a seguir:

![Subsistemas principais](media/iot-accelerators-architecture-overview/CoreSubsystems.png)

As seções a seguir descrevem como os componentes do acelerador de solução de monitoramento remoto são mapeados nos principais subsistemas.

### <a name="iot-devices"></a>Dispositivos IoT

Uma solução de IoT deve habilitar a comunicação segura, eficiente e robusta entre quase todo tipo de dispositivo e um gateway de nuvem. Os dispositivos são ativos comerciais que variam de sensores de temperatura simples a linhas de produção de fábrica complexas com centenas de componentes e sensores.

Um gateway de campo ou dispositivo edge é um aparelho especializado de dispositivo ou software para fins gerais que pode atuar como:

* Habilitador de comunicação para lidar com a conversão de protocolo.
* Sistema de controle de dispositivo local e o hub de processamento de telemetria do dispositivo. Um dispositivo edge pode processar telemetria localmente para controlar dispositivos sem comunicação com a nuvem. Um dispositivo edge também pode filtrar ou agregar telemetria de dispositivos para reduzir a quantidade de telemetria transferida para a nuvem.

Na solução de monitoramento remoto, os dispositivos se conectam a um hub IoT e enviam telemetria para processamento. A solução de monitoramento remoto também permite que os operadores gerenciem dispositivos que usam gerenciamento automático de dispositivo ou trabalhos. Você pode usar os SDKs do dispositivo IoT do Azure para implementar seus dispositivos.

A solução de monitoramento remoto pode implantar e gerenciar dispositivos do IoT Edge. Processamento no edge ajuda a reduzir o volume de telemetria enviado para a nuvem e a acelerar as respostas a eventos de dispositivo.

### <a name="cloud-gateway"></a>Gateway de nuvem

Um gateway de nuvem permite a comunicação entre dispositivos e dispositivos edge. Esses dispositivos podem estar em vários locais remotos.

O gateway gerencia a comunicação do dispositivo, incluindo o gerenciamento de conexão, a proteção do caminho de comunicação, autenticação e autorização. O gateway também impõe cotas de taxa de transferência e a conexão e coleta a telemetria usada para cobrança, diagnóstico e outras tarefas de monitoramento.

A solução de monitoramento remoto implanta um hub IoT para fornecer um ponto de extremidade seguro para dispositivos enviarem telemetria. O Hub IoT também:

* Inclui um repositório de identidades de dispositivo para gerenciar os dispositivos permitidos para se conectarem à solução.
* Habilita a solução a enviar comandos para dispositivos. Por exemplo, para abrir uma válvula para liberar a pressão.
* Habilita gerenciamento de dispositivo em massa. Por exemplo, para atualizar o firmware em um conjunto de dispositivos.

### <a name="stream-processing"></a>Processamento de fluxo

Como a solução ingere os dados de telemetria, é importante entender como o fluxo de processamento de telemetria pode variar. Dependendo do cenário, os registros de dados podem fluir por meio de diferentes estágios:

* Armazenamento, como caches de memória, filas temporárias e arquivos permanentes.

* Análise, para executar telemetria de entrada por meio de um conjunto de condições e pode produzir registros de dados de saída diferente. Por exemplo, a telemetria de entrada codificada em Avro pode retornar a telemetria de saída codificada em JSON.

* Telemetria de entrada original e registros de saída de análise são normalmente armazenados e disponibilizados para exibição. A telemetria de entrada e registros de saída também podem disparar ações como e-mails, tíquetes de incidentes e comandos de dispositivo.

O roteamento pode expedir a telemetria para um ou mais pontos de extremidade de armazenamento, processos de análise e ações. Uma solução pode combinar os estágios em ordens diferentes e processá-los com tarefas simultâneas em paralelo.

A solução de Monitoramento Remoto usa o [Azure Stream Analytics](/azure/stream-analytics/) para o processamento de stream. O mecanismo de regras na solução usa consultas do Stream Analytics para gerar alertas e ações. Por exemplo, a solução pode usar uma consulta para identificar quando a temperatura média no compartimento de armazenamento de um caminhão em cinco minutos cai abaixo de 36 graus.

### <a name="storage"></a>Armazenamento

Soluções de IoT podem gerar grandes quantidades de dados, que costumam ser dados de série temporal. Esses dados precisam ser armazenados onde possam ser usados para visualização e relatórios. Uma solução também pode precisar acessar os dados mais tarde para análise ou processamento adicional. É comum ter dados divididos em armazenamentos de dados quentes e frios. O armazenamento de dados quentes mantém os dados recentes para acesso de baixa latência. O armazenamento de dados frios normalmente armazena dados históricos.

A solução de monitoramento remoto usa [Azure Time Series Insights](/azure/time-series-insights/) como seu armazenamento de dados quentes e o Cosmos DB como seu armazenamento de dados frios.

### <a name="ui-and-reporting-tools"></a>Interface do usuário e ferramentas de relatório

A solução de interface do usuário pode fornecer:

* Acesso e visualização dos resultados de análise e dados do dispositivo.
* Descoberta de dispositivos por meio do registro.
* Comando e controle de dispositivos.
* Fluxos de trabalho de provisionamento de dispositivos.
* Notificações e alertas.
* Integração com painéis dinâmicos e interativos para exibir dados de um grande número de dispositivos.  
* Localização geográfica e os serviços de reconhecimento geográfico.

A solução de monitoramento remoto inclui uma interface de usuário web para fornecer essa funcionalidade. A interface do usuário web inclui:

* Um mapa interativo para mostrar a localização de dispositivos.
* Acesso ao Gerenciador de Time Series Insights para consultar e analisar a telemetria.

### <a name="business-integration"></a>Integração de negócios

A camada de integração de negócios lida com a integração da solução IoT com sistemas de negócios, como CRM, ERP e aplicativos de linha de negócios. Os exemplos incluem faturamento de serviços, atendimento ao cliente e fornecimento de peças de reposição.

A solução de monitoramento remoto usa regras para enviar e-mails quando uma condição for atendida. Por exemplo, a solução pode notificar um operador quando a temperatura em um caminhão cai abaixo de 36 graus.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a arquitetura de referência do IoT do Azure e viu alguns exemplos de como o Acelerador de solução de monitoramento remoto segue suas recomendações. A próxima etapa é ler a [arquitetura de referência do Microsoft Azure IoT](https://aka.ms/iotrefarchitecture).