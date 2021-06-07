---
title: Aplicativo de exemplo para acessar dados de análise do Marketplace comercial
description: Use o aplicativo de exemplo para criar seu próprio aplicativo de análise de Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583704"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Aplicativo de exemplo para acessar dados de análise do Marketplace comercial

Os aplicativos de exemplo são criados em linguagens C# e JAVA e estão disponíveis no [GitHub](https://github.com/partneranalytics).

- [Aplicativo de exemplo em C#](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [Aplicativo de exemplo JAVA](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

Você pode optar por fazer a inspiração do aplicativo de exemplo e criar seu próprio aplicativo em qualquer linguagem.

O aplicativo de exemplo atinge os seguintes objetivos:

- Gera um token Azure Active Directory (AD do Azure).
- Obtém conjuntos de valores disponíveis.
- Cria consultas definidas pelo usuário.
- Obtém consultas de sistema e definidas pelo usuário.
- Agenda um relatório.

O aplicativo de exemplo não aborda o método de chamar APIs para outras funcionalidades. No entanto, o processo de chamar outras APIs permanece o mesmo descrito acima.

## <a name="how-to-run-the-application"></a>Como executar o aplicativo

1. Clone o repositório para um sistema local usando este comando:

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > Para obter mais instruções, consulte o `ProgrammaticExportSampleAppISV/README.md` arquivo no [repositório](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)github.

1. Para executar rapidamente o aplicativo, atualize a ID do cliente e o segredo do cliente no **appsettings.Development.jsem**

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="Ilustra um trecho da appsettings.Development.jsno arquivo.":::

A execução do aplicativo iniciará um servidor Web local e uma página será aberta ( `https://localhost:44365/ProgrammaticExportSampleApp/sample` ).

[![Ilustra a página do relatório de agendamento.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

Esta página fará chamadas de API para o servidor Web em execução no computador local, o que, por sua vez, fará as chamadas de API de acesso programático real.

## <a name="code-snippets"></a>Snippets de código

A estrutura básica do código C# para fazer as chamadas de API de acesso programático é a seguinte:

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="Captura de tela de chamadas de API.":::

## <a name="next-steps"></a>Próximas etapas

- [APIs para acessar dados de análise do Marketplace comercial](analytics-available-apis.md)
