---
title: Conecte dados do Cloud App Security ao Azure Sentinel visualização | Microsoft Docs
description: Saiba como se conectar a dados do Cloud App Security ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5a7dfeed7b52453b38720c21c7d213679b8d2854
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597134"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conectar-se a dados do Microsoft Cloud App Security 

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

É possível transmitir logs do [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) em Azure Sentinel com um único clique. Essa conexão permite que você transmita os alertas do Cloud App Security para Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador de segurança ou de administrador global

## <a name="connect-to-cloud-app-security"></a>Conectar-se ao Cloud App Security

Se você já tiver o Cloud App Security, verifique se ele está [habilitado na sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se o Cloud App Security é implantado e ingestão de seus dados, os dados de alerta podem facilmente ser transmitidos em Sentinel do Azure.


1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **Cloud App Security** lado a lado.

2. Clique em **Conectar**.

3. Para usar o esquema relevante no Log Analytics para os alertas do Cloud App Security, pesquise **SecurityAlert**.


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
