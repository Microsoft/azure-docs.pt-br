---
title: Conectar o Zimperium Mobile Threat Defense ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar o Zimperium Mobile Threat Defense ao Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 7cbf1c52af1d2902ae0726fc0dd98dbf12cecc44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097442"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Conecte o Zimperium Mobile Threat Defense ao Azure Sentinel


> [!IMPORTANT]
> Atualmente, o conector de dados do Zimperium Mobile Threat Defense no Azure Sentinel está em versão prévia pública.
> Esse recurso é fornecido sem um Contrato de Nível de Serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



O conector do Zimperium Mobile Threat Defense oferece a capacidade de conectar o log de ameaças do Zimperium ao Azure Sentinel para ver painéis, criar alertas personalizados e aprimorar a investigação. Isso proporciona mais insights sobre o panorama de ameaças móveis da sua organização e aprimora suas funcionalidades de operação de segurança.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do workspace no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Configurar e conectar o Zimperium Mobile Threat Defense

O Zimperium Mobile Threat Defense pode integrar os logs diretamente ao **Azure Sentinel**, além de exportá-los.

1. No portal do Azure Sentinel, clique em Conectores de dados e selecione **Zimperium Mobile Threat Defense**.
2. Clique em **Abrir página do conector**.
3. Siga as instruções da página do conector do **Zimperium Mobile Threat Defense**, resumidas a seguir.
 1. No zConsole, clique em **Gerenciar** na barra de navegação.
 2. Clique na guia **Integrações** .
 3. Clique no botão **Relatório de Ameaças** e no botão **Adicionar Integrações**.
 4. Crie a integração selecionando **Microsoft Azure Sentinel** nas integrações disponíveis e insira a ID do workspace e a chave primária para se conectar ao Azure Sentinel.
 5. Há também uma opção disponível para selecionar um nível de filtro para os dados de ameaça a serem enviados por push ao Azure Sentinel. 

4. Para obter mais informações, veja o [portal de suporte ao cliente do Zimperium](https://support.zimperium.com).


## <a name="find-your-data"></a>Encontrar seus dados

Depois que uma conexão bem-sucedida for estabelecida, os dados serão exibidos no Log Analytics em CustomLogs ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.

Para usar o esquema relevante no Log Analytics para o Zimperium Mobile Threat Defense, procure ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a ser exibidos no Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Zimperium Mobile Threat Defense ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).

- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

Para saber mais sobre o Zimperium, confira o seguinte:

- [Zimperium](https://zimperium.com)

- [Blog sobre segurança móvel do Zimperium](https://blog.zimperium.com)

- [Portal de suporte ao cliente do Zimperium](https://support.zimperium.com)

