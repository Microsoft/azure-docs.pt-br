---
title: Examinar comentários para ofertas de aplicativos do Azure – marketplace comercial da Microsoft
description: Como lidar com comentários para sua oferta de aplicativo do Azure da equipe de revisão do Microsoft Azure Marketplace. Você pode acessar os comentários no Azure DevOps com suas credenciais do Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ede4e5dd781851c781407a8acea640e3e501f259
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436378"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Como lidar com comentários de análise para ofertas de aplicativos do Azure

Este artigo explica como acessar os comentários da equipe de revisão do Microsoft Azure Marketplace no [Azure DevOps](https://azure.microsoft.com/services/devops/). Se problemas críticos forem localizados na oferta de aplicativo Azure durante a etapa de **revisão da Microsoft**, você poderá entrar nesse sistema para exibir informações detalhadas sobre esses problemas (revisar comentários). Após corrigir todos os problemas, será necessário reenviar a oferta para continuar a publicá-la no Azure Marketplace. O diagrama a seguir ilustra como esse processo de comentários se relaciona com o processo de publicação.

![Examinar o processo de comentários](./media/review-feedback-process.png)

Normalmente, problemas de revisão são referenciados como uma PR (solicitação de pull). Cada PR está vinculada a um item online do Azure DevOps, que contém detalhes sobre o problema. A imagem a seguir exibe um exemplo da experiência do Partner Center quando são encontrados problemas durante as revisões. 

![Status de publicação](./media/publishing-status.png)

A PR que contém detalhes específicos sobre o envio será mencionada no link "Exibir Relatório de Certificação". Para situações complexas, as equipes de suporte e revisão também poderão enviar-lhe um email.

## <a name="azure-devops-access"></a>Acesso ao Azure DevOps

Todos os usuários com acesso à função de "desenvolvedor" no Partner Center terão acesso para ver os itens de PR referenciados em comentários de revisão.

## <a name="reviewing-the-pull-request"></a>Revisar a solicitação de pull

Use o procedimento a seguir para revisar os problemas documentados na solicitação de pull.

1. Na seção **Análise da Microsoft** do formulário de Etapas de publicação, clique em um link de PR para iniciar o navegador e navegue até a página **Visão Geral** (Página Inicial) dessa PR. A imagem a seguir ilustra um exemplo de home page com problema crítico para a oferta de aplicativo de exemplo da Contoso. Essa página contém informações de resumo úteis sobre os problemas de revisão encontrados no aplicativo do Azure.

    [![Home page de solicitação de pull](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique nessa imagem para expandir.*

1. (Opcional) No lado direito da janela, na seção **Políticas**, selecione a mensagem do problema (neste exemplo: **falha na Validação da Política**) para investigar os detalhes de baixo nível do problema, incluindo os arquivos de log associados. Os erros normalmente são exibidos na parte inferior dos arquivos de log.

1. No menu esquerdo da home page, selecione **Arquivos** para exibir os arquivos de lista que compõem os ativos técnicos dessa oferta. Os revisores da Microsoft deverão ter adicionado comentários, descrevendo os problemas críticos descobertos. No exemplo a seguir, dois problemas foram descobertos.

    [![Captura de tela que realça os arquivos e os dois problemas descobertos.](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique nessa imagem para expandir.*

1. Selecione cada nó de comentário na árvore esquerda para navegar até o comentário no contexto do código ao redor. Corrija seu código-fonte no projeto da equipe para corrigir o problema descrito pelo comentário.

>[!Note]
>Não é possível editar os ativos técnicos da oferta no ambiente do Azure DevOps da equipe de revisão. Para publicadores, esse é um ambiente somente leitura para o código-fonte contido. No entanto, você poderá deixar suas respostas aos comentários para o benefício da equipe de revisão da Microsoft.

   No exemplo a seguir, o publicador revisou, corrigiu e respondeu ao primeiro problema.

   ![Primeiro corrigir e comentar resposta](./media/first-comment-reply.png)

## <a name="next-steps"></a>Próximas etapas

Após corrigir os problemas críticos documentados no(s) relatório(s) de revisão, será necessário [republicar a oferta de aplicativo Azure](../create-new-azure-apps-offer.md).
