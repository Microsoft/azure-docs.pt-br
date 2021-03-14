---
title: Integração e gerenciamento de sensores e assinaturas no portal do defender para IoT
description: Saiba como carregar, exibir e gerenciar sensores no portal do defender para IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 2/18/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 63b3b450e289b40aa9acbfb0d5170e8eb57f9e58
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733254"
---
# <a name="onboard-and-manage-sensors-and-subscriptions-in-the-defender-for-iot-portal"></a>Integração e gerenciamento de sensores e assinaturas no portal do defender para IoT

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
1. Use as ferramentas de filtro e pesquisa para encontrar informações de inteligência de sensor e ameaça que você precisa.

- Quantos sensores foram integrados
- O número de sensores que são conectados à nuvem e gerenciados localmente
- O Hub associado a um sensor integrado
- Detalhes adicionados sobre um sensor, como o nome atribuído ao sensor durante a integração, a zona associada ao sensor ou outras informações descritivas adicionadas com marcas

## <a name="manage-onboarded-sensors"></a>Gerenciar sensores integrados

Use o [defender para o portal de IOT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) para tarefas de gerenciamento relacionadas a sensores.

Sensores integrados podem ser exibidos na página **sites e sensores** . Você também pode editar informações de sensor nesta página.

### <a name="export-sensor-details"></a>Detalhes do sensor de exportação

Para exportar informações de sensor integrado, selecione o ícone **Exportar** na parte superior da página **sites e sensores** .

### <a name="edit-sensor-zone-details"></a>Editar detalhes da zona do sensor

Use as opções de edição **sites e sensores** para editar o nome do sensor e a zona.

Para editar:

1. Selecione as **reticências** (**...**) para o sensor que você deseja editar.
1. Selecione **Editar**.
1. Atualize a zona do sensor ou crie uma nova zona.

### <a name="delete-a-sensor"></a>Excluir um sensor

Se você excluir um sensor conectado à nuvem, as informações não serão enviadas para o Hub IoT. Exclua os sensores conectados localmente quando você não estiver mais trabalhando com eles.

Para excluir um sensor:

1. Selecione as reticências (**...**) para o sensor que você deseja excluir.
1. Confirme a exclusão.

### <a name="reactivate-a-sensor"></a>Reativar um sensor 

Talvez seja necessário reativar o sensor porque você deseja:

- **Trabalhar no modo conectado à nuvem em vez de no modo gerenciado localmente**: após a reativação, as detecções de sensor são exibidas no sensor e as informações de alerta recém detectadas são entregues por meio do Hub IOT. Essas informações podem ser compartilhadas com outros serviços do Azure, como o Azure Sentinel.

- **Trabalhar no modo gerenciado localmente em vez de no modo conectado à nuvem**: após a reativação, as informações de detecção de sensor são exibidas apenas no sensor.

- **Associar o sensor a um novo hub IOT**: para fazer isso, registre novamente o sensor com um novo hub e, em seguida, baixe um novo arquivo de ativação.

Para reativar um sensor:

1. Vá para a página **sites e sensores** no [portal do defender para IOT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Selecione o sensor para o qual você deseja carregar um novo arquivo de ativação.

3. Exclua o sensor.

4. Integre o sensor novamente no novo modo ou com um novo hub IoT selecionando **um sensor** na página Introdução.

5. Baixe o arquivo de ativação.

1. Entre no console do sensor do defender para IoT.

7. No console do sensor, selecione **configurações do sistema** e, em seguida, selecione **reativação**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Carregue o arquivo de ativação para reativar o sensor.":::

8. Selecione **carregar** e selecione o arquivo que você salvou na página sensor integrado.

9. Selecione **Ativar**.

## <a name="offboard-a-subscription"></a>Transferir uma assinatura

As assinaturas são gerenciadas mensalmente. Quando você transferir uma assinatura, você será cobrado por essa assinatura até o fim do mês. 

Desinstale todos os sensores associados à assinatura antes de remover a assinatura. Para obter mais informações sobre como excluir um sensor, consulte [excluir um sensor](#delete-a-sensor). 

Para transferir uma assinatura:

1. Navegue até a página de **preços** .
1. Selecione a assinatura e, em seguida, selecione o ícone **excluir** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. No pop-up de confirmação, marque a caixa de seleção para confirmar que você excluiu todos os sensores associados à assinatura.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Marque a caixa de seleção e selecione transferir para transferir seu sensor.":::

1. Selecione o botão **transferir** . 

O ambiente local não é afetado, mas você deve desinstalar o sensor do ambiente local ou reatribuir o sensor a outra assinatura, de modo a impedir que todos os dados relacionados fluam para o console de gerenciamento local. 

## <a name="see-also"></a>Confira também

[Ativar e configurar seu sensor](how-to-activate-and-set-up-your-sensor.md)
