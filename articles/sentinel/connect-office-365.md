---
title: Conectar logs do Office 365 ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de log do Office 365 para obter informações sobre atividades de usuário e de administrador em andamento no Exchange, nas equipes e no SharePoint, incluindo o OneDrive.
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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 05848eb2761284669e659b3875e96acdfa71f90f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632175"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Conectar logs do Office 365 ao Azure Sentinel

O conector de log do [Office 365](/office/) traz para o Azure Sentinel informações sobre atividades de usuário e administrador em andamento no **Exchange** e no **SharePoint** (incluindo o **onedrive**), e agora também em **equipes** . Essas informações incluem detalhes de ações como downloads de arquivos, solicitações de acesso enviadas, alterações em eventos de grupo, operações de caixa de correio, eventos de equipes (como eventos de chat, equipe, membro e canal), bem como os detalhes do usuário que realizou as ações. Conectar logs do Office 365 ao Azure Sentinel permite exibir e analisar esses dados em suas pastas de trabalho, consultá-los para criar alertas personalizados e incorporá-los para melhorar o processo de investigação, fornecendo mais informações sobre a segurança do Office 365.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação em seu espaço de trabalho do Azure Sentinel.

- Você precisa ser um administrador global ou administrador da segurança no seu locatário.

- Sua implantação do Office 365 deve estar no mesmo locatário que o seu espaço de trabalho do Azure Sentinel.

> [!IMPORTANT]
> - Para que o conector possa acessar dados por meio da API de atividade de gerenciamento do Office 365, você deve ter o **log de auditoria unificado** habilitado na sua implantação do Office 365. Dependendo do tipo de licença do Office 365/Microsoft 365 que você tem, ela pode ou não ser habilitada por padrão. Consulte o [centro de conformidade e segurança do Office 365](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) para verificar o status do log de auditoria unificado de acordo com seu tipo de licença.
> - Você também pode habilitar, desabilitar e verificar manualmente o status atual do log de auditoria unificada do Office 365. Para obter instruções, consulte [Ativar ou desativar a pesquisa de logs de auditoria do Office 365](/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Para obter mais informações, consulte a [referência da API de atividade de gerenciamento do Office 365](/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Conforme observado acima, e como você verá na página conector em tipos de **dados**, o conector do Azure Sentinel Office 365 atualmente dá suporte à ingestão de logs de auditoria somente do Microsoft Exchange e do SharePoint (incluindo o onedrive) **e, agora, de equipes também**. No entanto, há algumas soluções externas se você estiver interessado em trazer [outros dados do Office](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) para o Azure Sentinel. 

## <a name="enable-the-office-365-log-connector"></a>Habilitar o conector de log do Office 365

### <a name="instructions-tab"></a>Guia instruções

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione **Office 365** e, em seguida, selecione a **página conector aberto** no painel de visualização.

1. Na seção rotulada **configuração**, marque as caixas de seleção dos logs de atividade do Office 365 que você deseja conectar ao Azure Sentinel e clique em **aplicar alterações**. 

   > [!NOTE]
   > Se você tiver conectado anteriormente vários locatários ao Azure Sentinel, usando uma versão mais antiga do conector do Office 365 com suporte, você poderá exibir e modificar quais logs você coleta de cada locatário. Você não poderá adicionar locatários, mas poderá remover locatários adicionados anteriormente.

### <a name="next-steps-tab"></a>Guia próximas etapas

- Consulte as pastas de trabalho recomendadas, exemplos de consulta e modelos de regra de análise que são agrupados com o conector de log do **Office 365** , para obter informações sobre os dados de log do SharePoint, do onedrive, do Exchange e das equipes.

- Para consultar manualmente os dados de log do Office 365 nos **logs**, insira `OfficeActivity` na primeira linha da janela de consulta.
   - Para filtrar a consulta para um tipo de log específico, insira `| where OfficeWorkload == "<logtype>"` na segunda linha da consulta, em que *\<logtype\>* é `SharePoint` , `OneDrive` , `Exchange` ou `MicrosoftTeams` .

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Office 365 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a detectar ameaças com o Azure Sentinel, usando regras [internas](tutorial-detect-threats-built-in.md) ou [personalizadas](tutorial-detect-threats-custom.md) .