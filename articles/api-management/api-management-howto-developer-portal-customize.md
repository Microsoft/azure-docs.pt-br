---
title: Tutorial – Acessar e personalizar o portal do desenvolvedor – Gerenciamento de API do Azure | Microsoft Docs
description: Siga este tutorial para aprender a personalizar o portal do desenvolvedor do Gerenciamento de API, um site gerado de modo automático e totalmente personalizável com a documentação das suas APIs.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012963"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Tutorial: Acessar e personalizar o portal do desenvolvedor

O *portal do desenvolvedor* é um site gerado de modo automático e totalmente personalizável com a documentação das suas APIs. É nele que os consumidores de APIs podem descobrir suas APIs, aprender a usá-las e solicitar acesso.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Acessar a versão gerenciada do portal do desenvolvedor
> * Navegar pela interface administrativa
> * Personalizar o conteúdo
> * Publicar as alterações
> * Ver o portal publicado

Encontre mais detalhes sobre o portal do desenvolvedor na [Visão geral do portal do desenvolvedor do Gerenciamento de API do Azure](api-management-howto-developer-portal.md).

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="Portal do desenvolvedor do Gerenciamento de API – modo de administrador" border="false":::

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)
- Importar e publicar uma instância de gerenciamento de API do Azure. Para obter mais informações, confira [Importação e publicação](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Acessar o portal como administrador

Siga as etapas abaixo para acessar a versão gerenciada do portal.

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. Selecione o botão **Portal do desenvolvedor** na barra de navegação superior. Uma nova guia do navegador com uma versão administrativa do portal será aberta.

## <a name="understand-the-portals-administrative-interface"></a>Noções básicas sobre a interface administrativa do portal

### <a name="default-content"></a>Conteúdo padrão 

Se você estiver acessando o portal pela primeira vez, o conteúdo padrão será automaticamente provisionado em segundo plano. O conteúdo padrão foi projetado para apresentar as funcionalidades do portal e minimizar as personalizações necessárias para personalizar seu portal. Saiba mais sobre o que está incluído no conteúdo do portal na [Visão geral do portal do desenvolvedor do Gerenciamento de API do Azure](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Editor visual

Personalize o conteúdo do portal com o editor visual. 
* As seções do menu à esquerda permitem criar ou modificar páginas, mídia, layouts, menus, estilos ou configurações de site. 
* Os itens de menu na parte inferior permitem alternar entre os visores (por exemplo, móvel ou área de trabalho), exibir os elementos do portal visíveis para usuários autenticados ou anônimos ou salvar ou desfazer ações.
* Adicione linhas a uma página clicando em um ícone azul com um sinal de adição. 
* Os widgets (por exemplo, texto, imagens ou lista de APIs) podem ser adicionados clicando em um ícone cinza com um sinal de adição.
* Reorganize os itens em uma página com a interação do tipo "arrastar e soltar". 

### <a name="layouts-and-pages"></a>Layouts e páginas

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Páginas e layouts" border="false":::

Os layouts definem como as páginas são exibidas. Por exemplo, no conteúdo padrão, há dois layouts: um se aplica à home page e o outro, a todas as páginas restantes.

Um layout é aplicado a uma página por meio da correspondência do modelo de URL à URL da página. Por exemplo, um layout com um modelo de URL igual a `/wiki/*` será aplicado a cada página com o segmento de `/wiki/` na URL: `/wiki/getting-started`, `/wiki/styles` etc.

Na imagem anterior, o conteúdo que pertence ao layout é marcado em azul, enquanto a página é marcada em vermelho. As seções do menu são marcadas respectivamente.

### <a name="styling-guide"></a>Guia de estilo

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Guia de estilo" border="false":::

O guia de estilo é um painel criado com os designers em mente. Ele permite supervisionar e estilizar todos os elementos visuais do portal. O estilo é hierárquico: muitos elementos herdam propriedades de outros elementos. Por exemplo, os elementos de botão usam cores para texto e tela de fundo. Para alterar a cor de um botão, você precisa alterar a variante da cor original.

Para editar uma variante, selecione-a e escolha o ícone de lápis exibido na parte superior dela. Depois de fazer as alterações na janela pop-up, feche-a.

### <a name="save-button"></a>Botão Salvar

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Botão Salvar" border="false":::

Sempre que você fizer uma alteração no portal, precisará salvá-la manualmente selecionando o botão **Salvar** no menu, na parte inferior, ou clicar em [CTRL] + [S]. Quando você salva as alterações, o conteúdo modificado é carregado automaticamente no serviço Gerenciamento de API.

## <a name="customize-the-portals-content"></a>Personalizar o conteúdo do portal

Antes de disponibilizar seu portal para os visitantes, personalize o conteúdo gerado automaticamente. As alterações recomendadas incluem layouts, estilos e o conteúdo da home page.

> [!NOTE]
> Devido a considerações de integração, as seguintes páginas não podem ser removidas nem movidas para outra URL: `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, `/signin`, `/signin-sso` e `/signup`.

### <a name="home-page"></a>Página inicial

A página **Inicial** padrão é preenchida com um conteúdo de espaço reservado. Remova seções inteiras que tenham esse conteúdo ou mantenha a estrutura e ajuste os elementos um a um. Substitua o texto gerado e as imagens por um conteúdo próprio e verifique se os links apontam para as localizações desejadas.

### <a name="layouts"></a>Layouts

Substitua o logotipo gerado automaticamente na barra de navegação pela sua imagem.

### <a name="styling"></a>Estilo

Embora não seja necessário ajustar nenhum estilo, você poderá considerar o ajuste de determinados elementos. Por exemplo, altere a cor primária para que ela corresponda à cor da sua marca.

### <a name="customization-example"></a>Exemplo de personalização

No vídeo a seguir, demonstramos como editar o conteúdo do portal, personalizar a aparência do site e publicar as alterações.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Publicar o portal

Para disponibilizar seu portal e as últimas alterações dele para os visitantes, você precisará *publicá-lo*. Publique o portal na interface administrativa do portal ou no portal do Azure.

### <a name="publish-from-the-administrative-interface"></a>Publicação por meio da interface administrativa

1. Lembre-se de salvar as alterações selecionando o ícone **Salvar**.
1. Na seção **Operações** do menu, selecione **Publicar site**. Esta operação pode levar alguns minutos.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Publicar o portal" border="false":::

### <a name="publish-from-the-azure-portal"></a>Publicação por meio do portal do Azure

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. No menu à esquerda, em **Portal do desenvolvedor**, escolha **Visão geral do portal**.
1. Na janela **Visão geral do portal**, selecione **Publicar**.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Publicar o portal por meio do portal do Azure":::

> [!NOTE]
> O portal precisará ser publicado novamente após as alterações de configuração do serviço Gerenciamento de API. Por exemplo, publique o portal novamente depois de atribuir um domínio personalizado, atualizar os provedores de identidade, definir a delegação ou especificar a entrada e os termos do produto.


## <a name="visit-the-published-portal"></a>Visitar o portal publicado

Depois de publicar o portal, acesse-o na mesma URL do painel administrativo, por exemplo, `https://contoso-api.developer.azure-api.net`. Veja-o em uma sessão de navegador separada (usando o modo de navegação particular ou anônimo) como um visitante externo.

## <a name="apply-the-cors-policy-on-apis"></a>Aplicar a política do CORS em APIs

Para permitir que os visitantes do seu portal testem as APIs por meio do console interativo interno, habilite o CORS (compartilhamento de recursos entre origens) nas suas APIs. Para obter detalhes, confira a [Visão geral do portal do desenvolvedor do Gerenciamento de API do Azure](api-management-howto-developer-portal.md#cors).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o portal do desenvolvedor:

- [Visão Geral do portal do desenvolvedor do Gerenciamento de API do Azure](api-management-howto-developer-portal.md)
- [Migre para o novo portal do desenvolvedor](developer-portal-deprecated-migration.md) do portal herdado preterido.