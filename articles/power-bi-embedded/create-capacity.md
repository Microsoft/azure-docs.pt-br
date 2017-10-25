---
title: Criar uma capacidade do Power BI Embedded no Portal do Azure | Microsoft Docs
description: Este artigo explica como criar uma capacidade do Power BI Embedded no Microsoft Azure.
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 1902e5c18cd7083ceeda79e6b9e779e4baaf175a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>Criar uma capacidade do Power BI Embedded no Portal do Azure

Este artigo explica como criar uma capacidade do Power BI Embedded no Microsoft Azure. O Power BI Embedded simplifica as funcionalidades do Power BI ajudando você a adicionar rapidamente elementos visuais, relatórios e painéis incríveis em seus aplicativos.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>Antes de começar

Para concluir este início rápido, você precisa de:

* **Assinatura do Azure**: visite a [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/) para criar uma conta.
* **Azure Active Directory**: sua assinatura deve estar associada a um locatário do AAD (Azure Active Directory). Também ***é preciso estar conectado ao Azure com uma conta no locatário em questão***. Não há suporte para contas da Microsoft. Para obter mais informações, confira Autenticação e permissões de usuário.
* **Locatário do Power BI:** pelo menos uma conta no seu locatário do AAD deve ter se inscrito no Power BI.
* **Grupo de recursos**: use um grupo de recursos que você já tem ou [crie um novo](../azure-resource-manager/resource-group-overview.md).

## <a name="create-a-capacity"></a>Criar uma capacidade

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

2. Selecione **+ (Novo)** > **Dados + Análise**.

3. Na caixa de pesquisa, pesquise *Power BI Embedded*.

4. No Power BI Embedded, selecione **Criar**.

5. Preencha as informações necessárias e selecione **Criar**.

    ![Campos a serem preenchidos para criar uma nova capacidade](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |Configuração |Descrição |
    |---------|---------|
    |**Nome do recurso**|Um nome para identificar a capacidade. O nome do recurso é exibido no portal de administração do Power BI, além do Portal do Azure.|
    |**Assinatura**|A assinatura na qual você deseja criar a capacidade.|
    |**Grupo de recursos**|O grupo de recursos que contém essa nova capacidade. Escolha um grupo de recursos existente ou crie outro. Para saber mais, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).|
    |**Administrador de capacidade do Power BI**|Os administradores de capacidade do Power BI podem exibir a capacidade no portal de administração do Power BI e atribuir permissões de atribuição a outros usuários. Por padrão, o administrador de capacidade é sua conta. O administrador de capacidade deve estar dentro de seu locatário do Power BI.|
    |**Localidade**|A localização em que o Power BI está hospedado para seu locatário. Essa configuração é resolvida automaticamente, não é possível selecionar uma localização diferente.|
    |**Tipo de preços**|Selecione a SKU (contagem de núcleo virtual e tamanho da memória) que atende às suas necessidades.  Para obter detalhes, consulte [Preço do Power BI Embedded](https://azure.microsoft.com/pricing/details/power-bi-embedded/)|

6. Selecione **Criar**.

Normalmente, a criação demora menos de um minuto, com frequência, apenas alguns segundos. Se você selecionou **Fixar no painel**, navegue até o painel para ver a nova capacidade. Ou navegue até **Mais serviços** > **Power BI Embedded** para ver se sua capacidade está pronta.

![painel do Portal do Azure com a capacidade do Power BI Embedded](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>Próximas etapas

Para usar a nova capacidade do Power BI Embedded, navegue até o portal de administração do Power BI para atribuir espaços de trabalho. Para saber mais, confira [Gerenciar capacidades no Power BI Premium e no Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

Se você não precisar usar essa capacidade, pause-a para interromper a cobrança. Para obter mais informações, consulte [Pausar e iniciar a capacidade do Power BI Embedded no Portal do Azure](pause-start.md).

Para começar a inserir conteúdo do Power BI em seu aplicativo, consulte [Como inserir seus painéis, relatórios e blocos do Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Mais perguntas? [Experimente perguntar para a Comunidade do Power BI](http://community.powerbi.com/)