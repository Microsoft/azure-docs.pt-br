---
title: Conectar-se a dados do Office 365 para o Azure Sentinel Preview | Microsoft Docs
description: Saiba como se conectar a dados do Office 365 a Sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 77587b0b7506ef0ccadbeb6d1f010f5b6a72d93e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714536"
---
# <a name="connect-data-from-office-365-logs"></a>Conectar dados de Logs do Office 365

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir os logs de auditoria do [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) em Azure Sentinel com um único clique. Você pode transmitir logs de auditoria de vários locatários para um único espaço de trabalho no Azure Sentinel. O conector do log de atividades do Office 365 fornece informações sobre as atividades do usuário em andamento. Você obterá informações sobre vários usuário, administrador, sistema e as ações de política e eventos do Office 365. Conectando-se em logs do Office 365 no Azure Sentinel, você pode usar esses dados para exibir painéis, criar alertas personalizados e melhorar o processo de investigação.


## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um administrador global ou administrador de segurança em seu locatário
- Em seu computador, do qual você se conectou Sentinel do Azure para criar a conexão, certifique-se de que a porta 4433 está aberta para tráfego da web.

## <a name="connect-to-office-365"></a>Conectar ao Office 365

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **Office 365** lado a lado.

2. Se você ainda não tiver habilitado, sob **Conexão** usar o **habilitar** botão para habilitar a solução do Office 365. Se já tiver sido habilitado, ele será identificado na tela de conexão como já está habilitada.
1. O Office 365 permite que você transmitir dados de vários locatários para Sentinel do Azure. Para cada locatário que você deseja se conectar, adicione o locatário sob **são conectadas ao Azure Sentinel**. 
1. Abre uma tela do Active Directory. Você precisará se autenticar com um usuário de administrador global em cada locatário que você deseja se conectar ao Azure Sentinel e fornecer permissões para o Azure Sentinel ler seus logs. 
5. Em logs de atividades do Office 365 do Stream, clique em **selecionar** para escolher quais tipos de log que você deseja transmitir ao Azure Sentinel. Atualmente, Sentinel do Azure dá suporte ao Exchange e SharePoint.

4. Clique em **aplicar alterações**.

3. Para usar o esquema relevante no Log Analytics para os logs do Office 365, pesquise **OfficeActivity**.


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Office 365 para Sentinel do Azure. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

