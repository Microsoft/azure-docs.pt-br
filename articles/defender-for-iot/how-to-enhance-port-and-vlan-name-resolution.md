---
title: Aprimorar a resolução de nomes de porta e de VLAN
description: Personalize os nomes de porta e VLAN em seus sensores para enriquecer a resolução do dispositivo.
ms.date: 12/13/2020
ms.topic: how-to
ms.openlocfilehash: de6fbe70d5a5359ad4e4c276642b9b9ed0cef00f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784162"
---
# <a name="enhance-port-vlan-and-os-resolution"></a>Melhorar a resolução de porta, VLAN e so

Você pode personalizar os nomes de porta e VLAN em seus sensores para enriquecer a resolução do dispositivo.

## <a name="customize-port-names"></a>Personalizar nomes de porta

O Azure defender para IoT atribui automaticamente nomes à maioria das portas reservadas universalmente, como DHCP ou HTTP. Você pode personalizar nomes de porta para outras portas que o defender para IoT detecta. Por exemplo, atribua um nome a uma porta não reservada, pois essa porta mostra uma atividade excepcionalmente alta.

Esses nomes aparecem quando:

  - Selecione **grupos de dispositivos** no mapa do dispositivo.

  - Você cria widgets que fornecem informações de porta.

### <a name="view-custom-port-names-in-the-device-map"></a>Exibir nomes de porta personalizados no mapa do dispositivo

As portas que incluem um nome definido pelos usuários aparecem no mapa do dispositivo, no grupo **aplicativos conhecidos** .

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="Captura de tela do mapa do dispositivo, mostrando o grupo aplicativos conhecidos.":::

### <a name="view-custom-port-names-in-widgets"></a>Exibir nomes de porta personalizados em widgets

Os nomes de porta que você definiu aparecem nos widgets que abrangem o tráfego por porta.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="Cubra o tráfego.":::

Para definir nomes de porta personalizados:

1. Selecione **configurações do sistema** e, em seguida, selecione **aliases padrão**.

2. Selecione **Adicionar alias de porta**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="Adicionar alias de porta.":::

3. Insira o número da porta, selecione **TCP/UDP**, ou selecione **ambos** e adicione o nome.

4. Selecione **Salvar**.

## <a name="configure-vlan-names"></a>Configurar nomes de VLAN

Você pode enriquecer os dados de inventário de dispositivos com marcas e números de VLAN de dispositivo. Além do enriquecimento de dados, você pode exibir o número de dispositivos por VLAN e exibir a largura de banda por widgets de VLAN.

O suporte a VLANs é baseado no 802.1 q (até a ID de VLAN 4094).

Dois métodos estão disponíveis para recuperar informações de VLAN:

- **Descoberto automaticamente**: por padrão, o sensor descobre automaticamente VLANs. As VLANs detectadas com o tráfego são exibidas na tela configuração de VLAN, em relatórios de mineração de dados e em outros relatórios que contêm informações de VLAN. VLANs não utilizadas não são exibidas. Você não pode editar ou excluir essas VLANs. 

  Você deve adicionar um nome exclusivo a cada VLAN. Se você não adicionar um nome, o número de VLAN aparecerá em todos os locais em que a VLAN é relatada.

- **Adicionado manualmente**: você pode adicionar VLANs manualmente. Você deve adicionar um nome exclusivo para cada VLAN que foi adicionado manualmente e pode editar ou excluir essas VLANs.

Os nomes de VLAN podem conter até 50 caracteres ASCII.

> [!NOTE]
> Os nomes de VLAN não são sincronizados entre o sensor e o console de gerenciamento. Você também precisa definir o nome no console de gerenciamento.  
Para switches Cisco, adicione a seguinte linha à configuração de span: `monitor session 1 destination interface XX/XX encapsulation dot1q` . Nesse comando, *XX/XX* é o nome e o número da porta.

Para configurar os nomes de VLAN:

1. No menu lateral, selecione **configurações do sistema**.

2. Na janela **configurações do sistema** , selecione **VLAN**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Use as configurações do sistema para editar suas VLANs.":::

3. Adicione um nome exclusivo ao lado de cada ID de VLAN.

## <a name="improve-device-operating-system-classification-data-enhancement"></a>Melhorar a classificação do sistema operacional do dispositivo: aprimoramento de dados

Os sensores detectam automaticamente novos dispositivos, bem como alterações em dispositivos descobertos anteriormente, incluindo tipos de sistema operacional.

Em determinadas circunstâncias, os conflitos podem ser detectados em sistemas operacionais descobertos. Isso pode acontecer, por exemplo, se você tiver uma versão de sistemas operacionais que se refere a sistemas de desktop ou de servidor. Se isso acontecer, você receberá uma notificação com classificações opcionais de sistemas operacionais.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Aprimore os dados.":::

Investigue as recomendações para enriquecer a classificação do sistema operacional. Essa classificação aparece no inventário do dispositivo, nos relatórios de mineração de dados e em outros monitores. Verificar se essas informações estão atualizadas pode melhorar a precisão de alertas, ameaças e relatórios de análise de risco.

Para acessar as recomendações do sistema operacional:

1. Selecione **configurações do sistema**.
1. Selecione **aprimoramento de dados**.

## <a name="next-steps"></a>Próximas etapas

Exibir informações de dispositivo aprimorado em vários relatórios:

- [Investigar as detecções do sensor em um inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Tendências de sensor e relatórios de estatísticas](how-to-create-trends-and-statistics-reports.md)
- [Consultas de Data Mining de sensor](how-to-create-data-mining-queries.md)
