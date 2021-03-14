---
title: Conectar dados do Microsoft defender for Identity (anteriormente Azure ATP) ao Azure Sentinel | Microsoft Docs
description: Saiba como transmitir logs do Microsoft defender para identidade (anteriormente chamado de proteção avançada contra ameaças do Azure) (ATP) para o Azure Sentinel com um único clique.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 1fe36dc7b3c04f033c1b693b657e07bcf42e3223
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714996"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Conectar dados do Microsoft defender para identidade (anteriormente, proteção avançada contra ameaças do Azure)

> [!IMPORTANT]
> O Microsoft defender for Identity data Connector no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo descreve como transmitir alertas de segurança do [Microsoft defender para identidade](/azure-advanced-threat-protection/what-is-atp) no Azure Sentinel. 

Para encaminhar alertas de integridade além de alertas de segurança, integre o Microsoft defender para identidade com um servidor syslog. Para obter mais informações, consulte a [documentação do Microsoft defender para identidade](/defender-for-identity/setting-syslog). 

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões de administrador global ou administrador de segurança
- Você deve ser um cliente de visualização do Microsoft defender para identidade e habilitar a integração entre o Microsoft defender para identidade e o Microsoft Cloud App Security. Para obter mais informações, consulte [integração do Microsoft defender para identidade](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection).

## <a name="connect-to-microsoft-defender-for-identity"></a>Conectar-se ao Microsoft defender para identidade

Verifique se a versão de visualização do Microsoft defender para identidade está [habilitada em sua rede](/azure-advanced-threat-protection/install-atp-step1).
Se o Microsoft defender for Identity for implantado e ingerir seus dados, os alertas suspeitos poderão ser facilmente transmitidos para o Azure Sentinel. Pode levar até 24 horas para que os alertas comecem a transmitir para o Azure Sentinel.


1. Para conectar o Microsoft defender para identidade ao Azure Sentinel, você deve primeiro habilitar a integração entre o Microsoft defender para identidade e o Microsoft Cloud App Security. Para obter informações sobre como fazer isso, consulte [integração do Microsoft defender para identidade](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection).

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **Microsoft defender for Identity (versão prévia)** .

1. Você pode selecionar se deseja que os alertas do Microsoft defender para identidade gerem automaticamente incidentes no Azure Sentinel automaticamente. Em **Criar incidentes**, selecione **Habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente com base em alertas gerados no serviço de segurança conectado. É possível editar essa regra em **Análise** e depois em **Regras ativas**.

1. Clique em **Conectar**.

1. Para usar o esquema relevante no Log Analytics para os alertas do Microsoft defender for Identity, procure **SecurityAlert**.

> [!NOTE]
> Se os alertas forem maiores que 30 KB, o Azure Sentinel interromperá a exibição do campo entidades nos alertas.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Microsoft defender para identidade ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).