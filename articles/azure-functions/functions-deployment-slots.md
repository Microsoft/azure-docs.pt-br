---
title: Azure Functions slots de implantação
description: Aprenda a criar e usar slots de implantação com Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 2dbf7c31e6b0b40fa9dc2d59e86c0ecc731657e1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172402"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions slots de implantação

Azure Functions slots de implantação permitem que seu aplicativo de funções execute diferentes instâncias chamadas "Slots". Os slots são ambientes diferentes expostos por meio de um ponto de extremidade disponível publicamente. Uma instância de aplicativo sempre é mapeada para o slot de produção e você pode trocar instâncias atribuídas a um slot sob demanda. Os aplicativos de funções em execução no plano de serviço de aplicativos podem ter vários slots, enquanto no plano de consumo apenas um slot é permitido.

O seguinte reflete como as funções são afetadas por slots de permuta:

- O redirecionamento de tráfego é contínuo; nenhuma solicitação foi descartada devido a uma troca.
- Se uma função estiver em execução durante uma permuta, a execução continuará e os próximos gatilhos serão roteados para a instância do aplicativo trocado.

> [!NOTE]
> No momento, os slots não estão disponíveis para o plano de consumo do Linux.

## <a name="why-use-slots"></a>Por que usar Slots?

Há várias vantagens em usar slots de implantação. Os cenários a seguir descrevem usos comuns para Slots:

- **Ambientes diferentes para finalidades diferentes**: usar Slots diferentes oferece a oportunidade de diferenciar instâncias de aplicativo antes de alternar para a produção ou para um slot de preparo.
- **Preaquecimento**: a implantação em um slot em vez de diretamente na produção permite que o aplicativo fique quente antes de entrar no ar. Além disso, o uso de Slots reduz a latência para cargas de trabalho disparadas por HTTP. As instâncias são ativadas antes da implantação, o que reduz o início frio para funções implantadas recentemente.
- **Fallbacks fáceis**: após uma troca com produção, o slot com um aplicativo previamente preparado agora tem o aplicativo de produção anterior. Se as alterações trocadas no slot de produção não forem as esperadas, você poderá reverter a troca imediatamente para obter a "última instância boa conhecida" de volta.

## <a name="swap-operations"></a>Operações de permuta

Durante uma troca, um slot é considerado a origem e o outro destino. O slot de origem tem a instância do aplicativo que é aplicada ao slot de destino. As etapas a seguir garantem que o slot de destino não experimente tempo de inatividade durante uma troca:

1. **Aplicar configurações:** As configurações do slot de destino são aplicadas a todas as instâncias do slot de origem. Por exemplo, as configurações de produção são aplicadas à instância de preparo. As configurações aplicadas incluem as seguintes categorias:
    - Configurações do aplicativo e cadeias [de conexão específicas do slot](#manage-settings) (se aplicável)
    - Configurações de [implantação contínua](../app-service/deploy-continuous-deployment.md) (se habilitada)
    - Configurações de [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md) (se habilitado)

1. **Aguarde as reinicializações e a disponibilidade:** A permuta espera que cada instância no slot de origem conclua sua reinicialização e esteja disponível para solicitações. Se alguma instância não for reiniciada, a operação de permuta reverterá todas as alterações no slot de origem e interromperá a operação.

1. **Roteamento de atualização:** Se todas as instâncias no slot de origem forem ativadas com êxito, os dois slots concluirão a permuta alternando as regras de roteamento. Após essa etapa, o slot de destino (por exemplo, o slot de produção) tem o aplicativo que foi anteriormente ativado no slot de origem.

1. **Repetir operação:** Agora que o slot de origem tem o aplicativo de pré-permuta anteriormente no slot de destino, conclua a mesma operação aplicando todas as configurações e reiniciando as instâncias do slot de origem.

Tenha em mente os seguintes pontos:

- Em qualquer ponto da operação de permuta, a inicialização dos aplicativos trocados ocorre no slot de origem. O slot de destino permanece online enquanto o slot de origem está preparado, independentemente de a troca ser bem-sucedida ou falhar.

- Para trocar um slot de preparo pelo slot de produção, verifique se o slot de produção é *sempre* o slot de destino. Dessa forma, a operação de permuta não afeta seu aplicativo de produção.

- As configurações relacionadas a origens e associações de eventos devem ser definidas como [configurações de slot de implantação](#manage-settings) *antes de iniciar uma troca*. Marcá-los como "adesivos" antecipadamente garante que os eventos e as saídas sejam direcionados para a instância apropriada.

## <a name="manage-settings"></a>Gerenciar configurações

Algumas definições de configuração são específicas do slot. A lista a seguir detalha quais configurações são alteradas quando você permuta os slots e que permanecem os mesmos.

**Configurações específicas do slot**:

* Pontos de extremidade de publicação
* Nomes de domínio personalizados
* Certificados não públicos e configurações de TLS/SSL
* Configurações de dimensionamento
* Agendadores de Trabalhos Web
* Restrições de IP
* Always On
* Configurações de Diagnóstico
* CORS (Compartilhamento de Recursos entre Origens)

**Configurações não específicas do slot**:

* Configurações gerais, como versão do Framework, 32/64 bits, Web Sockets
* Configurações do aplicativo (podem ser configuradas para fixarem-se a um slot)
* Cadeias de conexão (podem ser configuradas para fixarem-se a um slot)
* Mapeamentos de manipulador
* Certificados públicos
* Conteúdo de Trabalhos Web
* Conexões híbridas *
* Integração de rede virtual *
* Pontos de extremidade de serviço *
* Rede de distribuição de conteúdo do Azure *

Os recursos marcados com um asterisco (*) estão planejados para serem desalternados. 

> [!NOTE]
> Determinadas configurações de aplicativo que se aplicam a configurações não alternadas também são trocadas. Por exemplo, como as configurações de diagnóstico não são trocadas, as configurações de aplicativo relacionadas, como `WEBSITE_HTTPLOGGING_RETENTION_DAYS` e `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` também não são trocadas, mesmo que não apareçam como configurações de slot.
>

### <a name="create-a-deployment-setting"></a>Criar uma configuração de implantação

Você pode marcar configurações como uma configuração de implantação, o que o torna "adesivo". Uma configuração adesiva não alterna com a instância do aplicativo.

Se você criar uma configuração de implantação em um slot, certifique-se de criar a mesma configuração com um valor exclusivo em qualquer outro slot que esteja envolvido em uma permuta. Dessa forma, embora o valor de uma configuração não mude, os nomes de configuração permanecem consistentes entre os slots. Essa consistência de nome garante que seu código não tente acessar uma configuração definida em um slot, mas não em outra.

Use as seguintes etapas para criar uma configuração de implantação:

1. Navegue até **Slots de implantação** no aplicativo de funções e selecione o nome do slot.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Localize os slots no portal do Azure." border="true":::

1. Selecione **configuração** e, em seguida, selecione o nome da configuração que você deseja colocar com o slot atual.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Defina a configuração de aplicativo para um slot no portal do Azure." border="true":::

1. Selecione a **configuração do slot de implantação** e, em seguida, selecione **OK**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Defina a configuração do slot de implantação." border="true":::

1. Quando a seção de configuração desaparecer, selecione **salvar** para manter as alterações

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Salve a configuração do slot de implantação." border="true":::

## <a name="deployment"></a>Implantação

Os slots ficam vazios quando você cria um slot. Você pode usar qualquer uma das [tecnologias de implantação com suporte](./functions-deployment-technologies.md) para implantar seu aplicativo em um slot.

## <a name="scaling"></a>Scaling

Todos os slots são dimensionados para o mesmo número de trabalhadores que o slot de produção.

- Para planos de consumo, o slot é dimensionado conforme o aplicativo de funções é dimensionado.
- Para planos do serviço de aplicativo, o aplicativo é dimensionado para um número fixo de trabalhadores. Os slots são executados no mesmo número de trabalhadores que o plano do aplicativo.

## <a name="add-a-slot"></a>Adicionar um slot

Você pode adicionar um slot por meio da [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create) ou por meio do Portal. As etapas a seguir demonstram como criar um novo slot no Portal:

1. Navegue até seu aplicativo de funções.

1. Selecione **Slots de implantação** e, em seguida, selecione **+ Adicionar slot**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Adicionar Azure Functions slot de implantação." border="true":::

1. Digite o nome do slot e selecione **Adicionar**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Nomeie o slot de implantação Azure Functions." border="true":::

## <a name="swap-slots"></a>Slots de permuta

Você pode trocar os slots por meio da [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap) ou por meio do Portal. As etapas a seguir demonstram como trocar slots no Portal:

1. Navegue até o aplicativo de função.
1. Selecione **Slots de implantação** e, em seguida, selecione **alternar**.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Captura de tela que mostra a página ' slot de implantação ' com a ação ' Adicionar slot ' selecionada." border="true":::

1. Verifique os parâmetros de configuração para sua permuta e selecione **alternar**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Troque o slot de implantação." border="true":::

A operação pode demorar um pouco enquanto a operação de permuta está em execução.

## <a name="roll-back-a-swap"></a>Reverter uma permuta

Se uma troca resultar em um erro ou se você simplesmente quiser "desfazer" uma troca, poderá reverter para o estado inicial. Para retornar ao estado de pré-atualização, faça outra troca para reverter a troca.

## <a name="remove-a-slot"></a>Remover um slot

Você pode remover um slot por meio da [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete) ou por meio do Portal. As etapas a seguir demonstram como remover um slot no Portal:

1. Navegue até **Slots de implantação** no aplicativo de funções e selecione o nome do slot.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Localize os slots no portal do Azure." border="true":::

1. Selecione **Excluir**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Captura de tela que mostra a página ' visão geral ' com a ação ' excluir ' selecionada." border="true":::

1. Digite o nome do slot de implantação que você deseja excluir e, em seguida, selecione **excluir**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Exclua o slot de implantação no portal do Azure." border="true":::

1. Feche o painel confirmação de exclusão.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Confirmação de exclusão do slot de implantação." border="true":::

## <a name="automate-slot-management"></a>Automatizar o gerenciamento de Slots

Usando o [CLI do Azure](/cli/azure/functionapp/deployment/slot), você pode automatizar as seguintes ações para um slot:

- [create](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create)
- [delete](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete)
- [list](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-list)
- [permuta](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap)
- [troca automática](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Alterar plano do serviço de aplicativo

Com um aplicativo de funções que está sendo executado em um plano do serviço de aplicativo, você pode alterar o plano do serviço de aplicativo subjacente para um slot.

> [!NOTE]
> Não é possível alterar o plano do serviço de aplicativo de um slot no plano de consumo.

Use as etapas a seguir para alterar o plano do serviço de aplicativo de um slot:

1. Navegue até **Slots de implantação** no aplicativo de funções e selecione o nome do slot.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Localize os slots no portal do Azure." border="true":::

1. Em **plano do serviço de aplicativo**, selecione **Alterar plano do serviço de aplicativo**.

1. Selecione o plano para o qual você deseja atualizar ou crie um novo plano.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Altere o plano do serviço de aplicativo no portal do Azure." border="true":::

1. Selecione **OK**.

## <a name="limitations"></a>Limitações

Azure Functions slots de implantação têm as seguintes limitações:

- O número de slots disponíveis para um aplicativo depende do plano. O plano de consumo só é permitido para um slot de implantação. Slots adicionais estão disponíveis para aplicativos em execução no plano do serviço de aplicativo.
- A troca de um slot redefine chaves para aplicativos que têm uma `AzureWebJobsSecretStorageType` configuração de aplicativo igual a `files` .
- Os slots não estão disponíveis para o plano de consumo do Linux.

## <a name="support-levels"></a>Níveis de suporte

Há dois níveis de suporte para slots de implantação:

- **Disponibilidade geral (GA)**: suporte completo e aprovado para uso em produção.
- Versão **prévia**: ainda não tem suporte, mas é esperado para alcançar o status de GA no futuro.

| Sistema operacional/plano de hospedagem           | Nível de suporte     |
| ------------------------- | -------------------- |
| Consumo do Windows       | Disponibilidade Geral |
| Windows Premium           | Disponibilidade Geral  |
| Windows dedicado         | Disponibilidade Geral |
| Consumo do Linux         | Sem suporte          |
| Linux Premium             | Disponibilidade Geral  |
| Linux dedicado           | Disponibilidade Geral |

## <a name="next-steps"></a>Próximas etapas

- [Tecnologias de implantação no Azure Functions](./functions-deployment-technologies.md)
