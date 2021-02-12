---
title: Carregar e gerenciar sensores no portal do defender para IoT
description: Saiba como carregar, exibir e gerenciar sensores no portal do defender para IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 427ea3884a3db6ba33405014435cf1f962670064
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562702"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>Carregar e gerenciar sensores no portal do defender para IoT

Este artigo descreve como carregar, exibir e gerenciar sensores no [portal do defender para IOT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Integrar sensores

Você integra um sensor registrando-o no Azure defender para IoT e baixando um arquivo de ativação do sensor.

### <a name="register-the-sensor"></a>Registrar o sensor

Para se inscrever:

1. Vá para a página de **boas-vindas** no [portal do defender para IOT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Selecione **sensor integrado**.
1. Crie um nome de sensor. Recomendamos que você inclua o endereço IP do sensor que você instalou como parte do nome ou use um nome facilmente identificável. Isso garantirá o controle mais fácil e a nomenclatura consistente entre o nome de registro no portal do Azure [defender para IOT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) e o IP do sensor implantado exibido no console do sensor.
1. Associe o sensor a uma assinatura do Azure.
1. Escolha um modo de gerenciamento de sensor usando a alternância **conectada à nuvem** . Se a alternância estiver ativada, o sensor será conectado à nuvem. Se a alternância estiver desativada, o sensor será gerenciado localmente.

   - **Sensores conectados à nuvem**: as informações detectadas pelo sensor são exibidas no console do sensor. As informações de alerta são entregues por meio de um hub IoT e podem ser compartilhadas com outros serviços do Azure, como o Azure Sentinel.

   - **Sensores gerenciados localmente**: informações que os sensores detectam são exibidas no console do sensor. Se você estiver trabalhando em uma rede de ar-gapped e quiser uma exibição unificada de todas as informações detectadas por vários sensores gerenciados localmente, trabalhe com o console de gerenciamento local.

   Para sensores conectados à nuvem, o nome definido durante a integração é o nome que aparece no console do sensor. Você não pode alterar esse nome diretamente do console. Para sensores gerenciados localmente, o nome aplicado durante a integração será armazenado no Azure e poderá ser atualizado no console do sensor.

1. Escolha um hub IoT para servir como um gateway entre este sensor e o Azure defender para IoT.
1. Se o sensor estiver conectado à nuvem, associe-o a um hub IoT e, em seguida, defina um nome de site e uma zona. Você também pode adicionar marcas descritivas. O nome, a zona e as marcas do site são entradas descritivas na [página sites e sensores](#view-onboarded-sensors).

### <a name="download-the-sensor-activation-file"></a>Baixar o arquivo de ativação do sensor

O arquivo de ativação do sensor contém instruções sobre o modo de gerenciamento do sensor. Você baixa um arquivo de ativação exclusivo para cada sensor implantado. Um usuário que entra no console do sensor pela primeira vez carrega o arquivo de ativação para o sensor.

Para baixar um arquivo de ativação:

1. Na página **sensor integrado** , selecione **baixar arquivo de ativação**.
1. Torne o arquivo acessível ao usuário que está entrando no console do sensor pela primeira vez.

## <a name="view-onboarded-sensors"></a>Exibir sensores integrados

No [portal do defender para IOT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started), você pode exibir informações básicas sobre sensores integrados. 

1. Selecione **sites e sensores**.
1. Na página **sites e sensores** , use as ferramentas de filtro e pesquisa para encontrar informações de sensor de que você precisa.

As informações disponíveis incluem:

- Quantos sensores foram integrados
- O número de sensores que são conectados à nuvem e gerenciados localmente
- O Hub associado a um sensor integrado
- Detalhes adicionados sobre um sensor, como o nome atribuído ao sensor durante a integração, a zona associada ao sensor ou outras informações descritivas adicionadas com marcas

## <a name="manage-onboarded-sensors"></a>Gerenciar sensores integrados

Use o [defender para o portal de IOT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) para tarefas de gerenciamento relacionadas a sensores.

### <a name="export"></a>Exportação

Para exportar informações de sensor integrado, selecione o ícone **Exportar** na parte superior da página **sites e sensores** .

### <a name="edit"></a>Editar

Use as ferramentas de edição de **sites e sensores** para adicionar e editar o nome, a zona e as marcas do site.

### <a name="delete"></a>Excluir

Se você excluir um sensor conectado à nuvem, as informações não serão enviadas para o Hub IoT. Exclua os sensores conectados localmente quando você não estiver mais trabalhando com eles.

Para excluir um sensor:

1. Selecione as reticências (**...**) para o sensor que você deseja excluir. 
1. Confirme a exclusão.

### <a name="reactivate"></a>Reativar

Talvez você queira atualizar o modo no qual o sensor é gerenciado. Por exemplo:

- **Trabalhar no modo conectado à nuvem em vez de no modo gerenciado localmente**: para fazer isso, atualize o arquivo de ativação para o sensor conectado localmente com um arquivo de ativação para um sensor conectado à nuvem. Após a reativação, as detecções do sensor são exibidas no sensor e no [defender para o portal de IOT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started). Depois que o arquivo de reativação for carregado com êxito, as informações de alerta recém detectadas serão enviadas para o Azure.

- **Trabalhar no modo conectado localmente em vez do modo conectado à nuvem**: para fazer isso, atualize o arquivo de ativação para um sensor conectado à nuvem com um arquivo de ativação para um sensor gerenciado localmente. Após a reativação, as informações de detecção de sensor são exibidas apenas no sensor.

- **Associar o sensor a um novo hub IOT**: para fazer isso, registre novamente o sensor e carregue um novo arquivo de ativação.

Para reativar um sensor:

1. Vá para a página **sites e sensores** no [portal do defender para IOT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Selecione o sensor para o qual você deseja carregar um novo arquivo de ativação.

3. Exclua o sensor.

4. Integre o sensor novamente da página de **integração** no modo novo ou com um novo hub IOT.

5. Baixe o arquivo de ativação na página **baixar arquivo de ativação** .

6. Entre no console do sensor do defender para IoT.

7. No console do sensor, selecione **configurações do sistema** e, em seguida, selecione **reativação**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Carregue o arquivo de ativação para reativar o sensor.":::

8. Selecione **carregar** e selecione o arquivo que você salvou.

9. Selecione **Ativar**. 

## <a name="see-also"></a>Confira também

[Ativar e configurar seu sensor](how-to-activate-and-set-up-your-sensor.md)
