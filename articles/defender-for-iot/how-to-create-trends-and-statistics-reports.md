---
title: Gerar relatórios de tendências e estatísticas
description: Conheça a atividade, as estatísticas e as tendências da rede usando o defender para tendências de IoT e os widgets de estatísticas.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: c28e2f1c24d39ceb915be9f4f6f222d70de9ee73
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522216"
---
# <a name="sensor-trends-and-statistics-reports"></a>Tendências de sensor e relatórios de estatísticas

## <a name="about-sensor-trends-and-statistics-reports"></a>Sobre tendências de sensor e relatórios de estatísticas

Você pode criar gráficos de widgets e gráficos de pizza para obter informações sobre as tendências e as estatísticas da rede. Os widgets podem ser agrupados em painéis definidos pelo usuário.

> [!NOTE]
> Os analistas de administrador e segurança podem criar relatórios de tendências e estatísticas.

O painel consiste em widgets que descrevem graficamente os seguintes tipos de informações:

- Tráfego por porta
- Tráfego principal por porta
- Largura de banda do canal
- Largura de banda total
- Conexão TCP ativa
- Largura de banda principal por VLAN
- Dispositivos:
  - Novos dispositivos
  - Dispositivos ocupados
  - Dispositivos por fornecedor
  - Dispositivos por sistema operacional
  - Número de dispositivos por VLAN
  - Dispositivos desconectados
- A conectividade cai por horas
- Alertas para incidentes por tipo
- Acesso à tabela do banco de dados
- Widgets de desseção de protocolo
- DELTAV
  - Distribuição de operações do DeltaV Roc
  - Eventos DeltaV Roc por nome
  - Eventos de DeltaV por tempo
- AMS
  - Tráfego AMS por porta do servidor
  - Tráfego AMS por comando
- Ethernet e endereço IP:
  - Tráfego de endereço IP e Ethernet pelo serviço CIP
  - Tráfego de endereço IP e Ethernet por classe CIP
  - Tráfego de endereço IP e Ethernet por comando
- OPC
  - Operações de gerenciamento superior do OPC
  - Operações de e/s superiores do OPC
- Siemens S7:
  - Tráfego S7 por função de controle
  - Tráfego S7 por subfunção
- VLAN
  - Número de dispositivos por VLAN
  - Largura de banda principal por VLAN
- 60870-5-104
  - Tráfego IEC-60870 por ASDU
- BACNET
  - Serviços BACnets
- DNP3
  - Tráfego DNP3 por função
- SRTP
  - Tráfego de SRTP por código de serviço
  - Erros de SRTP por dia
- SuiteLink:
  - SuiteLink principais marcas consultadas
  - Comportamento de marca numérica SuiteLink
- Tráfego IEC-60870 por ASDU
- Tráfego DNP3 por função
- Tráfego de MMS por serviço
- Tráfego Modbus por função
- Tráfego OPC-UA por serviço

> [!NOTE]
>  A hora nos widgets é definida de acordo com o tempo do sensor.

## <a name="create-reports"></a>Criar relatórios

Para ver os painéis e widgets:

Selecione **tendências & estatísticas** no menu lateral.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Captura de tela de uma investigação.":::

Por padrão, os resultados são exibidos para detecções nos últimos 7 dias. Você pode usar as ferramentas de filtro para alterar esse intervalo. Por exemplo, uma pesquisa de texto livre.

## <a name="next-steps"></a>Próximas etapas

Relatórios de avaliação de [risco](how-to-create-risk-assessment-reports.md) 
 Consultas de Data Mining de [sensor](how-to-create-data-mining-queries.md) 
 [Relatório de vetor de ataque](how-to-create-attack-vector-reports.md)