---
title: Criar e compartilhar painéis do Portal do Azure | Microsoft Docs
description: Este artigo explica como criar e editar painéis no portal do Azure.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: doubeby
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: kfollis
ms.openlocfilehash: 693e973fb988a57c15b4ea2fae47f16b4ff39011
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552490"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Criar e compartilhar painéis no Portal do Azure
Você pode criar vários painéis e compartilhá-los com outras pessoas que tenham acesso às suas assinaturas do Azure.  Este artigo percorre as noções básicas da criação/edição, publicação e gerenciamento de acesso aos painéis.

## <a name="create-a-dashboard"></a>Criar um painel
Para criar um painel, selecione o botão **Novo painel** ao lado do nome do painel atual.  

![criar painel](./media/azure-portal-dashboards/new-dashboard.png)

Essa ação cria um painel novo, vazio, privado e coloca você no modo de personalização onde é possível nomear seu painel e adicionar ou reorganizar blocos.  Nesse modo, a galeria de blocos recolhíveis assume o menu de navegação à esquerda.  A galeria de blocos permite que você encontre blocos para os recursos do Azure de várias maneiras: você pode procurar por [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), pelo tipo de recurso, por [marca](../azure-resource-manager/resource-group-using-tags.md) ou procurando por seus recursos por nome.  

![personalizar painel](./media/azure-portal-dashboards/customize-dashboard.png)

Adicione blocos arrastando e soltando-os na superfície do painel onde você desejar.

Há uma nova categoria chamada **Geral** para blocos que não estejam associados a um recurso específico.  Neste exemplo, podemos fixar o bloco Markdown.  Use este bloco para adicionar conteúdo personalizado ao seu painel.  O bloco dá suporte a texto sem formatação, [sintaxe de Markdown](https://daringfireball.net/projects/markdown/syntax)e um conjunto limitado de HTML.  (Para segurança, você não pode fazer coisas como injetar marcas `<script>` ou usar um determinado elemento de estilo de CSS que podem interferir com o portal). 

![adicionar markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Editar um painel
Depois de criar seu painel, você pode fixar blocos da galeria de blocos ou a representação de bloco de folhas. Vamos fixar a representação do nosso grupo de recursos. Você pode fixar ao procurar o item, ou da folha do grupo de recursos. As duas abordagens resultam na fixação da representação do bloco do grupo de recursos.

![Fixar no painel](./media/azure-portal-dashboards/pin-to-dashboard.png)

Depois de fixar o item, ele aparecerá em seu painel.

![exibir painel](./media/azure-portal-dashboards/view-dashboard.png)

Agora que temos um bloco Markdown e um grupo de recursos fixado ao painel, podemos redimensionar e reorganizar os blocos em um layout adequado.

Ao passar o mouse e selecionar "…" ou clicar com o botão direito do mouse em um bloco, você poderá ver todos os comandos contextuais do bloco. Por padrão, há dois itens:

1. **Desafixar do painel** – remove o bloco do painel
2. **Personalizar** – entra no modo de personalização

![personalizar bloco](./media/azure-portal-dashboards/customize-tile.png)

Selecionando Personalizar, você pode redimensionar e reorganizar blocos. Para redimensionar uma imagem, selecione o novo tamanho no menu contextual, conforme mostrado na imagem a seguir.

![redimensionar bloco](./media/azure-portal-dashboards/resize-tile.png)

Ou, se o bloco der suporte a qualquer tamanho, você poderá arrastar o canto inferior direito para o tamanho desejado.

![redimensionar bloco](./media/azure-portal-dashboards/resize-corner.png)

Depois de redimensionar os blocos, exiba o painel.

![bloco de exibição](./media/azure-portal-dashboards/view-tile.png)

Quando tiver concluído a personalização de um painel, basta selecionar **Personalização concluída** para sair no modo de personalização ou pressione o botão direito do mouse e selecione **Personalização concluída** no menu de contexto.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Publicar um painel e gerenciar o controle de acesso
Quando você cria um painel, ele será privado por padrão, o que significa que você é a única pessoa que pode vê-lo.  Para torná-lo visível para outras pessoas, use o botão **Compartilhar** exibido juntamente com os outros comandos do painel.

![compartilhar painel](./media/azure-portal-dashboards/share-dashboard.png)

Será solicitado que você escolha uma assinatura e o grupo de recursos para que seu painel seja publicado. Para integrar totalmente os painéis ao ecossistema, implementamos painéis compartilhados como recursos do Azure (e, por isso, não é possível compartilhar ao digitar um endereço de email).  O acesso às informações exibidas pela maioria dos blocos no portal é controlado pelo [Controle de Acesso Baseado em Função do Azure](../role-based-access-control/role-assignments-portal.md). Do ponto de vista do controle de acesso, os painéis compartilhados não são diferentes de uma máquina virtual ou de uma conta de armazenamento.  

Digamos que você tenha uma assinatura do Azure e membros de sua equipe receberam as funções de **proprietário**, **colaborador** ou **leitor** da assinatura.  Os usuários que são proprietários ou colaboradores podem listar, exibir, criar, modificar ou excluir painéis na assinatura.  Os usuários que são os leitores podem listar e exibir os painéis, mas não podem modificá-los ou excluí-los.  Os usuários com acesso de leitor podem fazer edições locais em um painel compartilhado, mas não podem publicar essas alterações no servidor.  No entanto, eles podem fazer uma cópia privada do painel para seu próprio uso.  Como sempre, os blocos individuais no painel impõem suas próprias regras de controle de acesso com base nos recursos aos quais eles correspondem.  

Para sua conveniência, a experiência de publicação do portal guia você por um padrão onde você coloca painéis em um grupo de recursos chamado **painéis**.  

![painel de publicação](./media/azure-portal-dashboards/publish-dashboard.png)

Você pode optar por publicar um painel em um determinado grupo de recursos.  O controle de acesso para esse painel corresponde ao controle de acesso para o grupo de recursos.  Os usuários que podem gerenciar os recursos desse grupo de recursos também têm acesso aos painéis.

![painel de publicação para o grupo de recursos](./media/azure-portal-dashboards/publish-to-resource-group.png)

Depois que o painel é publicado, o painel de controle **Compartilhamento + acesso** será atualizado e mostrará informações sobre o painel publicado, incluindo um link para gerenciar o acesso de usuário ao painel.  Isso inicia a folha Controle de Acesso Baseado em Função padrão usada para gerenciar o acesso a qualquer recurso do Azure.  Você sempre pode voltar a esta exibição ao selecionar **Compartilhar**.

![gerenciar o controle de acesso](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Próximas etapas
* Para gerenciar recursos, consulte [gerenciar recursos do Azure usando o portal do Azure](../azure-resource-manager/manage-resources-portal.md).
* Para implantar recursos, confira [Implantar recursos com modelos do Resource Manager e o Portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

