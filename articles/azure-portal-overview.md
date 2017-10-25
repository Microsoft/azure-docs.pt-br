---
title: "Visão geral do Portal do Azure | Microsoft Docs"
description: Saiba como usar o portal do Microsoft Azure.
services: 
documentationcenter: 
author: davidwrede
manager: erikre
editor: jimbe
ms.assetid: 53cb9df1-c96a-4f4e-b022-18336cd3d697
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/16/2015
ms.author: dwrede
ms.openlocfilehash: 71820306716c6297085a29f3ceab89b55396bfe6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-portal-overview"></a>Visão geral do portal do Microsoft Azure
O portal do Microsoft Azure é um local central no qual você pode provisionar e gerenciar os recursos do Azure.  Este tutorial mostrará o portal e como usar alguns de seus principais recursos:

* Um **marketplace abrangente** que permite que você procure milhares de itens da Microsoft e de outros fornecedores que podem ser adquiridos e/ou provisionados.
* Uma **experiência de procura unificada e escalonável** que facilita a localização de recursos importantes para você e a execução de várias operações de gerenciamento.
* **Páginas de gerenciamento consistente** (ou folhas) que permitem que você gerencie uma ampla variedade de serviços do Azure por meio de uma maneira consistente de expor configurações, ações, informações de cobrança, monitoramento de integridade e uso de dados, entre outros.
* Uma **experiência pessoal** que permite que você crie uma tela de início personalizada que mostra as informações que quer ver sempre que entrar.  Você também pode personalizar qualquer uma das folhas gerenciamento que contêm blocos.
  
  ![Orientação da interface do usuário do Portal do Azure][UIOrientation]

## <a name="before-you-get-started"></a>Antes de começar
Você precisará de uma assinatura válida do Azure para realizar este tutorial.  Se não tiver uma, [inscreva-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) hoje mesmo.  Quando tiver uma assinatura, você pode acessar o portal em <https://portal.azure.com>.

## <a name="how-to-create-a-resource"></a>Como criar um recurso
O Azure tem um marketplace com milhares de itens que você pode criar em um único lugar.  Digamos que você deseja criar uma nova VM (máquina virtual) do Windows Server 2012.  A hub +NOVO é o ponto de entrada para um conjunto estruturado de categorias em destaque do marketplace.  Cada categoria tem um pequeno conjunto de itens em destaque juntamente com um link para o marketplace completo que mostra todas as categorias e a pesquisa. Para criar essa nova VM do Windows Server 2012, execute as seguintes ações:  

1. O Windows Server 2012 está em destaque, portanto você pode selecioná-lo na categoria Computação.  
2. Preencha algumas informações básicas em um formulário.
3. Clique em 'Criar' e sua VM começará a provisionar imediatamente.

O hub de notificações vai alertá-lo quando o recurso tiver sido criado e uma folha de gerenciamento será aberta (você sempre pode procurar recursos posteriormente).

![Categorias do portal][PortalCategories]

## <a name="how-to-find-your-resources"></a>Como localizar os recursos
Você sempre pode fixar os recursos acessados com frequência na sua tela inicial, mas talvez seja necessário procurar algo que não acessa com frequência.  O hub de procura mostrado abaixo é a maneira de chegar a todos os seus recursos.  Você pode filtrar por assinatura, escolher/redimensionar colunas e navegar até as folhas de gerenciamento clicando em itens individuais.

![Hub de procura][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Como gerenciar e delegar acesso a um recurso
A partir dessa folha, você pode se conectar à máquina virtual usando a área de trabalho remota, monitorar as principais métricas de desempenho, controlar o acesso a essa VM usando o acesso baseado em função (RBAC), configurar a VM e executar outras tarefas de gerenciamento importantes.  A delegação de acesso com base em função é essencial ao gerenciamento em grande escala.  Clique em [aqui](active-directory/role-based-access-control-configure.md) para saber mais sobre isso. Para delegar acesso a um recurso, execute as seguintes ações:

1. Procure o recurso.
2. Clique em 'Todas as configurações' na seção Elementos Essenciais.
3. Clique em 'Usuários' na lista de configurações.
4. Clique em ‘Adicionar’ na barra de comandos.
5. Selecione um usuário e uma função.

![Gerenciando um recurso][ManageResource]

## <a name="how-to-get-help"></a>Como obter ajuda
Se você tiver um problema, estamos à sua disposição.  O portal tem uma página de ajuda e suporte que pode indicar a direção certa.  Dependendo do seu [plano de suporte](https://azure.microsoft.com/support/plans/), você também pode criar tíquetes de suporte diretamente no portal.  Depois de criar um tíquete de suporte, você pode gerenciar o ciclo de vida do tíquete de dentro do portal. Você pode acessar a página de ajuda e suporte navegando até Navegar -> Ajuda + suporte.  

![Ajuda e suporte][HelpSupport]

## <a name="summary"></a>Resumo
Vamos analisar o que você aprendeu neste tutorial:

* Você aprendeu a inscrever-se, a obter uma assinatura e a navegar até o portal
* Você obteve orientação sobre a interface de usuário do portal e aprendeu a criar e a procurar recursos
* Você aprendeu a criar um recurso e a procurar recursos
* Você aprendeu sobre as folhas de estrutura ou de gerenciamento e como pode gerenciar consistentemente diferentes tipos de recursos
* Você aprendeu a personalizar o portal para trazer as informações importantes para a frente e para o centro
* Você aprendeu a controlar o acesso a recursos usando o acesso baseado em função (RBAC)
* Você aprendeu a obter ajuda e suporte

O portal do Microsoft Azure simplifica radicalmente a criação e o gerenciamento de seus aplicativos na nuvem.  Dê uma olhada no [blog de gerenciamento](https://azure.microsoft.com/blog/topics/management/) para se manter atualizado, uma vez que estamos constantemente [ouvindo comentários](https://feedback.azure.com/forums/223579-azure-preview-portal/) e fazendo melhorias.  [ScottGu’s blog (Blog do ScottGu) (Blog do ScottGu)](http://weblogs.asp.net/scottgu) é outro ótimo lugar para procurar todas as atualizações do Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
