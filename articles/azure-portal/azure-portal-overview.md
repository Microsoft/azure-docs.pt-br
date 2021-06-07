---
title: Visão geral do portal do Azure
description: O portal do Azure é uma interface gráfica do usuário que você pode usar para gerenciar seus serviços do Azure. Saiba como navegar e localizar recursos no portal do Azure.
keywords: portal
ms.date: 03/12/2021
ms.topic: overview
ms.openlocfilehash: 8f61934e6af150c18d1331c2b28e2686641967d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563987"
---
# <a name="azure-portal-overview"></a>Visão geral do portal do Azure

Este artigo apresenta o portal do Azure, identifica os elementos da página do portal e ajuda você a se familiarizar com a experiência de gerenciamento no portal do Azure.

## <a name="what-is-the-azure-portal"></a>O que é o portal do Azure?

O portal do Azure é um console unificado baseado na Web que fornece uma alternativa para as ferramentas de linha de comando. Com o portal do Azure, você pode gerenciar sua assinatura do Azure usando uma interface gráfica do usuário. Você pode criar, gerenciar e monitorar tudo, desde aplicativos Web simples a implantações em nuvem complexas. Crie painéis personalizados para ter uma exibição organizada dos recursos. Configure opções de acessibilidade para ter a experiência ideal.

O portal do Azure foi projetado para ter resiliência e disponibilidade contínua. Ele está presente em todos os datacenters do Azure. Essa configuração o torna resiliente a falhas em datacenters individuais e evita lentidão na rede por manter a proximidade com os usuários. O portal do Azure é atualizado continuamente e não requer nenhum tempo de inatividade para atividades de manutenção.

## <a name="azure-portal-menu"></a>Menu do portal do Azure

Você pode escolher o modo padrão do menu do portal. Ele pode ser encaixado ou pode atuar como um painel de submenu.

Quando o menu do portal está no modo de submenu, ele fica oculto até que você precise dele. Selecione o ícone de menu para abrir ou fechar o menu.

![Menu do portal do Azure no modo de submenu](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Se você escolher o modo encaixado para o menu do portal, ele sempre estará visível. Você pode recolher o menu para oferecer mais espaço de trabalho.

![Menu do portal do Azure no modo encaixado](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Página Inicial do Azure

Como novo assinante dos serviços do Azure, a primeira coisa que você vê depois de [entrar no portal](https://portal.azure.com) é a **Página Inicial do Azure**. Esta página compila os recursos que ajudam você a aproveitar ao máximo sua assinatura do Azure. Incluímos links para cursos online gratuitos, a documentação, os serviços principais e sites úteis para permanecer atualizado e administrar mudanças para sua organização. Para oferecer acesso rápido e fácil ao trabalho em andamento, também mostramos uma lista dos recursos visitados mais recentemente. Você não pode personalizar esta página, mas pode escolher se deseja ver a **Página Inicial do Azure** ou o **Painel do Azure** como exibição padrão. Na primeira vez que você entra, há um prompt na parte superior da página em que você pode salvar sua preferência.

![Captura de tela mostrando onde salvar sua preferência.](./media/azure-portal-overview/azure-portal-default-view.png)

O menu do portal do Azure e o modo a exibição padrão do Azure podem ser alterados nas **Configurações do portal**. Se você alterar a seleção, a alteração será aplicada imediatamente.

![Captura de tela mostrando o seletor de exibição padrão](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Painel do Azure

Os painéis fornecem uma exibição com foco nos recursos da assinatura que são mais importantes para você. Nós fornecemos um painel padrão para você começar. Você pode personalizá-lo para colocar os recursos usados com frequência em uma só exibição. As alterações feitas na exibição padrão afetam apenas a sua experiência. No entanto, você pode criar painéis adicionais para uso próprio ou publicar seus painéis personalizados e compartilhá-los com outros usuários de sua organização. Para obter mais informações, confira [Criar e compartilhar painéis no portal do Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Conhecendo o portal

É útil entender o layout básico do portal e como interagir com ele. Aqui, apresentaremos os componentes da interface do usuário e parte da terminologia que usamos para fornecer instruções. Para fazer um tour mais detalhado do portal, confira a lição do curso [Navegar pelo portal](/learn/modules/tour-azure-portal/3-navigate-the-portal).

O menu do portal do Azure e o cabeçalho da página são elementos globais que sempre estão presentes. Esses recursos persistentes são o "shell" da interface do usuário associada a cada serviço ou recurso individual, e o cabeçalho fornece acesso a controles globais. A página de configuração (às vezes chamada de "folha") de um recurso também pode ter um menu de recursos para ajudar você a se movimentar entre os recursos.

A figura abaixo rotula os elementos básicos do portal do Azure, cada um dos quais é descrito na tabela a seguir.

![Captura de tela mostrando a exibição do portal em tela inteira e a chave para os elementos da interface do usuário](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Captura de tela mostrando o menu do portal expandido](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Chave|Descrição
|:---:|---|
|1|Cabeçalho da página. Aparece na parte superior de todas as páginas do portal e contém elementos globais.|
|2| Pesquisa global. Use a barra de pesquisa para localizar rapidamente um recurso, um serviço ou uma documentação específica.|
|3|Controles globais. Assim como todos os elementos globais, esses recursos persistem por todo o portal e incluem: o Cloud Shell, o filtro de assinatura, as notificações, as configurações do portal, a ajuda e o suporte e o envio de comentários.|
|4|Sua conta. Veja informações sobre sua conta, alterne entre diretórios, saia ou entre com uma conta diferente.|
|5|Menu do portal. O menu do portal é um elemento global que ajuda você a navegar entre serviços. Às vezes chamado de barra lateral, o modo de menu do portal pode ser alterado nas **Configurações do portal**.|
|6|Menu de recursos. Muitos serviços incluem um menu de recursos para ajudar você a gerenciar o serviço. Você pode ver esse elemento ser chamado de painel esquerdo.|
|7|Barra de comandos. Os controles na barra de comandos são contextuais com relação ao seu foco atual.|
|8|Painel de trabalho.  Exibe os detalhes sobre o recurso que está em foco.|
|9|Trilha. Você pode usar os links de trilha para voltar um nível no fluxo de trabalho.|
|10|Controle mestre para criar um recurso na assinatura atual. Expanda ou abra o menu do portal para localizar **+ Criar um recurso**. Pesquise ou procure no Azure Marketplace o tipo de recurso que deseja criar.|
|11|Sua lista de favoritos. Confira [Adicionar, remover e classificar favoritos](../azure-portal/azure-portal-add-remove-sort-favorites.md) para saber como personalizar a lista.|

## <a name="get-started-with-services"></a>Introdução aos serviços

Se for um novo assinante, você precisará criar um recurso antes que haja qualquer coisa a ser gerenciada. Selecione **+ Criar um recurso** para ver os serviços disponíveis no Azure Marketplace. Você encontrará centenas de aplicativos e serviços de muitos provedores, todos eles certificados para execução no Azure.

Preenchemos previamente seus Favoritos na barra lateral com links para os serviços mais usados.  Para ver todos os serviços disponíveis, selecione **Todos os serviços** na barra lateral.

> [!TIP]
> A maneira mais rápida de localizar um recurso, um serviço ou uma documentação é usar *Pesquisar* no cabeçalho global. Use os links de trilha para voltar para páginas anteriores.
>
Assista a este vídeo para ver uma demonstração de como usar a pesquisa global no portal do Azure.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Como usar a pesquisa global no portal do Azure](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre onde executar o portal do Azure em [Navegadores e dispositivos com suporte](../azure-portal/azure-portal-supported-browsers-devices.md)
* Permaneça conectado quando estiver em movimento com o [Aplicativo móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* Integre e configure seu ambiente de nuvem com o [Centro de Início Rápido do Azure](../azure-portal/azure-portal-quickstart-center.md)