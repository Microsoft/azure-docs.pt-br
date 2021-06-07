---
title: Conectar logs do Dynamics 365 ao Azure Sentinel | Microsoft Docs
description: Aprenda a usar o conector de atividades do Dynamics 365 Common Data Service (CDS) para reunir informações sobre atividades de administrador, usuário e suporte em andamento.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98104166"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Conectar logs de atividades do Dynamics 365 ao Azure Sentinel

O conector de atividades do [Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) Common Data Service (CDs) fornece informações sobre as atividades de administrador, usuário e suporte, bem como eventos de registro em log do engajamento social da Microsoft. Conectando os logs do Dynamics 365 CRM ao Azure Sentinel, você pode exibir esses dados em pastas de trabalho, usá-los para criar alertas personalizados e aproveitá-los para melhorar o processo de investigação. Esse novo conector do Azure Sentinel coleta os dados do Dynamics CDS da API de gerenciamento do Office. Para saber mais sobre as atividades do Dynamics CDS auditadas na plataforma de energia, visite [habilitar e usar o log de atividades](/power-platform/admin/enable-use-comprehensive-auditing).

> [!IMPORTANT]
>
> O conector de atividades do Dynamics 365 Common Data Service (CDS) está em **Visualização** no momento. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação em seu espaço de trabalho do Azure Sentinel.

- Você deve ter uma [licença de produção do Microsoft Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). Este conector não está disponível para ambientes de área restrita.
    - Uma assinatura Microsoft 365 Enterprise [E3 ou E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) é necessária para fazer o log de atividades.

- Para efetuar pull de dados da API de gerenciamento do Office:
    - Você deve ser um administrador global em seu locatário.

    - O [log de auditoria do Office](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) deve estar habilitado no centro de [conformidade e segurança do Office](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance).

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Habilitar o conector de dados de atividades do Dynamics 365

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione **Dynamics 365 (versão prévia)** e, em seguida, selecione a **página conector aberto** no painel de visualização.

1. Na guia **instruções** , em **configuração**, clique em **conectar**. 

    Depois que o conector for ativado, levará cerca de 30 minutos até que você possa ver os dados chegando no grafo. 

    De acordo com o [log de auditoria do Office no centro de conformidade](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log), pode levar até 30 minutos ou até 24 horas após a ocorrência de um evento para que o registro de log de auditoria correspondente seja retornado nos resultados.

1. Os logs de auditoria do Microsoft Dynamics podem ser encontrados na `Dynamics365Activity` tabela. Consulte a referência de [esquema](/azure/azure-monitor/reference/tables/dynamics365activity)da tabela.

## <a name="querying-the-data"></a>Consulta dos dados

1. No menu de navegação do Azure Sentinel, selecione **logs**.

1. Execute a consulta a seguir para verificar se os logs chegam:

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar dados de atividades do Dynamics 365 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a detectar ameaças com o Azure Sentinel, usando regras [internas](tutorial-detect-threats-built-in.md) ou [personalizadas](tutorial-detect-threats-custom.md) .
