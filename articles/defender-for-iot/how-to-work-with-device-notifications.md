---
title: Trabalhar com notificações de dispositivos
description: As notificações fornecem informações sobre a atividade de rede que podem exigir sua atenção, juntamente com recomendações para lidar com essa atividade.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: cac8d609272be1d9f34b7e0d6404e0a0ea524df7
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509024"
---
# <a name="work-with-device-notifications"></a>Trabalhar com notificações de dispositivos

As notificações fornecem informações sobre a atividade de rede que podem exigir sua atenção, juntamente com recomendações para lidar com essa atividade. Por exemplo, você pode receber uma notificação sobre:

- Um dispositivo inativo. Se o dispositivo não for mais uma parte da sua rede, você poderá removê-lo. Se o dispositivo estiver inativo, por exemplo, porque está desconectado por engano da rede, reconecte o dispositivo e ignore a notificação.

- Um endereço IP foi detectado em um dispositivo que está atualmente identificado apenas por um endereço MAC. Responda por meio da autorização do endereço IP para o dispositivo.

Responder às notificações melhora as informações fornecidas no mapa do dispositivo, inventário de dispositivos e consultas e relatórios de mineração de dados. Ele também fornece informações sobre alterações de rede legítimas e possíveis configurações incorretas de rede.

Para acessar as notificações:

- Selecione **configurações do sistema** e, em seguida, selecione **aprimoramento de dados**.

## <a name="notifications-vs-alerts"></a>Notificações vs. alertas

Além de receber notificações sobre a atividade de rede, você pode receber *alertas*. As notificações fornecem informações sobre alterações de rede ou propriedades de dispositivo não resolvidas que não apresentam uma ameaça. Os alertas fornecem informações sobre os desvios de rede e as alterações que podem apresentar uma ameaça à rede.

Para exibir notificações:

1. Selecione **mapa do dispositivo** no painel de menu esquerdo do console.

2. Selecione o ícone **notificações** . O número vermelho acima do ícone indica o número de notificações.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Ícone de notificação.":::

   A janela **notificações** exibe todas as notificações que o sensor detectou.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Notificações.":::

## <a name="filter-the-notifications-window"></a>Filtrar a janela de notificações

Use filtros de pesquisa para exibir notificações de seu interesse.

| Filtrar por | Descrição |
|--|--|
| Filtrar por tipo | Exibir notificações que abrangem uma área de interesse específica. Por exemplo, exibir somente notificações para dispositivos inativos. |
| Filtrar por intervalo de datas | Exibir notificações que abrangem um intervalo de tempo específico. Por exemplo, exibir notificações enviadas pela última semana apenas. |
| Pesquisar informações específicas | Procure notificações específicas. |

## <a name="notification-events-and-responses"></a>Eventos e respostas de notificação

A tabela a seguir descreve os tipos de evento de notificação que você pode receber, juntamente com as opções para tratá-los. Você pode atualizar as informações do dispositivo com um valor recomendado ou ignorar a notificação. Quando você descartar uma notificação, as informações do dispositivo não serão atualizadas com as informações recomendadas. Se o tráfego for detectado novamente, a notificação será enviada novamente.

| Tipos de evento de notificação | Descrição | Respostas |
|--|--|--|
| Novo IP detectado | Um novo endereço IP está associado ao dispositivo. Podem ser detectados cinco cenários: <br /><br /> Um endereço IP adicional foi associado a um dispositivo. Esse dispositivo também está associado a um endereço MAC existente.<br /><br /> Um novo endereço IP foi detectado para um dispositivo que está usando um endereço MAC existente. Atualmente, o dispositivo não se comunica usando um endereço IP.<br /> <br /> Um novo endereço IP foi detectado para um dispositivo que está usando um nome NetBIOS. <br /><br /> Um endereço IP foi detectado como a interface de gerenciamento para um dispositivo associado a um endereço MAC. <br /><br /> Um novo endereço IP foi detectado para um dispositivo que está usando um endereço IP virtual. | **Definir IP adicional para dispositivo** (dispositivos de mesclagem) <br /> <br />**Substituir IP existente** <br /> <br /> **Ignorar**<br /> Remova a notificação. |
| Dispositivos inativos | O tráfego não foi detectado em um dispositivo por mais de 60 dias. | **Delete (excluir)** <br /> Se este dispositivo não fizer parte de sua rede, remova-o. <br /><br />**Ignorar** <br /> Remova a notificação se o dispositivo fizer parte de sua rede. Se o dispositivo estiver inativo (por exemplo, porque está desconectado da rede por engano), ignore a notificação e reconecte o dispositivo. |
| Novos dispositivos de OT | Uma sub-rede inclui um dispositivo de OT que não está definido em uma sub-rede ICS. <br /><br /> Cada sub-rede que contém pelo menos um dispositivo de se pode ser definida como uma sub-rede ICS. Isso ajuda a diferenciar entre os dispositivos de OT e de ti no mapa. | **Definir como sub-rede do ICS** <br /> <br /> **Ignorar** <br />Remova a notificação se o dispositivo não fizer parte da sub-rede. |
| Nenhuma sub-rede configurada | Não há sub-redes atualmente configuradas em sua rede. <br /><br /> Configure sub-redes para uma melhor representação no mapa e a capacidade de diferenciar entre os dispositivos de OT e de ti. | **Abra a configuração de sub-redes** e configure sub-redes. <br /><br />**Ignorar** <br /> Remova a notificação. |
| Alterações do sistema operacional | Um ou mais sistemas operacionais novos foram associados ao dispositivo. | Selecione o nome do novo sistema operacional que você deseja associar ao dispositivo.<br /><br /> **Ignorar** <br /> Remova a notificação. |
| Novas sub-redes | Novas sub-redes foram descobertas. | **Learn**<br />Adicione a sub-rede automaticamente.<br />**Abrir configuração de sub-rede**<br />Adicione todas as informações de sub-rede ausentes.<br />**Ignorar**<br />Remova a notificação. |
| Alterações de tipo de dispositivo | Um novo tipo de dispositivo foi associado ao dispositivo. | **Definir como {...}**<br />Associe o novo tipo ao dispositivo.<br />**Ignorar**<br />Remova a notificação. |

## <a name="respond-to-many-notifications-simultaneously"></a>Responder a várias notificações simultaneamente

Talvez seja necessário lidar com várias notificações simultaneamente. Por exemplo:

- Se ele fez uma atualização do sistema operacional para um grande conjunto de servidores de rede, você pode instruir o sensor para aprender as novas versões de servidor para todos os servidores atualizados. 

- Se um grupo de dispositivos em uma determinada linha tiver sido desativado e não estiver mais ativo, você poderá instruir o sensor a remover esses dispositivos do console.

Você pode instruir o sensor a aplicar informações recém detectadas a vários dispositivos ou ignorá-lo.   

Para exibir notificações e manipular notificações:

1. Use a opção **Filtrar por tipo, intervalo de datas** ou **selecionar tudo** . Desmarque as notificações conforme necessário.

2. Instrua o sensor a aplicar informações recém detectadas a dispositivos selecionados selecionando **aprender**. Ou instrua o sensor a ignorar informações recém detectadas selecionando **ignorar**. O número de notificações que você pode aprender e ignorar simultaneamente, juntamente com o número de notificações que você deve tratar individualmente, é mostrado.

**Novos IPS** e **nenhuma sub-rede** configurada eventos não podem ser tratados simultaneamente. Eles exigem confirmação manual.

## <a name="improve-device-os-classification-data-enhancement"></a>Melhorar a classificação do so do dispositivo: aprimoramento de dados 

O sensor autodetecta continuamente novos dispositivos de OT. Ele também detecta alterações automáticas em dispositivos descobertos anteriormente, incluindo tipos de sistema operacional.

Em determinadas circunstâncias, os conflitos podem ser detectados em sistemas operacionais descobertos. Isso pode acontecer porque você tem uma versão do sistema operacional que se refere a sistemas de desktop ou de servidor. Se isso acontecer, você receberá uma notificação com as classificações opcionais do sistema operacional.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Aprimore os dados.":::

Investigue as recomendações para enriquecer a classificação do sistema operacional. Essas informações são exibidas no inventário do dispositivo, nos relatórios de mineração de dados e em outros monitores. Ele também pode melhorar a precisão de alertas, ameaças e análise de riscos.

Quando você aceitar uma recomendação, as informações do tipo de sistema operacional serão atualizadas no sensor.

## <a name="see-also"></a>Consulte também

[Exibir alertas](how-to-view-alerts.md)
