---
title: Email quando o status do Key Vault do segredo é alterado
description: Guia para usar Aplicativos Lógicos para responder a alterações de segredo do Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: e6ec06e3df87e0ad0e50efca24439435d655f6a1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752094"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Usar Aplicativos Lógicos para receber email sobre alterações de status de segredos do Key Vault

Neste guia, você aprenderá a responder a eventos do Azure Key Vault que são recebidos por meio da [Grade de Eventos do Azure](../../event-grid/index.yml) usando os [Aplicativos Lógicos do Azure](../../logic-apps/index.yml). No final, você terá um Aplicativo Lógico do Azure configurado para enviar um email de notificação sempre que um segredo é criado no Azure Key Vault.

Para obter uma visão geral da integração entre o Azure Key Vault e a Grade de Eventos do Azure, confira [Monitoramento do Key Vault com a Grade de Eventos do Azure](event-grid-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de email de qualquer provedor de email com suporte pelos Aplicativos Lógicos do Azure Apps (como Office 365 Outlook). Essa conta de email é usada para enviar as notificações de eventos. Para obter uma lista completa de conectores de Aplicativos Lógicos com suporte, consulte a [Visão geral dos conectores](/connectors)
- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um cofre de chaves na assinatura do Azure. Você pode criar rapidamente um novo cofre de chaves seguindo as etapas em [Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure](../secrets/quick-create-cli.md).
- A Grade de Eventos registrada como um provedor de recursos, confira [Registros de provedores de recursos](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="create-a-logic-app-via-event-grid"></a>Criar um aplicativo lógico por meio da Grade de Eventos

Primeiro, crie um aplicativo lógico com o manipulador de grade de eventos e assine os eventos "SecretNewVersionCreated" do Azure Key Vault.

Para criar uma assinatura da Grade de Eventos do Azure, siga as etapas a seguir:

1. Na portal do Azure, vá para o cofre de chaves, selecione **Eventos > Introdução** e clique em **Aplicativos Lógicos**

    
    ![Key Vault -página de eventos](../media/eventgrid-logicapps-kvsubs.png)

1. Em **Designer de Aplicativos Lógicos** valide a conexão e clique em **Continuar** 
 
    ![Designer de Aplicativo Lógico - conexão](../media/eventgrid-logicappdesigner1.png)

1. Na tela **Quando ocorrer um evento de recurso**, faça o seguinte:
    - Deixe **Assinatura** e **Nome do Recurso** como padrão.
    - Selecione **Microsoft.KeyVault.vaults** para **Tipo de Recurso**.
    - Selecione **Microsoft.KeyVault.SecretNewVersionCreated** para **Item do Tipo de Evento - 1**.

    ![Designer de Aplicativo Lógico - manipulador de eventos](../media/eventgrid-logicappdesigner2.png)

1. Selecione **+ Nova Etapa** Isso abrirá uma janela para Escolher uma ação.
1. Pesquisar por **Email**. Com base no seu provedor de email, localize e selecione o conector correspondente. Este tutorial usa o **Office 365 Outlook**. As etapas para outros provedores de email são semelhantes.
1. Selecione a ação **Enviar um email (V2)**.

   ![Designer de Aplicativo Lógico – enviar email](../media/eventgrid-logicappdesigner3.png)

1. Compile seu modelo de email:
    - **Para:** insira o endereço de email para receber os emails de notificação. Para este tutorial, use uma conta de email que você possa acessar para testes.
    - **Assunto** e **Corpo**: escreva o texto para o seu email. Selecione as propriedades JSON na ferramenta de seletor para incluir conteúdo dinâmico com base nos dados do evento. Você pode recuperar os dados do evento usando `@{triggerBody()?['Data']}`.

    Seu modelo de email poderá ser semelhante a este exemplo.

    ![Designer de Aplicativo Lógico – corpo do email](../media/eventgrid-logicappdesigner4.png)

8. Clique em **Salvar como**.
9. Insira um **nome** para o novo aplicativo lógico e clique em **Criar**.
    
    ![Designer de Aplicativo Lógico – criar](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testar e verificar

1.  Vá para o cofre de chaves no portal do Azure e selecione **Eventos > Assinaturas de Eventos**.  Verificar se a nova assinatura foi criada
    
    ![Designer de Aplicativo Lógico – testar e verificar](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Vá até o Key Vault, selecione **Segredos** e **+ Gerar/Importar**. Crie um novo segredo para fins de teste, nomeie a chave e mantenha os parâmetros restantes em suas configurações padrão.

    ![Key Vault - criar segredo](../media/eventgrid-logicapps-kv-create-secret.png)

1. Na tela **Criar um segredo** forneça qualquer nome, qualquer valor e selecione **Criar**.

Quando o segredo for criado, um email será recebido nos endereços configurados.

## <a name="next-steps"></a>Próximas etapas

- Visão geral: [Monitoramento do Key Vault com a Grade de Eventos do Azure](event-grid-overview.md)
- Como fazer: [Rotear notificações de Key Vault para Automação do Azure](event-grid-tutorial.md).
- [Esquema de eventos da Grade de Eventos do Azure para o Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- Saiba mais sobre a [Grade de Eventos do Azure](../../event-grid/index.yml).
- Saiba mais sobre o [recurso de Aplicativos Lógicos do Serviço de Aplicativo do Azure](../../logic-apps/index.yml).