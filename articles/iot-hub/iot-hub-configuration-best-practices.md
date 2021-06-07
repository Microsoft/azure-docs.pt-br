---
title: Práticas recomendadas de configuração de dispositivo para o Azure IoT Hub | Microsoft Docs
description: Saiba mais sobre as práticas recomendadas para usar o gerenciamento automático de dispositivos para minimizar tarefas repetitivas e complexas envolvidas no gerenciamento de dispositivos IoT em escala.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 8a39c2b06ca8a0f852891acb60ba199fc2c6db5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96024122"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Práticas recomendadas para configuração do dispositivo dentro de uma solução de IoT

O gerenciamento automático do dispositivo no Hub IoT do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciamento de grande frotas de dispositivos sobre a totalidade de seu ciclo de vida. Este artigo define muitas das práticas recomendadas para várias funções envolvidas no desenvolvimento e operar uma solução de IoT.

* **Fabricante/integrador de hardware de IoT**: Fabricantes de hardware de IoT, os integradores montando hardware de vários fabricantes ou os fornecedores de hardware para uma implantação de IoT fabricado ou integrado por outros fornecedores. Envolvidas no desenvolvimento e a integração de firmware, sistemas operacionais incorporados e software incorporado.

* **Desenvolvedor de soluções de IOT:** O desenvolvimento de uma solução de IoT normalmente é feito por um desenvolvedor de soluções. Esse desenvolvedor pode ser parte de uma equipe interna ou um SI (integrador de sistema) especializado nesta atividade. O desenvolvedor da solução IoT pode desenvolver vários componentes da solução IoT a partir do zero, integrar vários componentes padrão ou de código aberto ou personalizar um [acelerador de solução da IoT](../iot-accelerators/index.yml).

* **Operador de solução de IOT:** Depois que a solução de IoT é implantada, ela requer operações de longo prazo, monitoramento, atualizações e manutenção. Essas tarefas podem ser feitas por uma equipe interna composta por especialistas em tecnologia da informação, equipes de operações e manutenção de hardware e especialistas em domínio que monitoram o comportamento correto da infraestrutura geral de IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Compreender o gerenciamento de dispositivo automático para configurar dispositivos IoT em escala

O gerenciamento automático de dispositivos inclui os muitos benefícios de [gêmeos de dispositivos](iot-hub-devguide-device-twins.md) e [módulos de gêmeos](iot-hub-devguide-module-twins.md) para sincronizar estados desejados e relatados entre a nuvem e os dispositivos. [As configurações automáticas de dispositivo](./iot-hub-automatic-device-management.md) atualizam automaticamente grandes conjuntos de gêmeos e resumem o progresso e a conformidade. As etapas de alto nível a seguir descrevem o gerenciamento de dispositivo como automático é desenvolvido e usado:

* O **fabricante / integrador de hardware da IoT** implementa recursos de gerenciamento de dispositivo em um aplicativo incorporado usando [gêmeos de dispositivo](iot-hub-devguide-device-twins.md). Esses recursos podem incluir a instalação de software, atualizações de firmware e atualização e gerenciamento de configurações.

* O **desenvolvedor de soluções de IoT** implementa a camada de gerenciamento de operações de gerenciamento de dispositivo usando [gêmeos](iot-hub-devguide-device-twins.md) e [configurações de dispositivo automático](./iot-hub-automatic-device-management.md). A solução deve incluir a definição de uma interface de operador para executar tarefas de gerenciamento de dispositivo.

* O **operador de solução de IoT** usa a solução de IoT para executar tarefas de gerenciamento de dispositivos, especialmente para agrupar dispositivos juntos, alterações de configuração, como atualizações de firmware de iniciar, monitorar o andamento e solucionar problemas surgem.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware de IoT

Seguem as práticas recomendadas para fabricantes de hardware e integradores de lidar com o desenvolvimento de software inserido:

* **Implemente [gêmeos de dispositivos](iot-hub-devguide-device-twins.md):** Os gêmeos de dispositivos permitem sincronizar a configuração desejada da nuvem e informar a configuração atual e as propriedades do dispositivo. A melhor maneira de implementar gêmeos de dispositivos em aplicativos incorporados é por meio dos [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks). Dispositivos gêmeos são mais adequados para a configuração porque eles:

    * Suporte à comunicação bidirecional.
    * Permite que os dois estados de dispositivo conectado e desconectado.
    * Siga o princípio de consistência eventual.
    * São totalmente consultável na nuvem.

* **Estrutura o dispositivo gêmeo para gerenciamento de dispositivo:** o dispositivo gêmeo devem ser estruturadas de modo que as propriedades de gerenciamento de dispositivo são agrupadas logicamente em seções. Isso permitirá que as alterações de configuração sejam isoladas sem afetar outras seções do gêmeo. Por exemplo, crie uma seção nas propriedades desejadas para firmware, outra seção para software e uma terceira seção para configurações de rede. 

* **Relate os atributos do dispositivo que são úteis para o gerenciamento de dispositivos:** Atributos como marca e modelo de dispositivos físicos, firmware, sistema operacional, número de série e outros identificadores são úteis para relatórios e como parâmetros para segmentar alterações de configuração.

* **Defina os estados principais para relatar status e progresso:** os estados de nível superior devem ser enumerados para que possam ser relatados ao operador. Por exemplo, uma atualização de firmware deveria reportar status como Atual, Baixando, Aplicando, Em Progresso, e Erro. Defina campos adicionais para mais informação em cada estado.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções IoT

A seguir são melhores práticas para desenvolvedores de solução IoT que estão compilando sistemas baseados em Azure:

* **Implemente [gêmeos de dispositivos](iot-hub-devguide-device-twins.md):** Os gêmeos de dispositivos permitem sincronizar a configuração desejada da nuvem e informar a configuração atual e as propriedades do dispositivo. A melhor maneira de implementar gêmeos de dispositivos em aplicativos de soluções em nuvem é por meio dos [SDKs do IoT do Azure](https://github.com/Azure/azure-iot-sdks). Dispositivos gêmeos são mais adequados para a configuração porque eles:

    * Suporte à comunicação bidirecional.
    * Permite que os dois estados de dispositivo conectado e desconectado.
    * Siga o princípio de consistência eventual.
    * São totalmente consultável na nuvem.

* **Organizar usando dispositivo duas marcas de dispositivos:** a solução deve permitir que o operador definir os anéis de qualidade ou outros conjuntos de dispositivos com base em várias estratégias de implantação, como canary. A organização do dispositivo pode ser implementada em sua solução usando tags de gêmeo de dispositivo e [consultas](iot-hub-devguide-query-language.md). Organização do dispositivo é necessária para permitir que as implantações de configuração com precisão e com segurança.

* **Implemente [configurações automáticas de dispositivos](./iot-hub-automatic-device-management.md):** configurações automáticas de dispositivos implantam e monitoram alterações de configuração em grandes conjuntos de dispositivos IoT por meio de gêmeos de dispositivos.

   As configurações automáticas de dispositivos segmentam conjuntos de dispositivos gêmeos por meio da **condição de destino** que é uma consulta em marcas de dispositivos gêmeos ou em propriedades relatadas. O **conteúdo alvo** é o conjunto de propriedades desejadas que serão configuradas dentro do dispositivo gêmeo alvo. O conteúdo alvo deve alinhar com a estrutura do dispositivo gêmeo definida pelo fabricante/integrador de hardware IoT. As **métricas** são consultas em Propriedades relatadas do dispositivo e também devem ser alinhadas com a estrutura de entrelaçamento do dispositivo definida pelo fabricante/integrador de hardware de IOT.

   As configurações automáticas do dispositivo são executadas pela primeira vez logo após a configuração ser criada e, em seguida, em intervalos de cinco minutos. Eles também se beneficiam do Hub IoT que executa operações de dispositivos de velocidade de dispositivo a uma taxa que nunca excederá os [limites de limitação](iot-hub-devguide-quotas-throttling.md) para leituras e atualizações do dispositivo.

* **Use o [ Serviço de provisionamento de dispositivos](../iot-dps/how-to-manage-enrollments.md):** desenvolvedores de soluções devem usar o Serviço de provisionamento de dispositivos para atribuir tags de gêmeos a novos dispositivos, de forma que eles sejam configurados automaticamente por **configurações automáticas de dispositivo** que são direcionados a gêmeos com essa tag. 

## <a name="iot-solution-operator"></a>Operador de solução IoT

A seguir são melhores práticas para operadores de solução IoT que usam uma compilação de solução de IoT no Azure:

* **Organizar dispositivos para gerenciamento :** a solução deve permitir ou definir os anéis de qualidade ou outros conjuntos de dispositivos com base em várias estratégias de implantação, como canary. Os conjuntos de dispositivos serão usados para implementar alterações de configuração e executar outras operações de gerenciamento de dispositivos em escala.

* **Realize mudanças de configuração usando um lançamento em fases:** Um lançamento em etapas é um processo geral pelo qual um operador implementa alterações em um conjunto ampliado de dispositivos de IoT. O objetivo é fazer alterações gradualmente para reduzir o risco de fazer alterações significativas em larga escala.  O operador deve usar a interface da solução para criar uma [configuração de dispositivo automática](./iot-hub-automatic-device-management.md) e a condição de segmentação deve segmentar um conjunto inicial de dispositivos (como um grupo canário). O operador deve então validar a mudança de configuração no conjunto inicial de dispositivos.

   Uma vez que a validação esteja completa, o operador atualizará a configuração de dispositivos automáticos para incluir um conjunto maior de dispositivos. O operador também deve definir a prioridade para que a configuração seja maior do que as outras configurações atualmente destinadas a esses dispositivos. A implementação pode ser monitorada usando as métricas relatadas pela configuração automática do dispositivo.

* **Execute reversões em caso de erros ou configurações incorretas:** uma configuração automática de dispositivo que cause erros ou configurações incorretas pode ser revertida alterando a **condição de segmentação** para que os dispositivos não atendam mais à condição de segmentação. Garanta que outra configuração de dispositivo automático de baixa prioridade ainda está destinada a esses dispositivos. Verifique se a reversão foi bem-sucedida visualizando as métricas: a configuração revertida não deve mais mostrar o status de dispositivos não segmentados, e as métricas da segunda configuração agora devem incluir contagens para os dispositivos que ainda são segmentados.

## <a name="next-steps"></a>Próximas etapas

* Aprenda sobre como implementar gêmeos de dispositivos em [Entender e usar gêmeos de dispositivos no Hub IoT](iot-hub-devguide-device-twins.md).

* Siga as etapas para criar, atualizar ou excluir uma configuração de dispositivo automático na [configurar e monitorar dispositivos de IoT em escala](./iot-hub-automatic-device-management.md).

* Implementar um padrão de atualização de firmware usando dispositivos gêmeos e configurações de dispositivo automático na [Tutorial: implementar um processo de atualização de firmware do dispositivo](tutorial-firmware-update.md).