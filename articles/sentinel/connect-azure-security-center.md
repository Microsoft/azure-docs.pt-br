---
title: Conectar-se a dados da Central de segurança do Azure para visualização do Azure Sentinel | Microsoft Docs
description: Saiba como se conectar a dados da Central de segurança do Azure para Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 697983000f84f9e4efe7e2c8ef9dbb2f636e0735
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620569"
---
# <a name="connect-data-from-azure-security-center"></a>Conecte dados da Central de segurança do Azure

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Sentinela Azure lhe permite conectar os alertas do [Central de segurança do Azure](../security-center/security-center-intro.md) e transmiti-los para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Se você quiser exportar alertas da Central de segurança do Azure, você deve ser um colaborador na assinatura cujos logs você transmitir.

- Você deve ter o [camada Standard de Central de segurança do Azure](../security-center/security-center-pricing.md) em execução na assinatura. Caso contrário, [atualizar sua assinatura para o padrão](https://azure.microsoft.com/pricing/details/security-center/).

- Você deve fazer logon com um usuário que tenha permissões de administrador de segurança ou de administrador global em cada assinatura que você deseja se conectar.


## <a name="connect-to-azure-security-center"></a>Conectar-se à Central de segurança do Azure

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **Central de segurança do Azure** lado a lado.
1. No canto direito, clique em **Connect** ao lado de cada assinatura cujos alertas você deseja transmitir para o Azure Sentinel. Certifique-se de atualizar cada assinatura para a camada Standard de Central de segurança do Azure para alertas de fluxo para o Azure Sentinel.

3. Clique em **Conectar**.

4. Para usar o esquema relevante no Log Analytics para os alertas da Central de segurança do Azure, pesquise **SecurityEvent**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar-se a Central de segurança do Azure para Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
