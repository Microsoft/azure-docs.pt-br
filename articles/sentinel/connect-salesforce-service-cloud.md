---
title: Conectar dados de nuvem do serviço Salesforce ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados de nuvem do serviço do Salesforce para efetuar pull de logs do Salesforce no Azure Sentinel. Exiba dados do Salesforce em pastas de trabalho, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 152fee2e4e72cee6a7bc1c768c0a8ca7b031ec39
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878911"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Conectar sua nuvem do serviço Salesforce ao Azure Sentinel

> [!IMPORTANT]
> O conector de nuvem do serviço do Salesforce está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar sua solução de nuvem do serviço Salesforce ao Azure Sentinel. O conector de dados de nuvem do serviço Salesforce permite que você conecte facilmente seus dados do Salesforce com o Azure Sentinel, para que você possa exibi-los em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre a nuvem do serviço do Salesforce e o Azure Sentinel usa a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissão de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Você deve ter permissões de leitura para chaves compartilhadas para o espaço de trabalho. [Saiba mais sobre as chaves do espaço de trabalho](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

- Você deve ter permissões de leitura e gravação para Azure Functions a fim de criar uma Aplicativo de funções. [Saiba mais sobre Azure Functions](../azure-functions/index.yml).

- Você deve ter as seguintes credenciais da API REST do Salesforce: **nome de usuário da API** do Salesforce, senha da **API** do Salesforce, token de **segurança do Salesforce**, chave do consumidor do **Salesforce**, segredo do **consumidor do Salesforce**. [Saiba mais sobre a API REST do Salesforce](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

## <a name="configure-and-connect-salesforce-service-cloud"></a>Configurar e conectar a nuvem do serviço Salesforce

A nuvem do serviço Salesforce pode integrar e exportar logs diretamente para o Azure Sentinel.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione a **nuvem do serviço Salesforce (versão prévia)** e **abra a página conector**.

1. Siga as etapas descritas na seção **configuração** da página conector.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **CustomLogs** , na `SalesforceServiceCloud_CL` tabela.

Consulte a guia **próximas etapas** na página conector para ver algumas consultas de exemplo úteis.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar a nuvem do serviço Salesforce ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.