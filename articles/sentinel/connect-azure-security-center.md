---
title: Conectar dados do Azure defender ao Azure Sentinel
description: Saiba como conectar alertas do Azure defender da central de segurança do Azure e transmiti-los para o Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: bb188aa79015c2123b9d9d8b6baf277dfadf2f9c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633037"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Conectar dados de alerta do Azure defender da central de segurança do Azure

Use o conector de alerta do Azure defender para ingerir alertas do Azure defender da [central de segurança do Azure](../security-center/security-center-introduction.md) e transmiti-los para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- O usuário deve ter a função de leitor de segurança na assinatura dos logs que você transmite.

- Será necessário habilitar o Azure defender na central de segurança do Azure. (A camada Standard não existe mais e não é mais um requisito de licença.)

## <a name="connect-to-azure-defender"></a>Conectar-se ao Azure defender

1. No Azure Sentinel, selecione **conectores de dados** no menu de navegação.

1. Na Galeria de conectores de dados, selecione **alertas do Azure defender do ASC** (ainda pode ser chamado de central de segurança do Azure) e clique no botão **abrir página do conector** .

1. Em **configuração**, clique em **conectar** ao lado de cada assinatura cujos alertas você deseja transmitir para o Azure Sentinel. O botão conectar estará disponível somente se você tiver as permissões necessárias.

1. Você pode selecionar se deseja que os alertas do Azure defender gerem automaticamente incidentes no Azure Sentinel. Em **criar incidentes**, selecione **habilitado** para ativar a regra de análise padrão que cria automaticamente incidentes de alertas. Em seguida, você pode editar essa regra em **análise**, na guia  **regras ativas** .

1. Para usar o esquema relevante no Log Analytics para os alertas do Azure defender, procure **SecurityAlert**.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Azure defender ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
