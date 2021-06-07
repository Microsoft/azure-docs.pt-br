---
title: O que é a arquitetura da solução baseada em agente
description: Saiba mais sobre o fluxo de informações e a arquitetura baseada em agente do Azure Defender para IoT.
ms.topic: overview
ms.date: 1/25/2021
ms.openlocfilehash: e08c3f151c3d3bc4fe08e61d960874b07f5b63e8
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383517"
---
# <a name="what-is-agent-based-solution-for-device-builders"></a>O que é a solução baseada em agente para construtores de dispositivos

Este artigo descreve a arquitetura do sistema funcional da solução baseada em agente do Defender para IoT. O Azure Defender para IoT oferece dois conjuntos de funcionalidades para atender às necessidades do seu ambiente: uma solução sem agente para organizações e uma solução baseada em agente para criadores de dispositivos.

## <a name="iot-hub-built-in-security"></a>Segurança interna do hub IoT

O Defender para IoT está habilitado por padrão em todos os Hubs IoT criados. O Defender para IoT fornece monitoramento, recomendações e alertas em tempo real, sem a necessidade de instalação do agente em nenhum dispositivo e usa análises avançadas em metadados do Hub IoT registrados para analisar e proteger seus dispositivos de campo e hubs IoT. 

## <a name="defender-for-iot-micro-agent"></a>Microagente do Defender para IoT 

O microagente do Defender para IoT fornece proteção de segurança aprofundada e visibilidade do comportamento do dispositivo. coleta, agrega e analisa eventos de segurança brutos dos seus dispositivos. Os eventos de segurança brutos podem incluir conexões IP, criação de processo, logons de usuário e outras informações relevantes de segurança. Os agentes de dispositivo do Defender para IoT também cuidam da agregação de eventos para ajudar a evitar a alta taxa de transferência de rede. Os agentes são altamente personalizáveis, permitindo que você os use para tarefas específicas, como enviar apenas informações importantes no SLA mais rápido, ou para agregar informações de segurança extensivas e contexto em segmentos maiores, evitando custos de serviço mais altos.

Os agentes de dispositivo e outros aplicativos usam o **SDK de envio de mensagens de segurança do Azure** para enviar informações de segurança para o Hub IoT do Azure. O hub IoT obtém essas informações e as encaminha para o serviço Defender para IoT.

Depois que o serviço do Defender para IoT é habilitado, além dos dados encaminhados, o hub IoT também envia todos os dados internos para análise pelo Defender para IoT. Esses dados incluem logs de operação do dispositivo/nuvem, identidades do dispositivo e configuração do hub. Todas essas informações ajudam a criar o pipeline de análise do Defender para IoT.

O pipeline de análise do Defender para IoT também recebe outros fluxos de inteligência contra ameaças de várias fontes da Microsoft e de parceiros da Microsoft. O pipeline de análise inteiro do Defender para IoT funciona com cada configuração de cliente feita no serviço (como alertas personalizados e uso do SDK de envio de mensagens de segurança).

Usando o pipeline de análise, o Defender para IoT combina todos os fluxos de informações para gerar alertas e recomendações acionáveis. O pipeline contém as regras personalizadas criadas por pesquisadores e especialistas em segurança, bem como modelos de machine learning procurando desvios do comportamento padrão do dispositivo e da análise de risco.

Os alertas e as recomendações do Defender para IoT (saída de pipeline de análise) são gravados no workspace do Log Analytics de cada cliente. A inclusão dos eventos brutos no workspace e dos alertas e das recomendações permite investigações e consultas aprofundadas usando os detalhes exatos das atividades suspeitas detectadas.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="A arquitetura do microagente.":::

## <a name="next-steps"></a>Próximas etapas

[Perguntas frequentes sobre o Defender para IoT](resources-frequently-asked-questions.md)

[Pré-requisitos do sistema](quickstart-system-prerequisites.md)
