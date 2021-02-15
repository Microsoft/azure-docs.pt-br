---
title: Conectar dados de proxy do Squid ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados de proxy Squid para efetuar pull de logs de proxy Squid no Azure Sentinel. Exiba dados de proxy Squid em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.openlocfilehash: eec88bf85f1b7a2ec8db2bf23c43629d84cc5106
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100090438"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>Conectar seu proxy do Squid ao Azure Sentinel

> [!IMPORTANT]
> O conector de proxy Squid está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seu dispositivo de proxy do Squid ao Azure Sentinel. O conector de dados proxy do Squid permite que você conecte facilmente seus logs do Squid com o Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre o proxy Squid e o Azure Sentinel usa o processamento de arquivo local pelo agente de Log Analytics.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissão de leitura e gravação no espaço de trabalho do Azure Sentinel.

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>Encaminhar logs de proxy do Squid para o agente de Log Analytics  

Configure o proxy Squid para enviar arquivos de log para seu espaço de trabalho do Azure por meio do agente de Log Analytics.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione o conector de **proxy Squid (versão prévia)** e, em seguida, **abra a página conector**.

1. Siga as instruções na página do conector de **proxy do Squid** :

    1. Instalar e carregar o agente para Linux

        - Escolha uma VM Linux do Azure ou um computador Linux não Azure (físico ou virtual).

    1. Configurar os logs a serem coletados

        - Nas configurações avançadas do espaço de trabalho, adicione um tipo de log personalizado, carregue um arquivo de exemplo e configure conforme indicado.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, em **logs personalizados**, na `SquidProxy_CL` tabela.

Consulte a guia **próximas etapas** na página conector para ver algumas consultas de exemplo úteis.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o proxy do Squid ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
