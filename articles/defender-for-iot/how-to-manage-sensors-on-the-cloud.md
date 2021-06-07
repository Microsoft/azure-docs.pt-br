---
title: Gerenciar sensores e assinaturas no portal do Defender para IoT
description: Saiba como integrar, ver e gerenciar sensores no portal do Defender para IoT.
ms.date: 2/18/2021
ms.topic: how-to
ms.openlocfilehash: 5b4c8b3d10fe88816e07eb775b2bf3827d578b17
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383041"
---
# <a name="manage-sensors-and-subscriptions-in-the-defender-for-iot-portal"></a>Gerenciar sensores e assinaturas no portal do Defender para IoT

Este artigo descreve como integrar, ver e gerenciar sensores no [portal do Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Integrar sensores

Você integra um sensor registrando-o no Azure Defender para IoT e baixando um arquivo de ativação do sensor.

### <a name="register-the-sensor"></a>Registre o sensor

Para se inscrever:

1. Vá para a página de **Boas-vindas** no [portal do Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Selecione **Sensor integrado**.
1. Crie um nome de sensor. É recomendável incluir o endereço IP do sensor que você instalou como parte do nome ou usar um nome facilmente identificável. Isso garantirá um acompanhamento mais fácil e uma nomenclatura consistente entre o nome de registro no portal do Azure [Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) e o IP do sensor implantado exibido no console do sensor.
1. Associe o sensor a uma assinatura do Azure.
1. Escolha um modo de gerenciamento de sensor usando a alternância de **Nuvem conectada**. Se a alternância estiver ativada, o sensor será conectado à nuvem. Se a alternância estiver desativada, o sensor será gerenciado localmente.

   - **Sensores conectados à nuvem**: as informações que os sensores detectam são exibidas no console do sensor. As informações de alertas são entregues por meio de um hub IoT e podem ser compartilhadas com outros serviços do Azure, por exemplo, o Azure Sentinel.

   - **Sensores gerenciados localmente**: as informações que os sensores detectam são exibidas no console do sensor. Se você estiver trabalhando em uma rede desconectada e quiser obter uma exibição unificada de todas as informações detectadas por vários sensores gerenciados localmente, trabalhe com o console de gerenciamento local.

   Para sensores conectados à nuvem, o nome definido durante a integração é o nome que aparece no console do sensor. Você não pode alterar esse nome diretamente do console. Para os sensores gerenciados localmente, o nome aplicado durante a integração será armazenado no Azure e poderá ser atualizado no console do sensor.

1. Escolha um hub IoT para servir como um gateway entre este sensor e o Azure Defender para IoT.
1. Se o sensor estiver conectado à nuvem, associe-o a um hub IoT e defina um nome e uma zona do site. Também é possível adicionar tags descritivas. O nome, a zona e as marcas do site são entradas descritivas na [página Sites e Sensores](#view-onboarded-sensors).

### <a name="download-the-sensor-activation-file"></a>Baixar o arquivo de ativação do sensor

O arquivo de ativação do sensor contém instruções sobre o modo de gerenciamento do sensor. Você baixa um arquivo de ativação exclusivo para cada sensor implantado. Um usuário que entra no console do sensor pela primeira vez carrega o arquivo de ativação para o sensor.

Para baixar um arquivo de ativação:

1. Na página **Sensor Integrado**, selecione **baixar arquivo de ativação**.
1. Torne o arquivo acessível para o usuário que está entrando no console do sensor pela primeira vez.

## <a name="view-onboarded-sensors"></a>Exibir sensores integrados

No [portal do Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started), você pode ver informações básicas sobre sensores integrados.

1. Selecione **Sites e Sensores**.
1. Use as ferramentas de filtro e pesquisa para encontrar informações do sensor e de inteligência contra ameaças que você precisa.

- Quantos sensores foram integrados
- O número de sensores que são conectados à nuvem e gerenciados localmente
- O hub associado a um sensor integrado
- Detalhes adicionados sobre um sensor, como o nome atribuído ao sensor durante a integração, a zona associada ao sensor ou outras informações descritivas adicionadas com tags

## <a name="manage-onboarded-sensors"></a>Gerenciar sensores integrados

Use o [portal do Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) para tarefas de gerenciamento relacionadas a sensores.

Os sensores integrados podem ser exibidos na página **Sites e Sensores**. Você também pode editar as informações do sensor nessa página.

### <a name="export-sensor-details"></a>Exportar detalhes do sensor

Para exportar informações do sensor integrado, selecione o ícone **Exportar** na parte superior da página **Sites e Sensores**.

### <a name="edit-sensor-zone-details"></a>Editar detalhes da zona do sensor

Use as opções de edição de **Sites e Sensores** para editar o nome e a zona do sensor.

Para editar:

1. Selecione as **reticências** ( **...** ) do sensor que você deseja editar.
1. Selecione **Editar**.
1. Atualize a zona do sensor ou crie uma zona.

### <a name="delete-a-sensor"></a>Excluir um sensor

Se você excluir um sensor conectado à nuvem, as informações não serão enviadas para o hub IoT. Exclua os sensores conectados localmente quando você não estiver mais trabalhando com eles.

Para excluir um sensor:

1. Selecione as reticências ( **...** ) do sensor que você deseja excluir.
1. Confirme a exclusão.

### <a name="reactivate-a-sensor"></a>Reativar um sensor 

Talvez seja necessário reativar o sensor porque você deseja:

- **Trabalhar no modo conectado à nuvem em vez de no modo gerenciado localmente**: após a reativação, as detecções de sensor são exibidas no sensor e as informações de alerta recém detectadas são entregues por meio do hub IoT. Essas informações podem ser compartilhadas com outros serviços do Azure, como o Azure Sentinel.

- **Trabalhar no modo gerenciado localmente em vez de no modo conectado à nuvem**: após a reativação, as informações de detecção de sensor são exibidas apenas no sensor.

- **Associar o sensor a um novo hub IoT**: para fazer isso, registre novamente o sensor com um novo hub e baixe um novo arquivo de ativação.

Para reativar um sensor:

1. Vá para a página **Sites e Sensores** no [portal do Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Selecione o sensor para o qual você deseja carregar um novo arquivo de ativação.

3. Exclua o sensor.

4. Integre o sensor novamente no novo modo ou com um novo hub IoT selecionando **Integrar um sensor** na página Introdução.

5. Baixe o arquivo de ativação.

1. Entre no console do sensor do Defender para IoT.

7. No console do sensor, selecione **Configurações do Sistema** e escolha **Reativação**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Carregue o arquivo de ativação para reativar o sensor.":::

8. Selecione **Carregar** e selecione o arquivo que você salvou na página Sensor integrado.

9. Selecione **Ativar**.

## <a name="offboard-a-subscription"></a>Remover uma assinatura

As assinaturas são gerenciadas mensalmente. Quando remover uma assinatura, você será cobrado por ela até o fim do mês. 

Desinstale todos os sensores associados à assinatura antes de removê-la. Para obter mais informações sobre como excluir um sensor, confira [Excluir um sensor](#delete-a-sensor). 

Para remover uma assinatura:

1. Navegue até a página **Preço**.
1. Selecione a assinatura e escolha o ícone **excluir** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false":::.
1. No pop-up de confirmação, marque a caixa de seleção para confirmar que você excluiu todos os sensores associados à assinatura.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Marque a caixa de seleção e selecione Remover para remover seu sensor.":::

1. Selecione o botão **Remover**. 

O ambiente local não é afetado, mas você deve desinstalar o sensor do ambiente local ou transferi-lo para outra assinatura, de modo a impedir que dados relacionados fluam para o console de gerenciamento local. 

## <a name="next-steps"></a>Próximas etapas

[Ative e configure o sensor](how-to-activate-and-set-up-your-sensor.md)
