---
title: Conectar Azure Key Vault logs de diagnóstico ao Azure Sentinel
description: Saiba como usar Azure Policy para se conectar Azure Key Vault logs de diagnóstico ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 00e634ec62d1fda4b1f2763a8c69b8e950d54beb
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505181"
---
# <a name="connect-azure-key-vault-diagnostics-logs"></a>Conectar Azure Key Vault logs de diagnóstico

Azure Key Vault é um serviço de nuvem para armazenar e acessar segredos com segurança. Um segredo é qualquer coisa que você deseja controlar rigidamente o acesso ao, como chaves de API, senhas, certificados ou chaves de criptografia.

Esse conector permite transmitir seus Azure Key Vault logs de diagnóstico para o Azure Sentinel, permitindo que você monitore continuamente a atividade em todas as suas instâncias.

Saiba mais sobre como [monitorar Azure Key Vault](../azure-monitor/insights/key-vault-insights-overview.md) e sobre a [telemetria de diagnóstico de Azure Key Vault](../key-vault/general/logging.md).

## <a name="prerequisites"></a>Pré-requisitos

Para ingerir Azure Key Vault logs no Azure sentinela:

- Você deve ter permissões de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Para usar Azure Policy para aplicar uma política de streaming de log a recursos de Azure Key Vault, você deve ser atribuído à função de proprietário para o escopo de atribuição de política.

## <a name="connect-to-azure-key-vault"></a>Conectar-se ao Azure Key Vault

Esse conector usa Azure Policy para aplicar uma única configuração de streaming de log de Azure Key Vault a uma coleção de instâncias, definida como um escopo. Você pode ver os tipos de log ingeridos de Azure Key Vault no lado esquerdo da página do conector, em **tipos de dados**.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Selecione **Azure Key Vault** na Galeria de conectores de dados e, em seguida, selecione a **página abrir conector** no painel de visualização.

1. Na seção **configuração** da página conector, expanda **Habilitar logs de diagnóstico em Azure Key Vault**.

1. Selecione o botão **iniciar Azure Policy atribuição do assistente** .

    O assistente de atribuição de política é aberto, pronto para criar uma nova política chamada **implantar-definir configurações de diagnóstico para Azure Key Vault para log Analytics espaço de trabalho**.

    1. Na guia **noções básicas** , clique no botão com os três pontos em **escopo** para selecionar sua assinatura (e, opcionalmente, um grupo de recursos). Também é possível adicionar uma descrição.

    1. Na guia **parâmetros** , escolha seu espaço de trabalho do Azure Sentinel na lista suspensa **log Analytics espaço de trabalho** . Os campos restantes da lista suspensa representam os tipos de log de diagnóstico disponíveis. Deixe marcado como "verdadeiro" todos os tipos de log que você deseja ingerir.

    1. Para aplicar a política em seus recursos existentes, selecione a guia **correção** e marque a caixa de seleção **criar uma tarefa de correção** .

    1. Na guia **Revisar + criar**, clique em **Criar**. Agora, sua política está atribuída ao escopo escolhido.

> [!NOTE]
>
> Com esse conector de dados específico, os indicadores de status de conectividade (uma faixa de cores na Galeria de conectores de dados e os ícones de conexão ao lado dos nomes dos tipos de dados) serão mostrados como *conectados* (verde) somente se os dados tiverem sido ingeridos em algum momento nas duas últimas semanas. Depois que duas semanas tiverem passado sem ingestão de dados, o conector será exibido como sendo desconectado. O momento em que mais dados surgem, o status *conectado* retornará.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar Azure Key Vault ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
