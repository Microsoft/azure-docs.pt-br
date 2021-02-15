---
title: Integração de item de trabalho (versão prévia)-Application Insights
description: Saiba como criar itens de trabalho no GitHub ou no Azure DevOps com dados Application Insights inseridos neles.
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: 0c1d6ffd6a5a39fa49eadc558aa80f365f856df2
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101122"
---
# <a name="work-item-integration-preview"></a>Integração de item de trabalho (versão prévia)

A funcionalidade de integração de itens de trabalho permite que você crie facilmente itens de trabalho no GitHub ou no Azure DevOps que têm dados relevantes Application Insights inseridos neles.

> [!IMPORTANT]
> A integração do item de trabalho está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-and-configure-a-work-item-template"></a>Criar e configurar um modelo de item de trabalho

1. Para criar um modelo de item de trabalho, vá para o recurso Application Insights e à esquerda em *Configurar* selecionar **itens de trabalho** e, na parte superior, selecione **criar um novo modelo**

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Captura de tela da guia itens de trabalho com a criação de um novo modelo selecionado." lightbox="./media/work-item-integration/create-work-item-template.png":::

    Você também pode criar um modelo de item de trabalho na guia detalhes da transação de ponta a ponta, se nenhum modelo existir atualmente. Selecione um evento e, à direita, selecione **criar um item de trabalho** e, em seguida, **inicie com um modelo de pasta de trabalho**.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Captura de tela da guia detalhes da transação de ponta a ponta com criar um item de trabalho, comece com um modelo de pasta de trabalho selecionado." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. Depois de selecionar **criar um novo modelo**, você pode escolher seus sistemas de controle, nomear sua pasta de trabalho, vincular ao sistema de controle selecionado e escolher uma região para armazenar o modelo (o padrão é a região em que o recurso de Application insights está localizado). Os parâmetros de URL são a URL padrão para seu repositório, por exemplo, `https://github.com/myusername/reponame` ou `https://mydevops.visualstudio.com/myproject` .

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Captura de tela de criar um novo modelo de pasta de trabalho de item de trabalho.":::

## <a name="create-a-work-item"></a>Criar um item de trabalho

 Você pode acessar o novo modelo de qualquer detalhe de transação de ponta a ponta que possa ser acessado de desempenho, falhas, disponibilidade ou outras guias.

1. Para criar um item de trabalho, vá para detalhes da transação de ponta a ponta, selecione um evento e, em seguida, selecione **Criar item de trabalho** e escolha seu modelo de item de trabalho.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Captura de tela da guia detalhes da transação de ponta a ponta com criar item de trabalho selecionado." lightbox="./media/work-item-integration/create-work-item.png":::

1. Uma nova guia no navegador será aberta até o sistema de controle selecionado. No Azure DevOps, você pode criar um bug ou uma tarefa e, no GitHub, você pode criar um novo problema em seu repositório. Um novo item de trabalho é criado automaticamente com informações contextuais fornecidas pelo Application Insights.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Captura de tela do problema do GitHub criado automaticamente" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Captura de tela de criação automática de bug no Azure DevOps." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Editar um modelo

Para editar o modelo, vá para a guia **itens de trabalho** em *Configurar* e selecione o ícone de lápis ao lado da pasta de trabalho que você deseja atualizar.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Captura de tela da guia item de trabalho com o ícone Editar lápis selecionado.":::

Selecione Editar ![ ícone de edição ](./media/work-item-integration/edit-icon.png) na parte superior para começar a editar o modelo. Os modelos de item de trabalho são baseados em [pastas de trabalho do Azure monitor](../platform/workbooks-overview.md). As informações do item de trabalho são geradas usando a linguagem de consulta de palavra-chave. Você pode modificar as consultas para adicionar mais contexto essencial à sua equipe. Quando terminar de editar, salve a pasta de trabalho selecionando o ícone salvar ícone ![ Salvar ](./media/work-item-integration/save-icon.png) na barra de ferramentas superior.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Captura de tela da pasta de trabalho do modelo de item de trabalho no modo de edição." lightbox="./media/work-item-integration/edit-workbook.png":::

Você pode criar mais de uma configuração de item de trabalho e ter uma pasta de trabalho personalizada para atender a cada cenário. As pastas de trabalho também podem ser implantadas por Azure Resource Manager garantindo implementações padrão em seus ambientes.