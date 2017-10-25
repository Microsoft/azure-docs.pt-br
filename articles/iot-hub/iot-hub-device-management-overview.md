---
title: "Visão geral do gerenciamento do Hub IoT do Azure | Microsoft Docs"
description: "Visão geral do gerenciamento de dispositivos no Hub IoT do Azure: ciclo de vida do dispositivo de empresa e padrões de gerenciamento do dispositivo como a reinicialização, redefinição de fábrica, atualização de firmware, configuração, gêmeos de dispositivo, consultas, trabalhos."
services: iot-hub
documentationcenter: 
author: bzurcher
manager: timlt
editor: 
ms.assetid: a367e715-55f6-4593-bd68-7863cbf0eb81
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: 6d667d42bfef2ec61b055009210d5621f51c17df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-device-management-with-iot-hub"></a>Visão geral do gerenciamento de dispositivos com o Hub IoT
## <a name="introduction"></a>Introdução
O Hub IoT do Azure fornece os recursos e um modelo de extensibilidade que habilitam desenvolvedores de dispositivos e back-end a criar soluções robustas de gerenciamento de dispositivos. Os dispositivos variam de sensores restritos e microcontroladores de finalidade única a gateways poderosos para encaminhar comunicações para grupos de dispositivos.  Além disso, os casos de uso e requisitos para operadores de IoT variam significativamente em vários setores.  Apesar dessa variação, o gerenciamento de dispositivos com o Hub IoT fornece recursos, padrões e bibliotecas de código para atender a um conjunto diversificado de dispositivos e usuários finais.

Uma parte crucial da criação de uma solução IoT empresarial bem-sucedida é fornecer uma estratégia para os operadores lidarem com o gerenciamento contínuo de sua coleção de dispositivos. Os operadores de IoT precisam de ferramentas simples e confiáveis e aplicativos que permitam focar nos aspectos mais estratégicos de seus trabalhos. Esse artigo fornece:

* Uma breve visão geral da abordagem de Hub IoT do Azure para gerenciamento de dispositivos.
* Uma descrição dos princípios de gerenciamento de dispositivo comuns.
* Uma descrição do ciclo de vida do dispositivo.
* Uma visão geral dos padrões comuns de gerenciamento de dispositivo.

## <a name="device-management-principles"></a>Princípios de gerenciamento de dispositivos
A IoT traz um conjunto exclusivo de desafios de gerenciamento de dispositivo, e cada solução corporativa deve resolver os seguintes princípios:

![Gráfico dos princípios de gerenciamento de dispositivos][img-dm_principles]

* **Escala e automação**: soluções de IoT exigem ferramentas simples que podem automatizar tarefas de rotina e capacitar uma equipe de operações relativamente pequena a gerenciar milhões de dispositivos. Diariamente, os operadores esperam lidar com operações de dispositivo remotamente, em massa, e só receberem alertas quando surgirem problemas que exijam sua atenção direta.
* **Abertura e compatibilidade**: o ecossistema de dispositivos é incrivelmente diverso. Ferramentas de gerenciamento devem ser personalizadas a fim de acomodar vários protocolos, classes e plataformas de dispositivos. Os operadores devem ser capazes de dar suporte a muitos tipos de dispositivos, desde os chips de processo único incorporados mais restritos, até computadores eficientes e totalmente funcionais.
* **Reconhecimento de contexto**: os ambientes de IoT são dinâmicos e estão em constante mudança. A confiabilidade do serviço é fundamental. As operações de gerenciamento de dispositivos devem levar em consideração os seguintes fatores para garantir que esse tempo de inatividade de manutenção não afete as operações de negócios críticos ou crie condições perigosas:
    * Janelas de manutenção do SLA
    * Estados de energia e de rede
    * Condições de uso
    * Localização geográfica do dispositivo
* **Atende muitas funções**: o suporte aos fluxos de trabalho e processos exclusivos das funções de operações do IoT é crucial. A equipe de operações deve trabalhar de forma harmoniosa com as restrições de determinado departamentos de TI internos.  Também deve encontrar formas sustentáveis de detectar informações de operações de dispositivo em tempo real para supervisores e outras funções de gerente de negócios.

## <a name="device-lifecycle"></a>Ciclo de vida do dispositivo
Há um conjunto de estágios de gerenciamento geral de dispositivos que são comuns a todos os projetos de IoT empresariais. Na IoT do Azure, há cinco fases no ciclo de vida do dispositivo:

![As cinco fases do ciclo de vida de dispositivo IoT do Azure: planejar, provisionar, configurar, monitorar, desativar][img-device_lifecycle]

Em cada um desses cinco estágios, existem vários requisitos de operador de dispositivo que devem ser atendidos para fornecer uma solução completa:

* **Planejamento**: permita que os operadores criem um esquema de metadados de dispositivo que os possibilite consultar de forma fácil e precisa e selecionar um grupo de dispositivos para operações de gerenciamento em massa. Você pode usar o gêmeo de dispositivo para armazenar metadados de dispositivo na forma de marcas e propriedades.
  
    *Leitura adicional*: [Introdução aos gêmeos de dispositivos][lnk-twins-getstarted], [Entender os gêmeos de dispositivos][lnk-twins-devguide], [Como usar as propriedades gêmeo de dispositivo][lnk-twin-properties].
* **Provisionar**: provisionar novos dispositivos para o Hub IoT e habilitar operadores descobrir imediatamente os recursos do dispositivo com segurança.  Use o Registro de identidade do Hub IoT para criar as credenciais e identidades flexíveis de dispositivos e executar essa operação em massa usando um trabalho. Crie dispositivos para relatar seus recursos e condições por meio de propriedades do dispositivo no twin de dispositivo.
  
    *Leitura adicional*: [Gerenciar identidades de dispositivo][lnk-identity-registry], [Gerenciamento em massa de identidades de dispositivo][lnk-bulk-identity], [Como usar as propriedades gêmeo de dispositivo][lnk-twin-properties].
* **Configurar**: facilite as alterações de configuração em massa e atualizações de firmware em dispositivos enquanto mantém a integridade e a segurança. Execute essas operações de gerenciamento de dispositivo em massa usando propriedades desejadas ou com trabalhos de difusão e métodos diretos.
  
    *Leitura adicional*: [Usar métodos diretos][lnk-c2d-methods], [Invocar um método direto em um dispositivo][lnk-methods-devguide], [Como usar propriedades gêmeo de dispositivo][lnk-twin-properties], [Agendar e difundir trabalhos][lnk-jobs], [Agendar trabalhos em vários dispositivos][lnk-jobs-devguide].
* **Monitor**: monitorar a integridade geral da coleção de dispositivos, o status das operações em andamento e alertar os operadores para problemas que podem exigir a atenção.  Aplique o twin de dispositivo para permitir que dispositivos relatem condições operacionais em tempo real e o status de operações de atualização. Crie poderosos relatórios de painel que indicam os problemas mais imediatos por meio de consultas ao twin de dispositivo.
  
    *Leitura adicional*: [como usar as propriedades do dispositivo gêmeo][lnk-twin-properties], [Linguagem de consulta de Hub IoT para dispositivo gêmeo, trabalhos e roteamento de mensagens][lnk-query-language].
* **Desativação**: substitua ou encerre os dispositivos após uma falha, ciclo de atualização ou ao final da vida útil do serviço.  Use o gêmeo de dispositivo para manter informações sobre o dispositivo, se o dispositivo físico está sendo substituído ou arquivado ou se está sendo desativado. Use o Registro do Hub IoT para revogar com segurança as credenciais e identidades de dispositivos.
  
    *Leitura adicional*: [Como usar as propriedades de dispositivo gêmeo][lnk-twin-properties], [Gerenciar identidades de dispositivo][lnk-identity-registry].

## <a name="device-management-patterns"></a>Padrões de gerenciamento de dispositivos
O Hub IoT habilita o seguinte conjunto de padrões de gerenciamento de dispositivo.  Os [tutoriais de gerenciamento de dispositivo][lnk-get-started] mostram mais detalhadamente como estender esses padrões para ajustar seu cenário exato e como criar novos padrões com base nesses modelos de núcleo.

* **Reinicializar** - o aplicativo de back-end informa ao dispositivo por meio de um método direto que ele iniciou uma reinicialização.  O dispositivo usa as propriedades reportadas para atualizar o status de reinicialização do dispositivo.
  
    ![Gráfico de padrão de reinicialização de gerenciamento de dispositivos][img-reboot_pattern]
* **Redefinição de fábrica** - o aplicativo de back-end informa ao dispositivo por meio de um método direto que ele iniciou uma redefinição de fábrica.  O dispositivo usa as propriedades reportadas para atualizar o status de reinicialização de fábrica do dispositivo.
  
    ![Gráfico de padrão de redefinição de fábrica de gerenciamento de dispositivos][img-facreset_pattern]
* **Configuração** – o aplicativo de back-end usa as propriedades desejadas para configurar o software em execução no dispositivo.  O dispositivo usa as propriedades reportadas para atualizar o status de configuração do dispositivo.
  
    ![Gráfico de padrão de configuração de gerenciamento de dispositivos][img-config_pattern]
* **Atualização de firmware** - o aplicativo de back-end informa ao dispositivo por meio de um método direto que ele iniciou uma atualização de firmware.  O dispositivo inicia um processo de várias etapas para baixar a imagem do firmware, aplicar a imagem do firmware e finalmente se reconectar ao serviço do Hub IoT.  Durante o processo com várias etapas, o dispositivo usa as propriedades reportadas para atualizar o progresso e o status do dispositivo.
  
    ![Gráfico de padrão de atualização de firmware de gerenciamento de dispositivos][img-fwupdate_pattern]
* **Relatório de progresso e status** - o back-end da solução executa consultas do dispositivo gêmeo, em um conjunto de dispositivos, para reportar o status e o progresso das ações em execução no dispositivo.
  
    ![Gráfico de padrão de status e progresso de relatório de gerenciamento de dispositivos][img-report_progress_pattern]

## <a name="next-steps"></a>Próximas etapas
As funcionalidades, os padrões e as bibliotecas de código que o gerenciamento de dispositivos do Hub IoT fornece para gerenciamento de dispositivos, permitem que você crie aplicativos IoT que atendam aos requisitos corporativos de operador IoT em cada estágio do ciclo de vida do dispositivo.

Para continuar a aprender sobre as funcionalidades de gerenciamento de dispositivos no Hub IoT, confira o tutorial [Introdução ao gerenciamento de dispositivos][lnk-get-started].

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-node-node-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md
