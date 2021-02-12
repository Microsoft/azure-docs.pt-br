---
title: Solicitar acesso just-in-time
description: Descreve como os editores de aplicativos gerenciados do Azure solicitam acesso just-in-time a um aplicativo gerenciado.
author: MSEvanhi
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: evanhi
ms.openlocfilehash: f839eb04e0c036b5059e35fac16d9c702646cbd7
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435681"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Habilitar e solicitar o acesso just-in-time para aplicativos gerenciados do Azure

Os consumidores do seu aplicativo gerenciado podem ser relutados para conceder a você acesso permanente ao grupo de recursos gerenciado. Como um editor de um aplicativo de gerente, você pode preferir que os consumidores saibam exatamente quando você precisar acessar os recursos gerenciados. Para fornecer aos consumidores maior controle sobre a concessão de acesso a recursos gerenciados, os aplicativos gerenciados do Azure fornecem um recurso chamado acesso JIT (just-in-time). Esse recurso está atualmente na visualização.

O acesso JIT permite que você solicite acesso elevado a recursos de um aplicativo gerenciado para solução de problemas ou manutenção. Você sempre tem acesso somente leitura aos recursos, mas, para um período de tempo específico, você pode ter mais acesso.

O fluxo de trabalho para conceder acesso é:

1. Você adiciona um aplicativo gerenciado ao Marketplace e especifica que o acesso JIT está disponível.

1. Durante a implantação, o consumidor habilita o acesso JIT para essa instância do aplicativo gerenciado.

1. Após a implantação, o consumidor pode alterar as configurações de acesso JIT.

1. Você envia uma solicitação de acesso quando precisa solucionar problemas ou atualizar os recursos gerenciados.

1. O consumidor aprova sua solicitação.

Este artigo se concentra nas ações que os editores adotam para habilitar o acesso JIT e enviar solicitações. Para saber mais sobre a aprovação de solicitações de acesso JIT, consulte [aprovar o acesso just-in-time em aplicativos gerenciados do Azure](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Adicionar etapa de acesso JIT à interface do usuário

Em seu CreateUiDefinition.jsno arquivo, inclua uma etapa que permite aos consumidores habilitar o acesso JIT. Para dar suporte à funcionalidade JIT para sua oferta, adicione o conteúdo a seguir à sua CreateUiDefinition.jsno arquivo.

Em "etapas":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```

Em "saídas":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> O acesso JIT está em versão prévia. O esquema para a configuração de JIT pode mudar em iterações futuras.

## <a name="enable-jit-access"></a>Habilitar acesso JIT

Ao criar sua oferta no Partner Center, certifique-se de habilitar o acesso JIT.

1. Entre no portal do Marketplace comercial no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

1. Para obter orientações sobre como criar um novo aplicativo gerenciado, siga as etapas em [criar uma oferta de aplicativo do Azure](../../marketplace/create-new-azure-apps-offer.md).

1. Na página **configuração técnica** , marque a caixa de seleção **habilitar acesso JIT (just-in-time)** .

   :::image type="content" source="./media/request-just-in-time-access/enable-just-in-time-access.png" alt-text="Habilitar acesso Just-In-Time":::

Você adicionou uma etapa de configuração JIT à sua interface do usuário e habilitou o acesso JIT na oferta do Marketplace comercial. Quando os consumidores implantam seu aplicativo gerenciado, eles podem [ativar o acesso JIT para sua instância](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Solicitar acesso

Quando você precisar acessar os recursos gerenciados do consumidor, envie uma solicitação para uma função, hora e duração específicas. O consumidor deve, então, aprovar a solicitação.

Para enviar uma solicitação de acesso JIT:

1. Selecione **acesso JIT** para o aplicativo gerenciado que você precisa acessar.

1. Selecione **funções qualificadas** e selecione **Ativar** na coluna ação para a função desejada.

   ![Ativar solicitação de acesso](./media/request-just-in-time-access/send-request.png)

1. No formulário **Ativar função** , selecione uma hora de início e a duração da sua função como ativa. Selecione **Ativar** para enviar a solicitação.

   ![Ativar o acesso](./media/request-just-in-time-access/activate-access.png) 

1. Exiba as notificações para ver que a nova solicitação JIT foi enviada com êxito ao consumidor.

   ![Notificação](./media/request-just-in-time-access/in-progress.png)

   Agora, você deve aguardar até que o consumidor [aprove sua solicitação](approve-just-in-time-access.md#approve-requests).

1. Para exibir o status de todas as solicitações JIT de um aplicativo gerenciado, selecione **acesso JIT** e **histórico de solicitações**.

   ![Exibir status](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problemas conhecidos

A ID da entidade de segurança da conta que solicita acesso JIT deve ser incluída explicitamente na definição do aplicativo gerenciado. A conta não pode ser incluída apenas por meio de um grupo especificado no pacote. Essa limitação será corrigida em uma versão futura.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a aprovação de solicitações de acesso JIT, consulte [aprovar o acesso just-in-time em aplicativos gerenciados do Azure](approve-just-in-time-access.md).
