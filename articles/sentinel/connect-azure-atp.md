---
title: Conectar-se a dados do Azure ATP para versão prévia do Azure Sentinel | Microsoft Docs
description: Saiba como se conectar a dados do Azure ATP ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 77f745f92133f4f43cd2a65f2b69ded1eff9e8ed
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620622"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Conecte dados do Azure Advanced Threat ATP (proteção)

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


É possível transmitir logs do [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) em Azure Sentinel com um único clique.

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador de segurança ou de administrador global
- Você deve ser um cliente de visualização privada do Azure ATP

## <a name="connect-to-azure-atp"></a>Conectar-se para o Azure ATP

Verifique se é a versão de visualização privada do Azure ATP [habilitado na sua rede](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Se o Azure ATP é implantado e ingestão de seus dados, os alertas suspeitos podem facilmente ser transmitidos em Sentinel do Azure. Pode levar até 24 horas para os alertas iniciar o streaming em Sentinel do Azure.



1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **do Azure ATP** lado a lado.

2. Clique em **Conectar**.

6. Para usar o esquema relevante no Log Analytics para os alertas do Azure ATP, pesquise **SecurityAlert**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Azure Advanced Threat Protection para Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

