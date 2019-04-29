---
title: Tratar comentários de revisão de aplicativo Azure - Azure Marketplace | Microsoft Docs
description: Explica como usar o Azure DevOps para tratar comentários de revisão para ofertas de aplicativos Azure do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pbutlerm
ms.openlocfilehash: e5fe076cfe733b4fa0151570603c090af98de0e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745485"
---
# <a name="handling-review-feedback"></a>Tratar comentários de revisão

Este artigo explica como acessar o ambiente do Azure DevOps utilizado pela equipe de revisão do Microsoft Azure Marketplace.  Se problemas críticos forem localizados na oferta de aplicativo Azure durante a etapa de **revisão da Microsoft**, você poderá entrar nesse sistema para exibir informações detalhadas sobre esses problemas (revisar comentários).  Após corrigir todos esses problemas, será necessário reenviar a oferta para continuar a publicá-la no Azure Marketplace.  O diagrama a seguir ilustra como esse processo de comentários se relaciona com o processo de publicação.

![Etapas de publicação com comentários do VSTS](./media/pub-flow-vsts-access.png)

Normalmente, problemas de revisão são referenciados como uma PR (solicitação de pull).  Cada PR é vinculada a um item online do [Azure DevOps](https://azure.microsoft.com/services/devops/) (anteriormente nomeado VSTS (Visual Studio Team Services)), que contém detalhes sobre o problema.  A imagem a seguir exibe um exemplo de uma referência de PR de revisão.  Para situações complexas, as equipes de suporte e revisão também poderão enviar-lhe um email. 

![Comentários de revisão de exibição da guia de Status](./media/status-tab-ms-review.png)


## <a name="vsts-access"></a>Acesso do VSTS

Para exibir os itens de PR referenciados nos comentários de revisão, os publicadores deverão primeiro receber a devida autorização.  Caso contrário, novos publicadores receberão uma `401 - Not Authorized` página de resposta ao tentarem exibir as PRs.  Para solicitar acesso a esse repositório do Azure DevOps, execute as seguintes etapas:

1. Colete as seguintes informações:
    - Sua ID e nome de publicador
    - Tipo de oferta (aplicativo do Azure), nome da oferta e ID do SKU
    - O link da solicitação de pull, por exemplo: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` Essa URL pode ser recuperada da mensagem de notificação ou do endereço da página de resposta 401.
    - Os endereços de email dos indivíduos de sua organização de publicação à qual você deseja obter acesso.  Essa lista deverá incluir os endereços de proprietário fornecidos ao registrar-se como um publicador no Portal do Cloud Partner.
2. Crie um incidente de suporte.  Na barra de título do Portal do Cloud Partner, selecione o botão **Ajuda** e, em seguida, escolha **Suporte** no menu.  Seu navegador da Web padrão deverá iniciar e navegar até a página de novo incidente de suporte da Microsoft.  (Talvez você precise entrar primeiro.)
3. Especifique o **Tipo de problema** como **integração do marketplace** e **Categoria** como **Problema de acesso**, em seguida, selecione **Iniciar solicitação**.

    ![Categoria de tíquete de suporte](./media/support-incident1.png)

4. Na página **Etapa 1 de 2**, forneça suas informações de contato e selecione **Continuar**.
5. Na página **Etapa 2 de 2**, especifique um título do incidente (por exemplo `Request VSTS access`) e forneça as informações coletadas na primeira etapa (acima).  Leia e aceite o contrato e selecione **Enviar**.

Se a criação de incidentes for bem-sucedida, uma página de confirmação será exibida.  Salve as informações de confirmação nessa página para sua referência.  A Equipe de Suporte da Microsoft deverá responder à sua solicitação de acesso dentro de alguns dias úteis.


## <a name="reviewing-the-pull-request"></a>Revisar a solicitação de pull 

Use o procedimento a seguir para revisar os problemas documentados na solicitação de pull.

1. Na seção **revisão da Microsoft** do formulário **Etapas de publicação**, clique em um link de PR para iniciar o navegador e navegue até a página **Visão Geral** (página inicial) dessa PR.  A imagem a seguir ilustra um exemplo de página inicial com problema crítico para a oferta de aplicativo de exemplo da Contoso.  Essa página contém informações de resumo úteis sobre os problemas de revisão encontrados no aplicativo do Azure.  

    [![Página inicial de solicitação de pull](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique na imagem para expandir.*
    
2. (Opcional) No lado direito da janela, na seção **Políticas**, clique na mensagem do problema (neste exemplo: **falha na Validação da Política**) para investigar os detalhes de baixo nível do problema, incluindo os arquivos de log associados.  Os erros normalmente são exibidos na parte inferior dos arquivos de log.

3. No menu esquerdo da home page, selecione **Arquivos** para exibir os arquivos de lista que compõem os ativos técnicos dessa oferta.  Os revisores da Microsoft deverão ter adicionado comentários, descrevendo os problemas críticos descobertos.  No exemplo a seguir, dois problemas foram descobertos. 

    [![Página inicial de solicitação de pull](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique na imagem para expandir.*

4. Clique em cada nó de comentário na árvore esquerda para navegar até o comentário no contexto do código ao redor.  Corrija seu código-fonte no projeto da equipe para corrigir o problema descrito pelo comentário.

> [!Note]
> Não é possível editar os ativos técnicos da oferta no ambiente do Azure DevOps da equipe de revisão.  Para publicadores, esse é um ambiente somente leitura para o código-fonte contido.  No entanto, você poderá deixar suas respostas aos comentários para o benefício da equipe de revisão da Microsoft.

   No exemplo a seguir, o publicador revisou, corrigiu e respondeu ao primeiro problema.

   ![Primeiro corrigir e comentar resposta](./media/first-comment-reply.png)


## <a name="next-steps"></a>Próximas etapas

Após corrigir os problemas críticos documentados no(s) relatório(s) de revisão, será necessário [republicar a oferta de aplicativo Azure](./cpp-publish-offer.md).
