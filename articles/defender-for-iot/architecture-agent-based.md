---
title: Arquitetura da solução baseada em agente
description: Saiba mais sobre o fluxo de informações e a arquitetura baseada no agente do Azure defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 1729a20825eb554a4fd2ee60141994e57e62f030
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809712"
---
# <a name="agent-based-solution-for-device-builders"></a>Solução baseada em agente para construtores de dispositivos

Este artigo descreve a arquitetura do sistema funcional da solução baseada no agente do defender para IoT. O Azure defender para IoT oferece dois conjuntos de recursos para atender às necessidades do seu ambiente, solução sem agente para organizações e solução baseada em agente para criadores de dispositivos.

## <a name="iot-hub-built-in-security"></a>Segurança interna do Hub IoT

O defender para IoT está habilitado por padrão em todos os novos Hub IoT criados. O defender para IoT fornece monitoramento, recomendações e alertas em tempo real, sem a necessidade de instalação do agente em nenhum dispositivo e usa análises avançadas em metadados do Hub IoT registrados para analisar e proteger seus dispositivos de campo e hubs IoT. 

## <a name="defender-for-iot-micro-agent"></a>Defender para IoT micro Agent 

O defender para IoT micro Agent fornece proteção de segurança aprofundada e visibilidade do comportamento do dispositivo. coleta, agrega e analisa eventos de segurança brutos de seus dispositivos. Os eventos de segurança brutos podem incluir conexões IP, criação de processo, logons de usuário e outras informações relevantes de segurança. Agentes de dispositivo do defender para IoT também gerencia a agregação de eventos para ajudar a evitar alta taxa de transferência de rede. Os agentes são altamente personalizáveis, permitindo que você os use para tarefas específicas, como enviar apenas informações importantes no SLA mais rápido ou para agregar informações de segurança extensivas e contexto em segmentos maiores, evitando custos de serviço mais altos.

Os agentes de dispositivo e outros aplicativos usam o **SDK de mensagens de segurança do Azure Send** para enviar informações de segurança para o Hub IOT do Azure. O Hub IoT obtém essas informações e as encaminha para o serviço defender para IoT.

Depois que o serviço do defender para IoT é habilitado, além dos dados encaminhados, o Hub IoT também envia todos os seus dados internos para análise pelo defender para IoT. Esses dados incluem logs de operação do dispositivo-nuvem, identidades do dispositivo e configuração do Hub. Todas essas informações ajudam a criar o pipeline do defender para análise de IoT.

O defender for IoT Analytics pipeline também recebe outros fluxos de inteligência contra ameaças de várias fontes dentro da Microsoft e de parceiros da Microsoft. O pipeline de análise inteiro do defender para IoT funciona com cada configuração de cliente feita no serviço (como alertas personalizados e o uso do SDK de envio de mensagem de segurança).

Usando o pipeline de análise, o defender para IoT combina todos os fluxos de informações para gerar alertas e recomendações acionáveis. O pipeline contém as regras personalizadas criadas por pesquisadores e especialistas de segurança, bem como modelos de aprendizado de máquina procurando desvios do comportamento padrão do dispositivo e da análise de riscos.

Os alertas e recomendações do defender for IoT (saída de pipeline de análise) são gravados no espaço de trabalho Log Analytics de cada cliente. Incluir os eventos brutos no espaço de trabalho e os alertas e as recomendações permite investigações e consultas aprofundadas usando os detalhes exatos das atividades suspeitas detectadas.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="A arquitetura do micro Agent.":::

## <a name="see-also"></a>Consulte também

[FAQ do defender for IoT](resources-frequently-asked-questions.md)

[Pré-requisitos do sistema](quickstart-system-prerequisites.md)
