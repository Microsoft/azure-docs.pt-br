---
title: Conectar-se a dados do Azure AD para o Azure Sentinel Preview | Microsoft Docs
description: Saiba como se conectar a dados do Active Directory do Azure para Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 5784101c9f2e0dc238ac48c5d0f6fbe4c0dc596f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620673"
---
# <a name="connect-data-from-azure-active-directory"></a>Conectar-se a dados do Azure Active Directory

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinela do Azure permite que você colete dados do [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmita-a para o Azure Sentinel. Você pode escolher fluxo [logs de entrada](../active-directory/reports-monitoring/concept-sign-ins.md) e [logs de auditoria](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Pré-requisitos

- Se você quiser exportar dados de entrada do Active Directory, você deve ter uma licença do Azure AD P1 ou P2.

- Usuário com permissões globais de administrador de segurança ou de administrador no locatário que você deseja transmitir os logs de.

- Para poder ver o status de conexão, você deve ter permissão para acessar os logs de diagnóstico do Azure AD. 


## <a name="connect-to-azure-ad"></a>Conecte-se ao AD do Azure

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **Azure Active Directory** lado a lado.

2. Ao lado dos logs que você deseja transmitir para o Azure Sentinel, clique em **Connect**.

6. Para usar o esquema relevante no Log Analytics para os alertas do Azure AD, pesquise **SigninLogs** e **AuditLogs**.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Azure AD ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
