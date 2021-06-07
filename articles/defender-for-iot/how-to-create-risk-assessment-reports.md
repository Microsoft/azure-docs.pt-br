---
title: Criar relatórios de avaliação de risco
description: Obter informações sobre os riscos de rede detectados por sensores individuais ou uma exibição agregada de riscos detectados por todos os sensores.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 853157ef1b97fefdd15785b2a71c7ccc5d06a9a9
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784247"
---
# <a name="risk-assessment-reporting"></a>Relatórios de avaliação de risco

## <a name="about-risk-assessment-reports"></a>Sobre os relatórios de avaliação de risco

Os relatórios de avaliação de risco fornecem:

- Uma pontuação de segurança geral para os dispositivos detectados por sensores organizacionais.

- Uma pontuação de segurança para cada dispositivo de rede detectado por um sensor individual.

- Uma análise do número de dispositivos vulneráveis, dispositivos que precisam de melhoria e dispositivos seguros.

-  Informações sobre problemas de segurança e operacionais:

    - Problemas de configuração

    - Vulnerabilidade do dispositivo priorizada por nível de segurança

    - Problemas de segurança de rede

    - Problemas operacionais de rede

    - Conexões com redes ICS

    - Conexões com a Internet

    - Indicadores de malware industrial

    - Problemas de protocolo

    - Vetores de ataque

### <a name="risk-mitigation"></a>Mitigação de risco

Os relatórios fornecem recomendações para ajudá-lo a melhorar sua pontuação de segurança. Por exemplo, instale as atualizações de segurança mais recentes, atualize o firmware para a versão mais recente ou acompanhe os alertas.

## <a name="about-security-scores"></a>Sobre pontuações de segurança

A pontuação geral de segurança de rede é gerada em cada relatório. A pontuação representa a porcentagem de segurança de 100 por cento. Por exemplo, uma pontuação de 30% indicaria que a rede está 30% segura.

As pontuações de avaliação de risco são baseadas nas informações aprendidas na inspeção de pacotes, nos mecanismos de modelagem comportamental e em um design de máquina de estado específico do SCADA.

**Dispositivos seguros** são dispositivos com uma pontuação de segurança acima de 90%.

**Dispositivos que precisam de melhoria**: dispositivos com uma pontuação de segurança entre 70% e 89%.

**Dispositivos vulneráveis** são dispositivos com uma pontuação de segurança abaixo de 70%.

## <a name="create-risk-assessment-reports"></a>Criar relatórios de avaliação de risco

Criar um relatório de avaliação de risco de PDF. O nome do relatório é gerado automaticamente como risk-assessment-report-1.pdf. O número é atualizado para cada novo relatório que você criar.  A hora e o dia da criação são exibidos.

### <a name="create-a-sensor-risk-assessment-report"></a>Criar um relatório de avaliação de risco do sensor

Crie um relatório de avaliação de riscos com base em detecções feitas pelo sensor no qual você está conectado.

Para criar um relatório:

1. Faça logon no console do sensor.
1. Selecione **avaliação de risco** no menu lateral.
1. Selecione **gerar relatório**. O relatório aparece na seção relatórios arquivados.
1. Selecione o relatório na seção relatórios arquivados para baixá-lo.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Uma exibição da avaliação de risco.":::

Para importar um logotipo da empresa:

- Selecione **importar logotipo**.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Criar um relatório de avaliação de risco do console de gerenciamento local

Crie um relatório de avaliação de riscos com base em detecções feitas por qualquer um dos sensores gerenciados pelo seu console de gerenciamento local. 

Para criar um relatório:

1. Selecione **avaliação de risco** no menu lateral.

2. Selecione um sensor na lista suspensa **selecionar sensor** .

3. Selecione **gerar relatório**.

4. Selecione **baixar** na seção **relatórios arquivados** .

Para importar um logotipo da empresa:

- Selecione **importar logotipo**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importe seu logotipo por meio da exibição de avaliação de risco.":::

## <a name="see-also"></a>Veja também

[Relatório de vetor de ataque](how-to-create-attack-vector-reports.md)

