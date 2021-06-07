---
title: Conectar dados de proteção contra DDoS do Azure ao Azure Sentinel
description: Saiba como ingerir dados de proteção contra DDoS do Azure no Azure Sentinel, para que você possa exibi-lo, analisá-lo e analisá-lo.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 8089b1e74e88db81c1c15ad2cbf2072abcfff241
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621338"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Conectar dados da proteção contra DDoS do Azure

Ataques de DDoS (negação de serviço distribuído) tentam esgotar os recursos de um aplicativo, tornando o aplicativo indisponível para usuários legítimos. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet. A [proteção contra DDoS do Azure](../ddos-protection/ddos-protection-overview.md), combinada com as práticas recomendadas de design de aplicativo, fornece uma defesa robusta contra ataques de DDoS. Você pode conectar os logs de proteção contra DDoS do Azure ao Azure Sentinel, permitindo que você exiba dados de log em pastas de trabalho, use-os para criar alertas personalizados e incorporá-los para melhorar suas investigações. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Você deve ter um [plano de proteção padrão do DDoS do Azure](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan)configurado.

- Você deve ter uma rede virtual configurada [com o padrão de DDoS do Azure habilitado](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Conectar-se à proteção contra DDoS do Azure
    
1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Selecione **proteção contra DDoS do Azure** na Galeria de conectores de dados e, em seguida, selecione a **página abrir conector** no painel de visualização.

1. Habilite os **logs de diagnóstico** em todos os endereços IP públicos cujos logs você deseja conectar:

    1. Selecione o link **abrir configurações de diagnóstico >** e escolha um recurso de **endereço IP público** na lista.

    1. Selecione **+ Adicionar configuração de diagnóstico**.

    1. Na tela **configurações de diagnóstico** :
       - Insira um nome no campo  **nome da configuração de diagnóstico** .

       - Marque a caixa de seleção **Enviar para log Analytics** . Dois novos campos serão exibidos abaixo dele. Escolha a **assinatura** relevante e **log Analytics espaço de trabalho** (onde o Azure Sentinel reside).

       - Marque as caixas de seleção dos tipos de regra cujos logs você deseja ingerir. Recomendamos **DDoSProtectionNotifications**, **DDoSMitigationFlowLogs** e **DDoSMitigationReports**.

    1. Clique em **Salvar** na parte superior da tela. Repita esse processo para todos os firewalls adicionais (endereços IP públicos) para os quais você habilitou a proteção contra DDoS.

1. Para usar o esquema relevante em Log Analytics para alertas de proteção contra DDoS do Azure, procure **AzureDiagnostics**.

> [!NOTE]
>
> Com esse conector de dados específico, os indicadores de status de conectividade (uma faixa de cores na Galeria de conectores de dados e os ícones de conexão ao lado dos nomes dos tipos de dados) serão mostrados como *conectados* (verde) somente se os dados tiverem sido ingeridos em algum momento nas duas últimas semanas. Depois que duas semanas tiverem passado sem ingestão de dados, o conector será exibido como sendo desconectado. O momento em que mais dados surgem, o status *conectado* retornará.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar os logs de proteção contra DDoS do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
