---
title: Conectar dados de Cloud App Security à visualização do Azure Sentinel | Microsoft Docs
description: Saiba como conectar Cloud App Security dados ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 92de4120d73af70c5de013488344a15ba46513c7
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881080"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conectar dados de Microsoft Cloud App Security 

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir logs de [Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) para o Azure Sentinel com um único clique. Essa conexão permite transmitir os alertas de Cloud App Security para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador global ou administrador de segurança

## <a name="connect-to-cloud-app-security"></a>Conectar-se ao Cloud App Security

Se você já tiver Cloud App Security, verifique se ele está [habilitado em sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security for implantado e ingerir seus dados, os dados de alerta poderão ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no bloco **Cloud app Security** .

2. Clique em **Conectar**.

3. Para usar o esquema relevante em Log Analytics para os alertas de Cloud App Security, procure **SecurityAlert**.


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
