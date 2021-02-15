---
title: Conectar dados de Sign-On único do Okta ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de Sign-On único do Okta ao Azure Sentinel.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e76aea8a3fc59827664900a6d5686e2e725e258d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093108"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Conectar o Sign-On único do Okta ao Azure Sentinel com o Azure function

> [!IMPORTANT]
> O conector de dados de Sign-On único do Okta no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector do Okta single Sign-On (SSO) permite que você conecte facilmente todos os seus logs de solução de segurança de [SSO (Sign-On único) do Okta](https://www.okta.com/products/single-sign-on/) com o Azure Sentinel, para exibir painéis, criar alertas personalizados e melhorar a investigação. A integração entre o Okta único Sign-On e o Azure Sentinel usa Azure Functions para efetuar pull de dados de log usando a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-okta-single-sign-on"></a>Configurar e conectar o Okta único Sign-On

Azure Functions pode integrar e efetuar pull de eventos e logs diretamente do Okta único Sign-On e encaminhá-los para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector **de logon único do Okta** .

1. Selecione a **página abrir conector**.

1. Siga as instruções na página **logon único do Okta** .

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados são exibidos em Log Analytics na tabela **Okta_CL** .

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Okta single Sign-On ao Azure Sentinel usando os aplicativos de funções do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

