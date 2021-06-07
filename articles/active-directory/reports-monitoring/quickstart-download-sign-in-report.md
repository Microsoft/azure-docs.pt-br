---
title: 'Início Rápido: baixar um relatório de entrada usando o portal do Azure | Microsoft Docs'
description: Saiba como baixar um relatório de entrada usando o portal do Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011907"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Início Rápido: baixar um relatório de entrada usando o portal do Azure

Neste início rápido, você aprenderá a baixar os dados de entrada para seu locatário nas últimas 24 horas. Você pode baixar até 250.000 registros do portal do Azure. Os registros são classificados por mais recentes; por padrão, você obterá os 250.000 registros mais recentes. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de:

* Um locatário do Azure Active Directory, com uma licença Premium para exibir o relatório de atividade de entrada. Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory. Observe que, se você não tiver dados de atividades antes da atualização, serão necessários alguns dias para que os dados sejam exibidos nos relatórios depois que você atualizar para uma licença premium.
* Um usuário, que está na função de **Administrador de Segurança**, **Leitor de Segurança**, **Leitor de Relatório** ou **Administrador Global** para o locatário. Além disso, qualquer usuário no locatário pode acessar suas próprias entradas.

## <a name="quickstart-download-a-sign-in-report"></a>Início Rápido: baixar um relatório de entrada

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** do painel de navegação à esquerda e use o botão **Mudar diretório** para selecionar seu Active Directory.
3. No painel, selecione **Azure Active Directory** e, em seguida, selecione **Entradas**. 
4. Escolha **Últimas 24 horas** na lista suspensa do filtro **Data** e selecione **Aplicar** para exibir as entradas nas últimas 24 horas. 
5. Selecione o botão **Baixar**, selecione **CSV** como o formato de arquivo e especifique um nome de arquivo para baixar um arquivo CSV contendo os registros filtrados. 

![Relatório](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Próximas etapas

* [Relatórios de atividades de entrada no portal do Azure Active Directory](concept-sign-ins.md)
* [Retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md)
* [Latências de relatórios do Azure Active Directory](reference-reports-latencies.md)
