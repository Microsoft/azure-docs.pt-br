---
title: Práticas recomendadas de configuração de dispositivo para o Azure IoT Hub | Microsoft Docs
description: Saiba mais sobre práticas recomendadas para configurar dispositivos IoT em escala
author: chrisgre
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: c97395981ea3af90c7b0c590cb049fccc7392304
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734823"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Práticas recomendadas para configuração do dispositivo dentro de uma solução de IoT

O gerenciamento automático do dispositivo no Hub IoT do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciamento de grande frotas de dispositivos sobre a totalidade de seu ciclo de vida. Este artigo define muitas das práticas recomendadas para várias funções envolvidas no desenvolvimento e operar uma solução de IoT.

* **Fabricante/integrador de hardware de IoT:** Fabricantes de hardware de IoT, os integradores montando hardware de vários fabricantes ou os fornecedores de hardware para uma implantação de IoT fabricado ou integrado por outros fornecedores. Envolvidas no desenvolvimento e a integração de firmware, sistemas operacionais incorporados e software incorporado.

* **Desenvolvedor de soluções de IoT:** O desenvolvimento de uma solução de IoT normalmente é feito por um desenvolvedor de soluções. Esse desenvolvedor pode ser parte de uma equipe interna ou um SI (integrador de sistema) especializado nesta atividade. O desenvolvedor da solução IoT pode desenvolver vários componentes da solução IoT a partir do zero, integrar vários componentes padrão ou de código aberto ou personalizar um [acelerador de solução da IoT](/azure/iot-accelerators/).

* **Operador de solução de IoT:** Depois que a solução de IoT é implantada, ele requer operações de longo prazo, monitoramento, atualizações e manutenção. Essas tarefas podem ser feitas por uma equipe interna composta por especialistas em tecnologia da informação, equipes de operações e manutenção de hardware e especialistas em domínio que monitoram o comportamento correto da infraestrutura geral de IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Compreender o gerenciamento de dispositivo automático para configurar dispositivos IoT em escala

O gerenciamento automático de dispositivos inclui os muitos benefícios de [gêmeos de dispositivos](iot-hub-devguide-device-twins.md) e [módulos de gêmeos](iot-hub-devguide-module-twins.md) para sincronizar estados desejados e relatados entre a nuvem e os dispositivos. [Configurações de dispositivo automático](iot-hub-auto-device-config.md) automaticamente atualizar conjuntos grandes de gêmeos e resumir o progresso e conformidade. As etapas de alto nível a seguir descrevem o gerenciamento de dispositivo como automático é desenvolvido e usado:

* O **fabricante / integrador de hardware da IoT** implementa recursos de gerenciamento de dispositivo em um aplicativo incorporado usando [gêmeos de dispositivo](iot-hub-devguide-device-twins.md). Esses recursos podem incluir a instalação de software, atualizações de firmware e atualização e gerenciamento de configurações.

* O **desenvolvedor de soluções de IoT** implementa a camada de gerenciamento de operações de gerenciamento de dispositivo usando [gêmeos](iot-hub-devguide-device-twins.md) e [configurações de dispositivo automático](iot-hub-auto-device-config.md). A solução deve incluir a definição de uma interface de operador para executar tarefas de gerenciamento de dispositivo.

* O **operador de solução de IoT** usa a solução de IoT para executar tarefas de gerenciamento de dispositivos, especialmente para agrupar dispositivos juntos, alterações de configuração, como atualizações de firmware de iniciar, monitorar o andamento e solucionar problemas surgem.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware de IoT

Seguem as práticas recomendadas para fabricantes de hardware e integradores de lidar com o desenvolvimento de software inserido:

* **Implemente [gêmeos](iot-hub-devguide-device-twins.md):** Dispositivos gêmeos permitem sincronizar configurações desejadas da nuvem e relatar a configuração atual e as propriedades do dispositivo. A melhor maneira de implementar gêmeos de dispositivos em aplicativos incorporados é por meio dos [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks). Dispositivos gêmeos são mais adequados para a configuração porque eles:

    * Suporte à comunicação bidirecional.
    * Permite que os dois estados de dispositivo conectado e desconectado.
    * Siga o princípio de consistência eventual.
    * São totalmente consultável na nuvem.

* **Estrutura do dispositivo gêmeo para gerenciamento de dispositivo:** O dispositivo gêmeo deve ser estruturado, de modo que as propriedades de gerenciamento de dispositivo são agrupadas logicamente em seções. Isso permitirá que as alterações de configuração sejam isoladas sem afetar outras seções do gêmeo. Por exemplo, crie uma seção nas propriedades desejadas para firmware, outra seção para software e uma terceira seção para configurações de rede. 

* **Atributos de dispositivo de relatório que são úteis para gerenciamento de dispositivo:** Atributos como um dispositivo físico marca e modelam, firmware, o sistema operacional, o número de série, e outros identificadores são úteis para relatórios e como parâmetros para direcionar as alterações de configuração.

* **Defina os estados principais para relatar o status e progresso:** Estados de nível superior devem ser enumerados para que eles podem ser relatados para o operador. Por exemplo, uma atualização de firmware deveria reportar status como Atual, Baixando, Aplicando, Em Progresso, e Erro. Defina campos adicionais para mais informação em cada estado.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções IoT

A seguir são melhores práticas para desenvolvedores de solução IoT que estão compilando sistemas baseados em Azure:

* **Implemente [gêmeos](iot-hub-devguide-device-twins.md):** Dispositivos gêmeos permitem sincronizar configurações desejadas da nuvem e relatar a configuração atual e as propriedades do dispositivo. A melhor maneira de implementar gêmeos de dispositivos em aplicativos de soluções em nuvem é por meio dos [SDKs do IoT do Azure](https://github.com/Azure/azure-iot-sdks). Dispositivos gêmeos são mais adequados para a configuração porque eles:

    * Suporte à comunicação bidirecional.
    * Permite que os dois estados de dispositivo conectado e desconectado.
    * Siga o princípio de consistência eventual.
    * São totalmente consultável na nuvem.

* **Organize dispositivos usando marcas do dispositivo gêmeo:** A solução deve permitir que o operador definir os anéis de qualidade ou outros conjuntos de dispositivos com base em várias estratégias de implantação, como canário. A organização do dispositivo pode ser implementada em sua solução usando tags de gêmeo de dispositivo e [consultas](iot-hub-devguide-query-language.md). Organização do dispositivo é necessária para permitir que as implantações de configuração com precisão e com segurança.

* **Implemente [configurações de dispositivo automático](iot-hub-auto-device-config.md):** Implantar configurações de dispositivo automático e alterações de configuração de monitor para conjuntos grandes de dispositivos IoT por meio de dispositivos gêmeos. As configurações automáticas de dispositivos segmentam conjuntos de dispositivos gêmeos por meio da **condição de destino** que é uma consulta em marcas de dispositivos gêmeos ou em propriedades relatadas. O **conteúdo alvo** é o conjunto de propriedades desejadas que serão configuradas dentro do dispositivo gêmeo alvo. O conteúdo alvo deve alinhar com a estrutura do dispositivo gêmeo definida pelo fabricante/integrador de hardware IoT.

   As **métricas** são consultas em propriedades relatadas de dispositivos gêmeos e também devem ser alinhadas com a estrutura de dispositivo gêmeo definida pelo fabricante/integrador de hardware de IoT. As configurações automáticas de dispositivos também têm o benefício do IoT Hub executar operações de gêmeos de dispositivo a uma taxa que nunca excederá os [limites de aceleração](iot-hub-devguide-quotas-throttling.md) para leituras e atualizações de gêmeos de dispositivos.

* **Use o [serviço de provisionamento de dispositivo](../iot-dps/how-to-manage-enrollments.md):** Os desenvolvedores de solução devem usar o serviço de provisionamento de dispositivos para atribuir marcas do dispositivo gêmeo para novos dispositivos, de modo que eles serão configurados automaticamente pelo **configurações de dispositivo automático** que são destinadas a gêmeos com essa marca. 

## <a name="iot-solution-operator"></a>Operador de solução IoT

A seguir são melhores práticas para operadores de solução IoT que usam uma compilação de solução de IoT no Azure:

* **Organize dispositivos de gerenciamento:** A solução de IoT deve definir ou para permitir a criação de anéis de qualidade ou outros conjuntos de dispositivos com base em várias estratégias de implantação, como canário. Os conjuntos de dispositivos serão usados para implementar alterações de configuração e executar outras operações de gerenciamento de dispositivos em escala.

* **Execute as alterações de configuração usando uma distribuição em fases:**  Fases é um processo geral no qual um operador implanta alterações em um conjunto maior de dispositivos IoT. O objetivo é fazer alterações gradualmente para reduzir o risco de fazer alterações significativas em larga escala.  O operador deve usar a interface da solução para criar uma [configuração de dispositivo automática](iot-hub-auto-device-config.md) e a condição de segmentação deve segmentar um conjunto inicial de dispositivos (como um grupo canário). O operador deve então validar a mudança de configuração no conjunto inicial de dispositivos.

   Uma vez que a validação esteja completa, o operador atualizará a configuração de dispositivos automáticos para incluir um conjunto maior de dispositivos. O operador também deve definir a prioridade para que a configuração seja maior do que as outras configurações atualmente destinadas a esses dispositivos. A implementação pode ser monitorada usando as métricas relatadas pela configuração automática do dispositivo.

* **Execute as reversões em caso de erros ou configurações incorretas:**  Uma configuração de dispositivo automático que causa erros ou configurações incorretas pode ser revertida alterando a **condição de destino** para que os dispositivos não atendam à condição de destino. Garanta que outra configuração de dispositivo automático de baixa prioridade ainda está destinada a esses dispositivos. Verifique se que a reversão teve êxito exibindo as métricas: A configuração revertidas não deve mostrar o status para dispositivos visados e métricas da segunda configuração agora devem incluir contagens para os dispositivos que ainda são direcionadas.

## <a name="next-steps"></a>Próximas etapas

* Aprenda sobre como implementar gêmeos de dispositivos em [Entender e usar gêmeos de dispositivos no Hub IoT](iot-hub-devguide-device-twins.md).

* Siga as etapas para criar, atualizar ou excluir uma configuração de dispositivo automático na [configurar e monitorar dispositivos de IoT em escala](iot-hub-auto-device-config.md).

* Implementar um padrão de atualização de firmware usando dispositivos gêmeos e configurações de dispositivo automático em [Tutorial: Implementar um processo de atualização de firmware do dispositivo](tutorial-firmware-update.md).
