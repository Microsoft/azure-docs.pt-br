---
title: Configurar a solução baseada no agente do Azure defender para IoT
description: Saiba como configurar a coleta de dados na solução baseada no Azure defender para IoT Agent
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 53fc01839ef522afaffe52cd8a3126e40ba94a05
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809690"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>Configurar a solução baseada no agente do Azure defender para IoT  

Este artigo descreve como configurar a coleta de dados na solução baseada no Azure defender para IoT Agent.

## <a name="configure-data-collection"></a>Configurar a coleta de dados

Para configurar a coleta de dados na solução baseada no Azure defender para IoT Agent: 

1. Navegue até a portal do Azure e selecione o Hub IoT ao qual o defender para IoT está anexado 

1. No menu  **segurança**   , selecione **configurações**. 

1. Selecione a **coleta de dados**. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="Selecione coleta de dados nas configurações do menu segurança.":::

## <a name="geolocation-and-ip-address-handling"></a>Geolocalização e manipulação de endereço IP 

Para proteger sua solução de IoT, os endereços IP das conexões de entrada e de saída para seus dispositivos IoT, IoT Edge e Hub IoT são coletados e armazenados por padrão. Essas informações são essenciais e usadas para detectar conectividade anormal de fontes de endereço IP suspeitas. Por exemplo, quando há tentativas feitas, tente estabelecer conexões de uma fonte de endereço IP de um botnet conhecido ou de uma origem de endereço IP fora de sua localização geográfica. O serviço defender para IoT oferece a flexibilidade para habilitar e desabilitar a coleta de dados de endereço IP a qualquer momento. 

Para habilitar ou desabilitar a coleta de dados de endereço IP: 

1. Abra o Hub IoT e, em seguida, selecione **configurações**   no menu **segurança**   . 

1. Selecione a tela de **coleta de dados**   e modifique a localização geográfica e as configurações de manipulação de endereço IP para atender às suas necessidades. 

## <a name="log-analytics-creation"></a>Criação do Log Analytics 

O defender para IoT permite armazenar alertas de segurança, recomendações e dados brutos de segurança, em seu espaço de trabalho Log Analytics. Log Analytics ingestão no Hub IoT é definida como **off** por padrão na solução defender para IOT. É possível anexar o defender para IoT a um espaço de trabalho de análise de logs e armazenar os dados de segurança ali também. 

Há dois tipos de informações armazenadas por padrão em seu espaço de trabalho Log Analytics pelo defender para IoT:
 
- Alertas de segurança.

- Recommendations. 

Você pode optar por adicionar armazenamento de um tipo de informações adicional como `raw events` . 

> [!Note] 
> Armazenar  `raw events`   em log Analytics transporta custos de armazenamento adicionais. 

Para permitir que Log Analytics funcionem com o micro Agent: 

1. Navegue até **configuração do espaço de trabalho**  >  **coleta de dados** e alterne  **** para ativar. 

1. Crie um novo espaço de trabalho Log Analytics ou anexe um existente. 

1. Verifique se a opção **acesso a dados brutos de segurança**   está selecionada.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Verifique se o acesso a dados brutos de segurança está selecionado.":::

1. Selecione **Salvar**.

Todos os meses, os primeiros 5 gigabytes de dados ingeridos, por cliente, para o serviço de Log Analytics do Azure, são gratuitos. Cada gigabyte de dados ingeridos em seu espaço de trabalho do Log Analytics do Azure é mantido sem encargos durante os primeiros 31 dias. Para obter mais informações sobre preços, consulte [log Analytics preços](https://azure.microsoft.com/pricing/details/monitor/). 

Para alterar a configuração do workspace do Log Analytics: 

1. No Hub IoT, no menu **segurança** , selecione  **configurações**. 

1. Selecione a tela de **coleta de dados**   e modifique a configuração do espaço de trabalho das configurações de log Analytics para atender às suas necessidades. 

Para acessar seus alertas em seu espaço de trabalho do Log Analytics após a configuração:

1. Selecione um alerta no defender para IoT.

1. Selecione **investigar alertas no espaço de trabalho log Analytics**.

Para acessar seus alertas em seu espaço de trabalho do Log Analytics após a configuração:

1. Selecione uma recomendação no defender para IoT.

1. Selecione **investigar recomendações no espaço de trabalho log Analytics**. 
 
Para obter mais informações sobre como consultar dados de Log Analytics, consulte Introdução [às consultas no log Analytics](../azure-monitor/log-query/get-started-queries.md). 

## <a name="turn-off-defender-for-iot"></a>Desligar o defender para IoT 

Para ativar ou desativar um serviço defender para IoT em um hub IoT específico: 

1. No Hub IoT, no menu **segurança** , selecione  **configurações**.

1. Selecione a tela de **coleta de dados**   e modifique a configuração do espaço de trabalho das configurações de log Analytics para atender às suas necessidades.

## <a name="next-steps"></a>Próximas etapas 

Avance para o próximo artigo para [configurar sua solução](quickstart-configure-your-solution.md). 
