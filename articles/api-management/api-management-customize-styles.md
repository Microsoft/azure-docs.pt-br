---
title: Personalizar estilos no portal do desenvolvedor no Gerenciamento de API do Azure | Microsoft Docs
description: "Saiba como modificar os estilos usados para qualquer página no portal do desenvolvedor no Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.openlocfilehash: 89baf60d0204a1701e93309f09b90bc94c4ca57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>Personalizar o estilo do portal do desenvolvedor no Gerenciamento de API do Azure
Há três maneiras básicas para personalizar o portal do desenvolvedor no Gerenciamento de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de layout da página][modify-content-layout]
* [Atualizar os estilos usados para elementos de página entre o portal do desenvolvedor][customize-styles] (explicado neste guia)
* [Modificar os modelos usados para as páginas geradas pelo portal][portal-templates] (por exemplo, documentos de API, produtos, autenticação do usuário, etc.)

## <a name="change-headers-styling"> </a>Alterar o estilo dos elementos da página

As cores, fontes, tamanhos, espaços e outros elementos relacionados ao estilo de qualquer página no portal são definidos pelas regras de status. 

A edição das regras de estilo é feita no **Portal do desenvolvedor** ao entrar como um administrador. Para chegar lá, primeiro abra o Portal do Azure e clique em **Portal do editor** na barra de ferramentas de serviço da sua instância de Gerenciamento de API.

![Portal do editor][api-management-management-console]

Em seguida, clique em **Portal do desenvolvedor** no canto superior direito. 

![Link do portal do desenvolvedor no portal do editor][api-management-pp-dp-link]

Para abrir a barra de ferramentas de personalização mova o mouse sobre o ícone de personalização (ou selecione-o) e, em seguida, clique em "estilos" na barra de ferramentas.

![Botão da barra de personalização][api-management-customization-toolbar-button]

Há duas maneiras principais de editar as regras de estilo - você pode examinar a lista de todas as regras de estilo usadas em qualquer lugar que é exibida por padrão e modificar um estilo conforme necessário, ou você pode escolher **selecionar um elemento na página** e, em seguida, clique em qualquer lugar na página para ver somente os estilos para esse elemento.

![Barra de ferramentas de personalização][api-management-customization-toolbar]

Clique na opção **Selecionar um elemento na página** para este exemplo.  Os elementos agora ficam destacados à medida que você passa o ponteiro do mouse sobre eles, indicando quais estilos do elemento você começaria a editar se clicasse. Mova o mouse sobre o texto no cabeçalho (normalmente há o nome da empresa aqui) e, em seguida, clique nele. Um conjunto de regras de estilo categorizadas e nomeadas é exibido no editor de estilos. Cada regra representa uma propriedade de estilo do elemento selecionado. Por exemplo, para o texto do cabeçalho selecionado acima, o tamanho do texto está em @font-size-h1, enquanto que o nome da fonte com alternativas está em @headings-font-family.

> Se você está familiarizado com [bootstrap][bootstrap], essas regras são, na verdade, [variáveis LESS][LESS variables] no tema do bootstrap usadas pelo portal do desenvolvedor.
> 
> 

Vamos alterar a cor do texto do cabeçalho. Selecione a entrada no campo **@headings-color** e digite **#000000**. Esse é o código hexadecimal para a cor preta. Quando você fizer isso, verá que um indicador de cor quadrado aparece no final da caixa de texto. Se você clicar nesse indicador, um seletor de cor permitirá que você escolha uma cor.

![Seletor de cor][api-management-customization-toolbar-color-picker]

As alterações são visualizadas em tempo real como torná-los, mas ficam visíveis somente para administradores. Para tornar essas alterações visíveis para qualquer um, clique no botão **Publicar** no editor de estilos e confirme as alterações.

![Menu Publicar][api-management-customization-toolbar-publish-form]

> Para alterar as regras de estilo que se aplicam a qualquer outro elemento na página, siga o mesmo procedimento utilizado para o cabeçalho. Clique em **Selecionar um elemento na página** no editor de estilos, selecione o elemento em que você está interessado e comece a modificar os valores das regras de estilo exibidas na tela.
> 
> 


## <a name="next-steps"> </a>Próximas etapas
* Saiba como personalizar o conteúdo das páginas do portal do desenvolvedor usando [Modelos de portal do desenvolvedor](api-management-developer-portal-templates.md).

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/
