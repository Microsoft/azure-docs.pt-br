---
title: Conector de Gerenciamento de Serviços de TI-exportação segura em Azure Monitor-configuração com BMC
description: Este artigo mostra como conectar seus produtos/serviços de ITSM com o BMC na exportação segura no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 598ce86aef41dd791099b49edccd6a55b3e43cdd
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97837997"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>Conectar o BMC Helix ao Azure Monitor

As seções a seguir fornecem detalhes sobre como conectar seu produto BMC Helix e exportação segura no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você atendeu aos seguintes pré-requisitos:

* O Azure AD está registrado.
* Você tem a versão com suporte do gerenciamento de serviços de várias nuvens do BMC Helix (versão 19, 8 ou posterior).

## <a name="configure-the-bmc-helix-connection"></a>Configurar a conexão do BMC Helix

1. Use o procedimento a seguir no ambiente BMC Helix para obter o URI para a exportação segura:

   1. Faça logon no Integration Studio.
   1. Procure o fluxo **criar incidente do Azure Alerts** .
   1. Copie a URL do webhook.
   
   ![Captura de tela do webhook U R L no Integration Studio.](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
2. Siga as instruções de acordo com a versão:
   * [Habilitando a integração predefinida com o Azure monitor para a versão 20, 2](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Habilitando a integração predefinida com o Azure monitor para a versão 19,11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. Como parte da configuração da conexão no BMC Helix, acesse sua instância de integração do BMC e siga estas instruções:

   1. Selecione **Catálogo**.
   2. Selecione **alertas do Azure**.
   3. Selecione **conectores**.
   4. Selecione **configuração**.
   5. Selecione **Adicionar nova** configuração de conexão.
   6. Preencha as informações da seção de configuração:
      - **Nome**: Crie o seu próprio.
      - **Tipo de autorização**: **nenhum**
      - **Descrição**: Crie o seu próprio.
      - **Site**: **nuvem**
      - **Número de instâncias**: **2**, o valor padrão.
      - **Marque**: selecionado por padrão para habilitar o uso.
      - A ID do locatário do Azure e a ID do aplicativo do Azure são obtidas do aplicativo que você definiu anteriormente.

![Captura de tela que mostra a configuração do BMC.](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)
