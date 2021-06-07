---
title: Aprofunde-se nas ameaças globais, regionais e locais
description: Receba informações sobre ameaças globais, regionais e locais usando o mapa do site no console de gerenciamento local.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: db3b9fbca9acd6c4ce1cfe137a4024f66d8a6292
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784111"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Aprofunde-se nas ameaças globais, regionais e locais

O mapa do site no console de gerenciamento local ajuda você a obter cobertura de segurança completa dividindo sua rede em segmentos geográficos e geológicos que refletem sua topologia de negócios:

- **Nível de instalações geográficas**: um site reflete vários dispositivos agrupados de acordo com uma localização geográfica apresentada no mapa. Por padrão, o Azure defender para IoT fornece um mapa mundial. Você atualiza o mapa para refletir sua estrutura organizacional ou comercial. Por exemplo, use um mapa que reflete sites em um país específico, cidade ou campus industrial. Quando a cor do site é alterada no mapa, ela fornece à equipe do SOC uma indicação do status crítico do sistema na instalação.

  O mapa é interativo e permite abrir cada site e aprofundar-se nas informações deste site.

- **Camada lógica global**: uma unidade de negócios é uma maneira de dividir sua empresa em segmentos lógicos de acordo com setores específicos. Quando você faz isso, sua topologia de negócios é refletida no mapa.

  Por exemplo, uma empresa global que contém fábricas de vidro, fábricas plásticas e fábricas de automóvel pode ser gerenciada como três unidades de negócios diferentes. Um site físico localizado em Toronto inclui três linhas de produção de vidro diferentes, uma linha de produção plástica e uma linha de produção de mecanismo de caminhão. Portanto, este site tem representantes de todas as três unidades de negócios.

- **Nível de região geográfica**: Crie regiões para dividir uma empresa global em regiões geográficas. Por exemplo, a empresa que descrevemos pode usar as regiões América do Norte, Europa Ocidental e Europa Oriental. América do Norte tem fábricas de todas as três unidades de negócios. A Europa ocidental tem fábricas de automóveis e fábricas de vidro, e a Europa Oriental tem apenas fábricas plásticas.

- **Nível de segmento lógico local**: uma zona é um segmento lógico em um site que define, por exemplo, uma área funcional ou uma linha de produção. Trabalhar com zonas permite a imposição de políticas de segurança que são relevantes para a definição de zona. Por exemplo, um site que contém cinco linhas de produção pode ser segmentado em cinco zonas.

- **Nível de exibição local**: uma exibição local de uma única instalação de sensor fornece informações sobre o status operacional e de segurança dos dispositivos conectados.

## <a name="work-with-site-map-views"></a>Trabalhar com exibições de mapa do site

O console de gerenciamento local fornece uma visão geral da sua rede industrial em um mapa relacionado ao contexto. A exibição de mapa geral apresenta o mapa global da sua organização com a localização geográfica de cada site.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Captura de tela da exibição do mapa empresarial.":::

### <a name="color-coded-map-views"></a>Exibições de mapa codificadas por cor

**Verde**: o número de eventos de segurança está abaixo do limite que o defender para IOT definiu para o seu sistema. Nenhuma ação é necessária.

**Amarelo**: o número de eventos de segurança é igual ao limite que o defender para IOT definiu para o seu sistema. Considere investigar os eventos.  

**Vermelho**: o número de eventos de segurança está além do limite que o defender para IOT definiu para o seu sistema. Executar ação imediata.

### <a name="risk-level-map-views"></a>Exibições de mapa no nível de risco

**Avaliação de risco**: a exibição de avaliação de risco exibe informações sobre riscos do site. As informações de risco ajudam a priorizar a mitigação e criar um mapa de estrada para planejar melhorias de segurança.

**Resposta a incidentes**: Obtenha uma exibição centralizada de todos os alertas não confirmados em cada site em toda a empresa. Você pode fazer uma busca detalhada e gerenciar alertas detectados em um site específico.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Captura de tela da exibição do mapa Enterprise com resposta a incidentes.":::

**Atividade mal-intencionada**: se o malware foi detectado, o site aparecerá em vermelho. Isso indica que você deve tomar uma ação imediata.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Captura de tela da exibição do mapa empresarial com atividade mal-intencionada.":::

**Alertas operacionais**: este modo de exibição de mapa para sistemas de OT fornece uma compreensão melhor do que o sistema pode apresentar incidentes operacionais, como interrupções de PLC, carregamento de firmware e carregamento de programa.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Captura de tela da exibição do mapa Enterprise com alertas operacionais.":::

Para escolher uma exibição de mapa:

1. Selecione **modo de exibição padrão** no mapa.
2. Selecione um modo de exibição.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Captura de tela da exibição padrão do mapa do site.":::

## <a name="update-the-site-map-image"></a>Atualizar a imagem do mapa do site

O defender para IoT fornece um mapa do mundo padrão. Você pode alterá-lo para refletir sua organização: um mapa de país ou um mapa de cidade, por exemplo. 

Para substituir o mapa:

1. No painel esquerdo, selecione **configurações do sistema**.

2. Selecione **Alterar mapa do site** e carregar o arquivo gráfico para substituir o mapa padrão.

## <a name="next-step"></a>Próxima etapa

[Exibir alertas](how-to-view-alerts.md)
