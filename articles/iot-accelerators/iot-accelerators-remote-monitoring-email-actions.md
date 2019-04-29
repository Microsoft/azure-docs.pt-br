---
title: Envie um e-mail ação dentro de monitoramento remoto – Azure | Microsoft Docs
description: Este guia de instruções mostra como adicionar uma ação de e-mail a uma regra nova ou existente.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 693da06ce14a4854d5db49f588fa29d791060166
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61445535"
---
# <a name="add-an-email-action"></a>Adicionar uma ação de e-mail

Ações de e-mail ajudam a garantir que você nunca perca alertas. Você pode adicionar uma ação de e-mail a uma regra existente ou ao criar uma nova regra.

Para concluir as etapas deste guia prático, você precisa de uma instância implantada do acelerador da solução de monitoramento remoto na sua assinatura do Azure.

Para criar ou modificar uma regra, você deve ser um [**Administrador**, ou ter as permissões corretas](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Editar uma regra existente

Siga estas etapas para adicionar uma ação de e-mail a uma regra existente:

1. Navegue até a solução de monitoramento remoto.

1. No **Painel**, navegue até a página **Regras**:

    ![Página de regras](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Clique na caixa de seleção ao lado da regra existente para modificar e, em seguida, clique em **editar** na parte superior. Um painel editável **Regra** é exibido.

1. Na seção **Ação**, alterne **E-mail ativado** para **On**.

1. Na primeira vez em que você ativar uma ação de email no acelerador de solução, deverá [entrar no Outlook](#outlook).

1. Digite um endereço de e-mail na caixa do destinatário e pressione a tecla **Enter** para cada endereço de e-mail a ser adicionado:

    ![Entrada de endereço](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Digite um assunto para o e-mail.

1. Insira quaisquer notas adicionais para os destinatários do e-mail como texto simples. Você pode usar a formatação HTML se [Editar o modelo de e-mail](#htmledit).

1. Certifique-se de que o **Status da regra** é definido como **habilitado**.

1. Clique em **Aplicar**.

## <a name="create-a-new-rule"></a>Criar uma nova regra

Siga estas etapas para adicionar uma ação de e-mail ao criar uma nova regra:

1. Navegue até a solução de monitoramento remoto.

1. No **Painel**, navegue até a página **Regras**:

    ![Página de regras](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Siga as etapas na [Criação de uma seção de regras](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Siga as etapas na seção [Criar uma regra avançada](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) até o ponto em que você define um **nível de gravidade**. Não clique em **aplicar** ainda.

1. Na seção **Ação**, alterne **E-mail ativado** para **On**.

1. Na primeira vez em que você ativar uma ação de email no acelerador de solução, deverá [entrar no Outlook](#outlook).

1. Digite um endereço de e-mail na caixa do destinatário e pressione a tecla **Enter** para cada endereço de e-mail a ser adicionado:

    ![Entrada de endereço](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Digite um assunto para o e-mail.

1. Insira quaisquer notas adicionais para os destinatários do e-mail como texto simples. Você pode usar a formatação HTML se [Editar o modelo de e-mail](#htmledit).

1. Certifique-se de que o **Status da regra** é definido como **habilitado**.

1. Clique em **Aplicar**.

Sua regra com uma ação de e-mail está ativada agora. Cada vez que a ação é acionada, um novo e-mail é enviado para os destinatários.

## Entre no Outlook <a name="outlook"></a>

Na primeira vez em que você ativar uma ação de e-mail no acelerador de solução, será necessário entrar no Outlook. Esta ação configura a conta de e-mail que envia as notificações por e-mail.

> [!NOTE]
> Você deve criar uma conta do Outlook específica apenas para as notificações do acelerador de solução e usar essa conta ao ativar sua primeira ação de e-mail.

### <a name="contributor-role-outlook-setup"></a>Instalação do Outlook de função de Colaborador

Se alguém na função **contribuidor** na assinatura implantou o acelerador de solução, o aplicativo não tem permissões suficientes para configurar e verificar ações de e-mail por meio da interface do usuário da Web.

Antes de começar, crie uma conta do Outlook para usar para enviar notificações por e-mail de seu acelerador de solução.

As etapas a seguir mostram como configurar e verificar as ações de e-mail manualmente:

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Navegue até o Grupo de Recursos para o seu acelerador de solução.

1. Clique o **conector do Office 365**:

    ![Conexão da API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector.png)

1. Clique no banner para começar o processo de autorização:

    ![Autorizar](./media/iot-accelerators-remote-monitoring-email-actions/connector.png)

1. Clique em **Autorizar**. Você é solicitado a fazer login. A conta que você usa para fazer login deve ser o endereço de e-mail que o aplicativo usa para enviar notificações por e-mail:

    ![Botão Autorizar](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Clique em **Salvar** na parte inferior. Sua autorização será bem-sucedida se o banner desaparecer.

1. Para alterar o endereço de e-mail de onde as notificações são enviadas, clique em **Editar conexão da API**.

    ![Alterar e-mail](./media/iot-accelerators-remote-monitoring-email-actions/editemail.png)

### <a name="owner-role-outlook-setup"></a>Configuração do Outlook da função do proprietário

Se alguém na função de **proprietário** na assinatura implantou o acelerador de solução, o aplicativo poderá verificar se as ações de e-mail foram configuradas corretamente por meio da interface do usuário da Web.

Antes de começar, crie uma conta do Outlook para usar para enviar notificações por e-mail de seu acelerador de solução.

As etapas a seguir ajudam você a fazer login e configurar ações de e-mail:

1. Clique para entrar no Outlook. Você é levado para o portal do Azure:

   ![Entrar no Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Clique em **Autorizar**. Você é solicitado a fazer login. A conta que você usa para fazer login deve ser o endereço de e-mail que o aplicativo usa para enviar notificações por e-mail:

1. Clique em **Salvar**. Volte para o acelerador de solução e atualize a página.

1. Se você configurou com sucesso a notificação por e-mail, verá esta mensagem:

   ![Sinal de sucesso no Outlook](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## Personalize o e-mail HTML <a name="htmledit"></a>

Fora da caixa, o acelerador de solução de Monitoramento Remoto fornece um modelo HTML básico para e-mails de ação. O modelo de e-mail usa valores das configurações de ação de e-mail. Aqui está um exemplo de e-mail:

![exemplo de e-mail](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate.png)

As etapas a seguir mostram como editar o modelo de e-mail em HTML. Por exemplo, você pode incluir mais informações ou adicionar imagens personalizadas:

1. Clone o repositório GitHub de Java ou .NET Remote Monitoring:

1. Navegue até o local do modelo de e-mail:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Você pode adicionar ou remover quaisquer parâmetros neste modelo para personalizar a mensagem. Você também pode adicionar, remover ou substituir chamadas conforme necessário:

    Por exemplo, no código .NET: `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Por exemplo, no código Java:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parâmetros no modelo assumem a forma de `${...}`. Para excluir um parâmetro, exclua a linha necessária. Para adicionar um parâmetro, adicione uma linha com o valor a ser inserido.

1. Para adicionar imagens ou texto personalizado, atualize o arquivo de EmailTemplate.HTML diretamente.

## <a name="throttling"></a>Limitação

O acelerador de solução de monitoramento remoto usa o Outlook para enviar notificações por e-mail. O Outlook limita o número de e-mails enviados para [30 e-mails por 1 minuto](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). Clientes de email, recebendo emails também podem limitar o número de emails recebidos por minuto. Verifique com seu cliente de email específicos sobre as limitações. Quando você configura uma notificação por e-mail para uma regra, a regra deve calcular valores médios durante um período de pelo menos um minuto e não usar valores instantâneos:

![Cálculo médio](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Próximas etapas

Este guia mostrou como adicionar uma ação de e-mail a uma regra nova ou existente em uma solução de monitoramento remoto. O guia também mostrou a você e como editar o HTML que define o formato da mensagem.

A próxima etapa sugerida é aprender [ como usar alertas e corrigir problemas de dispositivos ](iot-accelerators-remote-monitoring-maintain.md).
