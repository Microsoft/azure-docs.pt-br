---
title: Conectar dados do Microsoft defender para Office 365 (anteriormente conhecido como Office 365 ATP) ao Azure Sentinel | Microsoft Docs
description: Ingerir dados do Microsoft defender para Office 365 no Azure Sentinel para obter visibilidade e criar cenários de resposta automatizados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: d5140bcd85606213f00185d4ba1f50dbe70dba63
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655487"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Conectar alertas do Microsoft defender para Office 365 

> [!IMPORTANT]
>
> - **O Microsoft defender para Office 365** era conhecido anteriormente como a **ATP (proteção avançada contra ameaças) do Office 365**.
>
>     Você pode ver que o nome antigo ainda está em uso no produto (incluindo seu conector de dados no Azure Sentinel) por um período de tempo.
>
> - A ingestão dos alertas do Microsoft defender para Office 365 está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
O [Microsoft defender para Office 365](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) protege sua organização contra o dia zero e outras ameaças avançadas causadas por malware desconhecido em mensagens de email, links de URL mal-intencionados e ferramentas de colaboração. Ao ingerir os alertas do Microsoft defender for Office 365 para o Azure Sentinel, você poderá utilizar informações sobre ameaças baseadas em email, compartilhamento de arquivos e com base em URL em suas operações de segurança. Em seguida, você pode analisar de forma mais abrangente os eventos de segurança em sua organização e criar guias estratégicos para uma resposta efetiva e imediata.

O conector importa os seguintes alertas:

- Um clique de URL potencialmente mal-intencionado foi detectado 

- Mensagens de email contendo malware removido após a entrega

- Mensagens de email contendo URLs de Phish removidas após a entrega 

- Email relatado pelo usuário como malware ou Phish 

- Padrões de envio de email suspeitos detectados 

- Usuário restrito do envio de email 

Esses alertas podem ser vistos por clientes do Office no **centro de conformidade e segurança do Office**.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação no espaço de trabalho do Azure Sentinel quando habilitar o conector.

- Você deve ser um administrador global ou um administrador de segurança no locatário do espaço de trabalho do Azure Sentinel.

- Você deve ter uma licença válida para o [office 365 ATP plano 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) (incluído nas licenças do Office 365 e5, Office 365 A5 e Microsoft 365 E5 e disponível para compra separadamente). 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Conectar-se ao Microsoft defender para Office 365

Se o Microsoft defender para Office 365 for implantado e se as políticas tiverem sido configuradas, os alertas poderão ser facilmente ingeridos no Azure Sentinel.

1. No Azure Sentinel, selecione **conectores de dados** no menu de navegação.

1. Selecione **Microsoft defender para Office 365** (ainda pode ser chamado de *proteção avançada contra ameaças do Office 365*) na Galeria de conectores e selecione a **página abrir conector**.

1. Na seção **configuração** , clique em **conectar**. 

1. Na seção **criar incidentes** , clique em **habilitar**.

1. Para usar o esquema relevante para consultar alertas do Office 365 ATP, procure **SecurityAlert** e especifique o **nome do provedor** como **OATP**.

1. Selecione a guia **próximas etapas** para ver e usar os exemplos de consulta e modelos de regra de análise agrupados com o conector do Microsoft defender para Office 365.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Microsoft defender para Office 365 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](./tutorial-detect-threats-built-in.md).