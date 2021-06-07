---
title: Conectar dados do firewall do Azure ao Azure Sentinel
description: Saiba como conectar dados do firewall do Azure ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: b21ce75bfb33b5a8869c63b7d3f71fb9f0c93768
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621288"
---
# <a name="connect-data-from-azure-firewall"></a>Conectar dados do firewall do Azure

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita. 

Você pode conectar os logs de firewall do Azure ao Azure Sentinel, permitindo que você exiba dados de log em pastas de trabalho, use-os para criar alertas personalizados e incorporá-los para melhorar sua investigação.

Saiba mais sobre como [monitorar logs de firewall do Azure](../firewall/firewall-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação no espaço de trabalho do Azure Sentinel.

## <a name="connect-to-azure-firewall"></a>Conectar-se ao firewall do Azure
    
1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Selecione **Firewall do Azure** na Galeria de conectores de dados e, em seguida, selecione a **página abrir conector**  no painel de visualização.

1. Habilite os **logs de diagnóstico** em todos os firewalls cujos logs você deseja conectar:

    1. Selecione o link **abrir >de recursos do firewall do Azure** .

    1. No menu de navegação **firewalls** , selecione **configurações de diagnóstico**.

    1. Selecione **+ Adicionar configuração de diagnóstico** na parte inferior da lista.

    1. Na tela **configurações de diagnóstico** , insira um nome no campo  **nome das configurações de diagnóstico** .
    
    1. Marque a caixa de seleção **Enviar para log Analytics** . Dois novos campos serão exibidos abaixo dele. Escolha a **assinatura** relevante e **log Analytics espaço de trabalho** (onde o Azure Sentinel reside).

    1. Marque as caixas de seleção dos tipos de regra cujos logs você deseja ingerir. Recomendamos **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule**.

    1. Selecione **salvar** na parte superior da tela.

1. Para usar o esquema relevante no Log Analytics para alertas de firewall do Azure, procure **AzureDiagnostics**.

> [!NOTE]
>
> Com esse conector de dados específico, os indicadores de status de conectividade (uma faixa de cores na Galeria de conectores de dados e os ícones de conexão ao lado dos nomes dos tipos de dados) serão mostrados como *conectados* (verde) somente se os dados tiverem sido ingeridos em algum momento nas duas últimas semanas. Depois que duas semanas tiverem passado sem ingestão de dados, o conector será exibido como sendo desconectado. O momento em que mais dados surgem, o status *conectado* retornará.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar os logs do firewall do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).