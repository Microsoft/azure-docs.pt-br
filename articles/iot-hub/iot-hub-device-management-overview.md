---
title: Visão geral do gerenciamento de dispositivos com o Hub IoT do Azure | Microsoft Docs
description: Visão geral do gerenciamento de dispositivos no Hub IoT do Azure ‒ ciclo de vida do dispositivo de empresa e padrões de gerenciamento do dispositivo como a reinicialização, redefinição de fábrica, atualização de firmware, configuração, gêmeos de dispositivo, consultas, trabalhos.
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 9ff8ebeaf7c4a219bc403c81dfea62f4657a1fd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148342"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Visão geral do gerenciamento de dispositivos com o Hub IoT

O Hub IoT do Azure fornece os recursos e um modelo de extensibilidade que habilitam desenvolvedores de dispositivos e back-end a criar soluções robustas de gerenciamento de dispositivos. Os dispositivos variam de sensores restritos e microcontroladores de finalidade única a gateways poderosos para encaminhar comunicações para grupos de dispositivos.  Além disso, os casos de uso e requisitos para operadores de IoT variam significativamente em vários setores.  Apesar dessa variação, o gerenciamento de dispositivos com o Hub IoT fornece recursos, padrões e bibliotecas de código para atender a um conjunto diversificado de dispositivos e usuários finais.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Uma parte crucial da criação de uma solução IoT empresarial bem-sucedida é fornecer uma estratégia para os operadores lidarem com o gerenciamento contínuo de sua coleção de dispositivos. Os operadores de IoT precisam de ferramentas simples e confiáveis e aplicativos que permitam focar nos aspectos mais estratégicos de seus trabalhos. Esse artigo fornece:

* Uma breve visão geral da abordagem de Hub IoT do Azure para gerenciamento de dispositivos.
* Uma descrição dos princípios de gerenciamento de dispositivo comuns.
* Uma descrição do ciclo de vida do dispositivo.
* Uma visão geral dos padrões comuns de gerenciamento de dispositivo.

## <a name="device-management-principles"></a>Princípios de gerenciamento de dispositivos

A IoT traz um conjunto exclusivo de desafios de gerenciamento de dispositivo, e cada solução corporativa deve resolver os seguintes princípios:

![Gráfico dos princípios de gerenciamento de dispositivos](media/iot-hub-device-management-overview/image4.png)

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

![As cinco fases do ciclo de vida de dispositivo IoT do Azure: planejar, provisionar, configurar, monitorar, desativar](./media/iot-hub-device-management-overview/image5.png)

Em cada um desses cinco estágios, existem vários requisitos de operador de dispositivo que devem ser atendidos para fornecer uma solução completa:

* **Planejamento**: permita que os operadores criem um esquema de metadados de dispositivo que os possibilite consultar de forma fácil e precisa e selecionar um grupo de dispositivos para operações de gerenciamento em massa. Você pode usar o gêmeo de dispositivo para armazenar metadados de dispositivo na forma de marcas e propriedades.
  
    *Leitura adicional*: 
    * [Introdução a dispositivos gêmeos](iot-hub-node-node-twin-getstarted.md)
    * [Entender os dispositivos gêmeos](iot-hub-devguide-device-twins.md)
    * [Como usar as propriedades do dispositivo gêmeo](tutorial-device-twins.md)
    * [Práticas recomendadas para configuração do dispositivo em uma solução de IoT](iot-hub-configuration-best-practices.md)

* **Provisionar**: provisionar novos dispositivos para o Hub IoT e habilitar operadores descobrir imediatamente os recursos do dispositivo com segurança.  Use o Registro de identidade do Hub IoT para criar as credenciais e identidades flexíveis de dispositivos e executar essa operação em massa usando um trabalho. Crie dispositivos para relatar seus recursos e condições por meio de propriedades do dispositivo no twin de dispositivo.
  
    *Leitura adicional*: 
    * [Gerenciar identidades do dispositivo](iot-hub-devguide-identity-registry.md)
    * [Gerenciamento em massa de identidades de dispositivo](iot-hub-bulk-identity-mgmt.md)
    * [Como usar as propriedades do dispositivo gêmeo](tutorial-device-twins.md)
    * [Práticas recomendadas para configuração do dispositivo em uma solução de IoT](iot-hub-configuration-best-practices.md)
    * [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](https://azure.microsoft.com/documentation/services/iot-dps)

* **Configurar**: facilite as alterações de configuração em massa e atualizações de firmware em dispositivos enquanto mantém a integridade e a segurança. Execute essas operações de gerenciamento de dispositivo em massa usando propriedades desejadas ou com trabalhos de difusão e métodos diretos.
  
    *Leitura adicional*:
    * [Como usar as propriedades do dispositivo gêmeo](tutorial-device-twins.md)
    * [Configurar e monitorar dispositivos IoT em escala](./iot-hub-automatic-device-management.md)
    * [Práticas recomendadas para configuração do dispositivo em uma solução de IoT](iot-hub-configuration-best-practices.md)

* **Monitor**: monitorar a integridade geral da coleção de dispositivos, o status das operações em andamento e alertar os operadores para problemas que podem exigir a atenção.  Aplique o twin de dispositivo para permitir que dispositivos relatem condições operacionais em tempo real e o status de operações de atualização. Crie poderosos relatórios de painel que indicam os problemas mais imediatos por meio de consultas ao twin de dispositivo.
  
    *Leitura adicional*: 
    * [Como usar as propriedades do dispositivo gêmeo](tutorial-device-twins.md)
    * [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md)
    * [Configurar e monitorar dispositivos IoT em escala](./iot-hub-automatic-device-management.md)
    * [Práticas recomendadas para configuração do dispositivo em uma solução de IoT](iot-hub-configuration-best-practices.md)

* **Desativação**: substitua ou encerre os dispositivos após uma falha, ciclo de atualização ou ao final da vida útil do serviço.  Use o gêmeo de dispositivo para manter informações sobre o dispositivo, se o dispositivo físico está sendo substituído ou arquivado ou se está sendo desativado. Use o Registro do Hub IoT para revogar com segurança as credenciais e identidades de dispositivos.
  
    *Leitura adicional*: 
    * [Como usar as propriedades do dispositivo gêmeo](tutorial-device-twins.md)
    * [Gerenciar identidades do dispositivo](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Padrões de gerenciamento de dispositivos

O Hub IoT habilita o seguinte conjunto de padrões de gerenciamento de dispositivo. Os [tutoriais de gerenciamento de dispositivo](iot-hub-node-node-device-management-get-started.md) mostram mais detalhadamente como estender esses padrões para ajustar seu cenário exato e como criar novos padrões com base nesses modelos de núcleo.

* **Reinicializar**: o aplicativo de back-end informa ao dispositivo por meio de um método direto que ele iniciou uma reinicialização.  O dispositivo usa as propriedades reportadas para atualizar o status de reinicialização do dispositivo.
  
    ![Gráfico de padrão de reinicialização de gerenciamento de dispositivos](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Redefinição de fábrica**: o aplicativo de back-end informa ao dispositivo por meio de um método direto que ele iniciou uma redefinição de fábrica. O dispositivo usa as propriedades reportadas para atualizar o status de reinicialização de fábrica do dispositivo.
  
    ![Gráfico de padrão de redefinição de fábrica de gerenciamento de dispositivos](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Configuração**: o aplicativo de back-end usa as propriedades desejadas para configurar o software em execução no dispositivo. O dispositivo usa as propriedades reportadas para atualizar o status de configuração do dispositivo.
  
    ![Gráfico de padrão de configuração de gerenciamento de dispositivos](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Atualização de firmware**: o aplicativo de back-end usa uma configuração de gerenciamento de dispositivo automático para selecionar os dispositivos para receber a atualização, para informar os dispositivos onde localizar a atualização e para monitorar o processo de atualização. O dispositivo inicia um processo de várias etapas para baixar, verificar e aplicar a imagem do firmware e então dar reboot no dispositivo antes de se reconectar ao serviço do Hub IoT. Durante o processo com várias etapas, o dispositivo usa as propriedades reportadas para atualizar o progresso e o status do dispositivo.
  
    ![Gráfico de padrão de atualização de firmware de gerenciamento de dispositivos](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Relatório de progresso e status**: o back-end da solução executa consultas do dispositivo gêmeo, em um conjunto de dispositivos, para reportar o status e o progresso das ações em execução no dispositivo.
  
    ![Grafo de padrão de status e progresso de relatório de gerenciamento de dispositivos](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Próximas etapas

As funcionalidades, os padrões e as bibliotecas de código que o gerenciamento de dispositivos do Hub IoT fornece para gerenciamento de dispositivos, permitem que você crie aplicativos IoT que atendam aos requisitos corporativos de operador IoT em cada estágio do ciclo de vida do dispositivo.

Para continuar a aprender sobre as funcionalidades de gerenciamento de dispositivos no Hub IoT, confira o tutorial [Introdução ao gerenciamento de dispositivos](iot-hub-node-node-device-management-get-started.md).