---
title: Conectar a defesa contra ameaças móveis do Zimperium ao Azure sentinela | Microsoft Docs
description: Saiba como conectar a defesa contra ameaças móveis do Zimperium ao Azure Sentinel.
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
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097442"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Conecte sua defesa contra ameaças móveis do Zimperium ao Azure Sentinel


> [!IMPORTANT]
> O conector de dados de defesa contra ameaças móveis do Zimperium no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



O conector de defesa contra ameaças móveis do Zimperium oferece a capacidade de conectar o log de ameaças do Zimperium com o Azure Sentinel para exibir painéis, criar alertas personalizados e melhorar a investigação. Isso lhe dá mais informações sobre o panorama de ameaças móveis de sua organização e aprimora seus recursos de operação de segurança.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Configurar e conectar a defesa contra ameaças móveis do Zimperium

A defesa contra ameaças móveis do Zimperium pode integrar e exportar logs diretamente para o **Azure Sentinel**.

1. No portal do Azure Sentinel, clique em conectores de dados e selecione **defesa contra ameaças móveis do Zimperium**.
2. Selecione a **página abrir conector**.
3. Siga as instruções na página do conector de **defesa contra ameaças móveis do Zimperium** , resumido da seguinte maneira.
 1. Em zConsole, clique em **gerenciar** na barra de navegação.
 2. Clique na guia **Integrações** .
 3. Clique no botão **relatório de ameaças** e, em seguida, no botão **Adicionar integrações** .
 4. Crie a integração selecionando **Microsoft Azure sentinela** nas integrações disponíveis e insira a ID do espaço de trabalho e a chave primária para se conectar ao Azure Sentinel.
 5. A opção para selecionar um nível de filtro para os dados de ameaça a serem enviados por push para o Azure Sentinel também está disponível. 

4. Para obter informações adicionais, consulte o [portal de suporte ao cliente do Zimperium](https://support.zimperium.com).


## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em CustomLogs ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.

Para usar o esquema relevante em Log Analytics para a defesa contra ameaças móveis do Zimperium, procure ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar a defesa contra ameaças móveis do Zimperium ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).

- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

Para saber mais sobre o Zimperium, consulte o seguinte:

- [Zimperium](https://zimperium.com)

- [Blog do Zimperium Mobile Security](https://blog.zimperium.com)

- [Portal de suporte ao cliente do Zimperium](https://support.zimperium.com)

