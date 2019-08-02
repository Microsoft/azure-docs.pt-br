---
title: Conectando a dados de proteção de informações do Azure para o Azure Sentinel Preview | Microsoft Docs
description: Saiba como se conectar a dados de proteção de informações do Azure no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 3edfdca16141726ea591c8473528fc23ee076c53
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620599"
---
# <a name="connect-data-from-azure-information-protection"></a>Conecte dados da proteção de informações do Azure

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

É possível transmitir logs do [do Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) em Azure Sentinel com um único clique. A proteção de informações do Azure ajuda a proteger seus dados, estejam eles armazenados na nuvem ou em infraestruturas locais e controle e ajuda para proteger emails, documentos e dados confidenciais que você compartilha fora da sua empresa. Desde fácil classificação até rótulos e permissões inseridos, aprimore a proteção de dados em todos os momentos com a proteção de informações do Azure. Ao se conectar Azure Information Protection para Azure Sentinel, transmitir todos os alertas da proteção de informações do Azure em Azure Sentinel.


## <a name="prerequisites"></a>Pré-requisitos

- Usuário com o administrador global, administrador de segurança ou permissões de proteção de informações


## <a name="connect-to-azure-information-protection"></a>Conectar-se à proteção de informações do Azure

Se você já tiver a proteção de informações do Azure, verifique se ele está [habilitado na sua rede](https://docs.microsoft.com/azure/information-protection/activate-service).
Se a proteção de informações do Azure é implantada e recebendo dados, os dados de alerta podem facilmente ser transmitidos em Sentinel do Azure.


1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **proteção de informações do Azure** lado a lado.

2. Vá para o [portal do Azure Information Protection](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. Sob **Conexão**, configurar o streaming de logs do Azure Information Protection para Azure Sentinel clicando [configurar a análise](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. Selecione o espaço de trabalho no qual você implantou o Azure Sentinel. 

5. Clique em **OK**.

6. Para usar o esquema relevante no Log Analytics para os alertas de proteção de informações do Azure, pesquise **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar-se a proteção de informações do Azure para Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
