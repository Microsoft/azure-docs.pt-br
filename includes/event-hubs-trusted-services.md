---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 743d7d46474b4ba55df50398f29cbdb964b5ff08
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978869"
---
## <a name="trusted-microsoft-services"></a>Serviços Microsoft confiáveis
Quando você habilita a opção **permitir que os serviços confiáveis da Microsoft ignorem essa** configuração de firewall, os serviços a seguir recebem acesso aos seus recursos de hubs de eventos.

| Serviço confiável | Cenários de uso com suporte | 
| --------------- | ------------------------- | 
| Grade de Eventos do Azure | Permite que a grade de eventos do Azure envie eventos para os hubs de eventos em seu namespace de hubs de eventos. Você também precisa executar as seguintes etapas: <ul><li>Habilitar a identidade atribuída pelo sistema para um tópico ou um domínio</li><li>Adicionar a identidade à função de remetente de dados dos hubs de eventos do Azure no namespace de hubs de eventos</li><li>Em seguida, configure a assinatura de evento que usa um hub de eventos como um ponto de extremidade para usar a identidade atribuída pelo sistema.</li></ul> <p>Para obter mais informações, consulte [entrega de eventos com uma identidade gerenciada](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (configurações de diagnóstico) | Permite que Azure Monitor envie informações de diagnóstico para os hubs de eventos em seu namespace de hubs de eventos. Azure Monitor pode ler do hub de eventos e também gravar dados no Hub de eventos. |
| Stream Analytics do Azure | Permite que um trabalho de Azure Stream Analytics leia dados de ([entrada](../articles/stream-analytics/stream-analytics-add-inputs.md)) ou grave dados em hubs de eventos ([saída](../articles/stream-analytics/event-hubs-output.md)) em seu namespace de hubs de eventos. <p>**Importante**: o trabalho de Stream Analytics deve ser configurado para usar uma **identidade gerenciada** para acessar o Hub de eventos. Para obter mais informações, consulte [usar identidades gerenciadas para acessar o Hub de eventos de um trabalho de Azure Stream Analytics (versão prévia)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Hub IoT do Azure | Permite que o Hub IoT envie mensagens para os hubs de eventos em seu namespace do hub de eventos. Você também precisa executar as seguintes etapas: <ul><li>Habilitar a identidade atribuída pelo sistema para o Hub IoT</li><li>Adicione a identidade à função de remetente de dados dos hubs de eventos do Azure no namespace de hubs de eventos.</li><li>Em seguida, configure o Hub IoT que usa um hub de eventos como um ponto de extremidade personalizado para usar a autenticação baseada em identidade.</li></ul>
