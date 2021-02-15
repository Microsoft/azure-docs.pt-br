---
title: Integrar a área de trabalho virtual do Windows com o Azure Advisor-Azure
description: Como usar o Azure Advisor com sua implantação de área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76b7f97b6020a3a0d5571a3a105d15f7d7893485
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147354"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Usar o Azure Advisor com a área de trabalho virtual do Windows

O Azure Advisor pode ajudar os usuários a resolver problemas comuns por conta própria, sem precisar arquivar casos de suporte. As recomendações reduzem a necessidade de enviar solicitações de ajuda, economizando tempo e custos.

Este artigo lhe dirá como configurar o assistente do Azure em sua implantação de área de trabalho virtual do Windows para ajudar seus usuários.

## <a name="what-is-azure-advisor"></a>O que é o Azure Advisor?

O Azure Advisor analisa suas configurações e telemetria para oferecer recomendações personalizadas para resolver problemas comuns. Com essas recomendações, você pode otimizar seus recursos do Azure para confiabilidade, segurança, excelência operacional, desempenho e custo. Saiba mais no [site do Azure Advisor](https://azure.microsoft.com/services/advisor/).

## <a name="how-to-start-using-azure-advisor"></a>Como começar a usar o Azure Advisor

Tudo o que você precisa para começar é uma conta do Azure no portal do Azure. Primeiro, abra o portal do Azure em <https://portal.azure.com/#home> , em seguida, selecione **Advisor** em **Serviços do Azure**, conforme mostrado na imagem a seguir. Você também pode inserir "Azure Advisor" na barra de pesquisa na portal do Azure.

> [!div class="mx-imgBorder"]
> ![Uma captura de tela da portal do Azure. O usuário está passando o cursor do mouse sobre o link do supervisor do Azure, fazendo com que um menu suspenso seja exibido.](media/azure-advisor.png)

Ao abrir o Azure Advisor, você verá cinco categorias:

- Cost
- Segurança
- Confiabilidade
- Excelência operacional
- Desempenho

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do Azure Advisor mostrando os cinco menus para cada categoria. Os cinco itens exibidos em suas próprias caixas são custo, segurança, confiabilidade, excelência operacional e desempenho.](media/advisor-categories.png)

Ao selecionar uma categoria, você vai para sua página recomendações ativas. Nessa página, você pode exibir as recomendações que o Azure Advisor tem para você, conforme mostrado na imagem a seguir.

> [!div class="mx-imgBorder"]
> ![Uma captura de tela da lista de recomendações ativas para excelência operacional. A lista mostra sete recomendações com níveis de prioridade variáveis.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Dicas adicionais para o Azure Advisor

- Certifique-se de verificar suas recomendações com frequência, pelo menos mais de uma vez por semana. O Azure Advisor atualiza suas recomendações ativas várias vezes por dia. A verificação de novas recomendações pode evitar problemas maiores, ajudando você a identificar e resolver menores.

- Sempre tente resolver os problemas com o nível de prioridade mais alto no Azure Advisor. Problemas de alta prioridade são marcados com vermelho. Deixar recomendações de alta prioridade não resolvidas pode levar a problemas na linha.

- Se uma recomendação parecer menos importante, você poderá descartá-la ou adiar. Para ignorar ou adiar uma recomendação, vá para a coluna **ação** e altere o estado do item.

- Não descartar recomendações até saber por que elas estão aparecendo e tem certeza de que ela não terá um impacto negativo sobre você ou seus usuários. Sempre selecione **saiba mais** para ver qual é o problema. Se você resolver um problema seguindo as instruções no Azure Advisor, ele desaparecerá automaticamente da lista. Você está melhor Solucionando problemas do que os adiando repetidamente.

- Sempre que você chegar a um problema na área de trabalho virtual do Windows, sempre verifique o Azure Advisor primeiro. O assistente do Azure fornecerá instruções sobre como solucionar o problema ou, pelo menos, apontará para um recurso que pode ajudar.

## <a name="next-steps"></a>Próximas etapas

Para saber como resolver as recomendações, consulte [como resolver as recomendações do Azure Advisor](azure-advisor-recommendations.md).

Se você tiver sugestões para novas recomendações, poste-as em nosso [Fórum do Azure Advisor User Voice](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations).
