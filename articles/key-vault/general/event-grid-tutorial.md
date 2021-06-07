---
title: Receber notificações do cofre de chaves e responder a elas com a Grade de Eventos do Azure
description: Saiba como integrar o Key Vault com a Grade de Eventos do Azure.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e46161812d122a1d5647e8589c58f9528578b878
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749826"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>Receber notificações do cofre de chaves e responder a elas com a Grade de Eventos do Azure

A integração do Azure Key Vault com a Grade de Eventos do Azure permite que o usuário seja notificado quando o status de um segredo armazenado em um cofre de chaves é alterado. Para obter uma visão geral deste recurso, confira [Monitoramento do Key Vault com a Grade de Eventos](event-grid-overview.md).

Esse guia descreve como receber notificações de Key Vault por meio da Grade de Eventos do Azure e como responder a alterações de status pela Automação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um cofre de chaves na assinatura do Azure. Você pode criar rapidamente um novo cofre de chaves seguindo as etapas em [Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure](../secrets/quick-create-cli.md).

## <a name="concepts"></a>Conceitos

A Grade de Eventos é um serviço de eventos para a nuvem. Ao seguir as etapas neste guia, você assinará eventos para o cofre de chaves e roteará eventos para a Automação. Quando um dos segredos no cofre de chaves está prestes a expirar, a Grade de Eventos é notificada sobre a alteração de status e faz um HTTP POST para o ponto de extremidade. Um web hook dispara então uma execução do script de um PowerShell da Automação.

![Fluxograma HTTP POST](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Criar uma conta de Automação

Crie uma conta da Automação por meio do [portal do Azure](https://portal.azure.com).

1.  Acesse portal.azure.com e faça logon em sua assinatura.

1.  Na caixa de pesquisa, digite **Contas de Automação**.

1.  Na seção **Serviços** do menu suspenso da barra de pesquisa, selecione **Contas de Automação**.

1.  Selecione **Adicionar**.

    ![Painel de Contas de Automação](../media/event-grid-tutorial-2.png)

1.  Insira as informações necessárias na folha **Adicionar Conta de Automação** e selecione **Criar**.

## <a name="create-a-runbook"></a>Criar um runbook

Depois que sua conta da Automação estiver pronta, crie um runbook.

![Criar uma interface do usuário de runbook](../media/event-grid-tutorial-3.png)

1.  Selecione a conta de Automação que você acabou de criar.

1.  Selecione **Runbook**, em **Automação de Processo**.

1.  Selecione **Criar um runbook**.

1.  Atribua um nome ao runbook e selecione **PowerShell** como o tipo do runbook.

1.  Clique no runbook que você criou e selecione o botão **Editar**.

1.  Insira o código a seguir (para fins de teste) e selecione o botão **Publicar**. Essa ação retornará o resultado da solicitação POST recebida.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Publicar interface do usuário do runbook](../media/event-grid-tutorial-4.png)

## <a name="create-a-webhook"></a>Criar um webhook

Crie um webhook para disparar o runbook recém-criado.

1.  Selecione **Webhooks** na seção de **Recursos** do runbook que você acabou de publicar.

1.  Selecione **Adicionar webhook**.

    ![Botão Adicionar webhook](../media/event-grid-tutorial-5.png)

1.  Selecione **Criar novo Webhook**.

1. Nomeie o webhook, defina uma data de validade e copie a URL.

    > [!IMPORTANT] 
    > Você não pode exibir a URL depois de criá-la. Lembre-se de salvar uma cópia em uma localização segura, em que você possa acessá-la no restante deste guia.

1. Selecione **Parâmetros e configurações de execução** e selecione **OK**. Não insira nenhum parâmetro. Isso habilitará o botão **Criar**.

1. Selecione **OK** e, então, selecione **Criar**.

    ![Interface do usuário de Criar novo Webhook](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Criar uma assinatura na Grade de Eventos

Crie uma assinatura de Grade de Eventos por meio do [portal do Azure](https://portal.azure.com).

1.  Vá para o cofre de chaves e selecione a guia **Eventos**.

    ![Guia Eventos no portal do Azure](../media/event-grid-tutorial-7.png)

1.  Clique no botão **Assinatura de evento**.

1.  Crie um nome descritivo para a assinatura.

1.  Escolha **Esquema da Grade de Eventos**.

1.  O **Recurso de Tópico** deve ser o cofre de chaves cujas alterações de status você deseja monitorar.

1.  Para **Filtrar para Tipos de Evento**, deixe todas as opções selecionadas (**9 selecionados**).

1.  Para **Tipo de Ponto de Extremidade**, selecione **Webhook**.

1.  Escolha **Selecionar um ponto de extremidade**. No novo painel de contexto, cole a URL do webhook da etapa [Criar um webhook](#create-a-webhook) no campo **Ponto de Extremidade do Assinante**.

1.  Selecione **Confirmar Seleção** no painel de contexto.

1.  Selecione **Criar**.

    ![Criar assinatura de evento](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>Testar e verificar

Verifique se a sua assinatura da Grade de Eventos está definida adequadamente. Esse teste pressupõe que você assinou a notificação "Nova Versão Secreta Criada" em [Criar uma assinatura de Grade de Eventos](#create-an-event-grid-subscription) e que você tem as permissões necessárias para criar uma nova versão de um segredo em um cofre de chaves.

![Testar a configuração da assinatura de Grade de Eventos](../media/event-grid-tutorial-9.png)

![Painel de criar um segredo](../media/event-grid-tutorial-10.png)

1.  Vá para o seu cofre de chaves no portal do Azure.

1.  Crie um novo segredo. Para fins de teste, defina a data de vencimento como o dia seguinte.

1.  Na guia **Eventos** no cofre de chaves, selecione a assinatura da grade de eventos que você criou.

1.  Em **Métricas**, confira se um evento foi capturado. Dois eventos são esperados: SecretNewVersion e SecretNearExpiry. Esses eventos validam que a Grade de Eventos capturou com êxito a alteração de status do segredo em seu cofre de chaves.

    ![Painel de métricas: verificar eventos capturados](../media/event-grid-tutorial-11.png)

1.  Vá para sua conta de Automação.

1.  Selecione a guia **Runbooks** e selecione o runbook que você criou.

1.  Selecione a guia **Webhooks** e confirme se o carimbo de data/hora "disparado pela última vez" está dentro de 60 segundos de quando você criou o novo segredo. Esse resultado confirma que a Grade de Eventos fez um POST no webhook com os detalhes do evento da alteração de status no cofre de chaves e que o webhook foi disparado.

    ![Guia WebHooks, carimbo de data/hora do último disparo](../media/event-grid-tutorial-12.png)

1. Retorne ao seu runbook e selecione a guia **Visão geral**.

1. Examine a lista **Trabalhos Recentes**. Você deverá ver que um trabalho foi criado e que o status dele é concluído. Isso confirma que o webhook disparou o runbook para iniciar a execução do respectivo script.

    ![Lista Trabalhos Recentes de Webhook](../media/event-grid-tutorial-13.png)

1. Selecione o trabalho recente e examine a solicitação POST enviada da Grade de Eventos para o webhook. Examine o JSON e verifique se os parâmetros para o cofre de chaves e o tipo de evento estão corretos. Se o parâmetro "tipo de evento" no objeto JSON corresponder ao evento que ocorreu no Key Vault (neste exemplo, Microsoft.KeyVault.SecretNearExpiry), o teste foi bem-sucedido.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="you-cant-create-an-event-subscription"></a>Não é possível iniciar uma assinatura de evento.

Registre novamente a Grade de Eventos e o provedor do Key Vault em seus provedores de recursos de assinatura do Azure. Consulte [Provedores e tipos de recursos do Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Próximas etapas

Parabéns! Se você seguiu corretamente todas as etapas, agora você está pronto para responder programaticamente às alterações de status dos segredos armazenados no seu cofre de chaves.

Você pode usar agora esse recurso de notificação caso você esteja usando um sistema baseado em sondagem para pesquisar alterações de status nos segredos em seus Key Vaults. Você também poderá substituir o script de teste em seu runbook por código para renovar programaticamente seus segredos quando eles estiverem prestes a expirar.

Saiba mais:


- Visão geral: [Monitoramento do Key Vault com a Grade de Eventos do Azure](event-grid-overview.md)
- Como fazer: [Receber emails quando o status do cofre de chaves secreto é alterado](event-grid-logicapps.md)
- [Esquema de eventos da Grade de Eventos do Azure para o Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Visão geral do Azure Key Vault](overview.md)
- [Visão geral da Grade de Eventos do Azure](../../event-grid/overview.md)
- [Visão geral da Automação do Azure](../../automation/index.yml)
