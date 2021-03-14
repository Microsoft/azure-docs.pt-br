---
title: Resumo e acesso de conversão do designer de exibição para pastas de trabalho Azure Monitor
description: Permissões necessárias para acessar pastas de trabalho ao fazer a transição de exibições no Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 42cb8db0f67d3a01b2e8443e6cb7c47f0fc44c4b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043383"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Resumo e acesso de conversão do designer de pastas de trabalho
O [Designer de exibição](view-designer.md) é um recurso do Azure monitor que permite que você crie modos de exibição personalizados para ajudá-lo a Visualizar dados em seu espaço de trabalho do log Analytics, com gráficos, listas e linhas do tempo. Eles estão sendo desativados e substituídos por pastas de trabalho, que fornecem funcionalidade adicional. Este artigo fornece detalhes sobre como você pode criar um resumo de visão geral e as permissões necessárias para acessar pastas de trabalho.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Criando o resumo do seu espaço de trabalho no painel do Azure
Os usuários do designer de exibição podem estar familiarizados com um bloco de visão geral para representar um conjunto de exibições. Para manter uma visão geral Visual como o resumo do espaço de trabalho do designer de exibição, as pastas de trabalho oferecem etapas fixadas, que podem ser fixadas ao seu [painel de portal do Azure](../../azure-portal/azure-portal-dashboards.md). Assim como os blocos de visão geral no resumo do espaço de trabalho, itens de pasta de trabalho fixados serão vinculados diretamente à exibição da pasta de trabalho

Você pode aproveitar o alto nível de recursos de personalização fornecidos com os painéis do Azure, que permite atualização automática, movimentação, dimensionamento e filtragem adicional para seus itens fixados e visualizações. 

![Captura de tela mostra um painel do Azure personalizado Resumo do espaço de trabalho intitulado.](media/view-designer-conversion-access/dashboard.png)

Crie um novo painel do Azure ou selecione um painel existente para iniciar a fixação de itens de pastas de trabalho.

Para fixar um item individual, você precisará habilitar o ícone de pino para sua etapa específica. Para fazer isso, selecione o botão **Editar** correspondente para sua etapa e, em seguida, selecione o ícone de engrenagem para abrir **as configurações avançadas**. Marque a opção para **sempre mostrar o ícone de pino nesta etapa** e um ícone de pino aparecerá no canto superior direito de sua etapa. Esse PIN permite que você fixe visualizações específicas ao seu painel, como os blocos de visão geral.

![Etapa do PIN](media/view-designer-conversion-access/pin-step.png)


Você também pode querer fixar várias visualizações da pasta de trabalho ou todo o conteúdo da pasta de trabalho em um painel. Para fixar a pasta de trabalho inteira, selecione **Editar** na barra de ferramentas superior para alternar o **modo de edição**. Um ícone de pino será exibido, permitindo que você fixe todo o item da pasta de trabalho ou todas as etapas e visualizações individuais na pasta de trabalho.

![Fixar tudo](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Permissões de compartilhamento e exibição 
As pastas de trabalho têm a vantagem de ser um documento privado ou compartilhado. Por padrão, as pastas de trabalho salvas serão salvas em **meus relatórios, o** que significa que apenas o criador pode exibir essa pasta de trabalho.

Você pode compartilhar suas pastas de trabalho selecionando o ícone **compartilhar** na barra de ferramentas superior enquanto estiver no **modo de edição**. Você será solicitado a mover sua pasta de trabalho para **relatórios compartilhados**, o que irá gerar um link que fornece acesso direto à pasta de trabalho.

Para que um usuário exiba uma pasta de trabalho compartilhada, ela deve ter acesso à assinatura e ao grupo de recursos em que a pasta de trabalho é salva.

![Acesso baseado em assinatura](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Próximas etapas

- [Tarefas comuns](view-designer-conversion-tasks.md)