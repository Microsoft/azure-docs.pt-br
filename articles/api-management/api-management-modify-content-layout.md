---
title: "Modificar conteúdo da página no portal do desenvolvedor no Gerenciamento de API do Azure | Microsoft Docs"
description: "Saiba como editar o conteúdo da página no portal do desenvolvedor no Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: antonba
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
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Modificar o conteúdo e o layout de páginas no portal do desenvolvedor no Gerenciamento de API do Azure
Há três maneiras básicas para personalizar o portal do desenvolvedor no Gerenciamento de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de layout da página][modify-content-layout] (explicado neste guia)
* [Atualizar os estilos usados para elementos de página entre o portal do desenvolvedor][customize-styles]
* [Modificar os modelos usados para as páginas geradas pelo portal][portal-templates] (por exemplo, documentos de API, produtos, autenticação do usuário, etc.)

## <a name="page-structure"> </a>Estrutura das páginas do portal do desenvolvedor

O portal do desenvolvedor é baseado em um sistema de gerenciamento de conteúdo. O layout de cada página é criado com base no conjunto de pequenos elementos de página conhecidos como widgets:

![Estrutura de página do portal do desenvolvedor][api-management-customization-widget-structure]

Todos os widgets são editáveis. 
* O conteúdo principal específico de cada página está no widget "Conteúdo". A edição de uma página significa a edição do conteúdo desse widget.
* Todos os elementos de layout da página estão contidos nos widgets restantes. As alterações feitas nesses widgets serão aplicadas a todas as páginas. Eles serão chamados de "widgets de layout".

Na edição de página de rotina é comum a modificação somente do widget de conteúdo, que terá conteúdo diferente para cada página.

## <a name="modify-layout-widget"> </a>Modificação do conteúdo de um widget de layout

O conteúdo no portal do desenvolvedor é modificado por meio do portal do editor, que pode ser acessado do Portal do Azure. Para alcançá-lo, clique em **Portal do editor** na barra de ferramentas do serviço da sua instância do Gerenciamento de API.

![Portal do editor][api-management-management-console]

Para editar o conteúdo do widget, clique em **Widgets** no menu do **Portal do Desenvolvedor** à esquerda. Neste exemplo, vamos modificar o conteúdo do widget Cabeçalho. Selecione o widget **Cabeçalho** na lista.

![Cabeçalho de widget][api-management-widgets-header]

O conteúdo do cabeçalho é editável no campo **Corpo** . Altere o texto como desejar e, em seguida, clique em **Salvar** na parte inferior da página.

Agora você poderá ver o novo cabeçalho em cada página no portal do desenvolvedor.

> Para abrir o portal do desenvolvedor enquanto estiver no portal do publicador, clique em **Portal do desenvolvedor** na barra superior.
> 
> 

## <a name="edit-page-contents"> </a>Editar o conteúdo de uma página

Para ver uma lista de todas as páginas de conteúdo existentes, clique em **Conteúdo** no menu **Portal do desenvolvedor** no portal do publicador.

![Gerenciar conteúdo][api-management-customization-manage-content]

Clique na página **Bem-vindo** para editar o que é exibido na home page do portal do desenvolvedor. Faça as alterações desejadas, visualize-as se necessário e depois clique em **Publicar Agora** para torná-las visíveis para todos.

> A home page usa um layout especial que permite exibir uma faixa na parte superior. Essa faixa não é editável na seção **Conteúdo** . Para editá-la, clique em **Widgets** no menu **Portal do desenvolvedor**, selecione **Home page** na lista suspensa **Camada Atual** e abra o item **Faixa** na seção **Em destaque**. O conteúdo desse widget pode ser editado como o conteúdo de qualquer outra página.
> 
> 

## <a name="next-steps"> </a>Próximas etapas
* [Atualizar os estilos usados para elementos de página entre o portal do desenvolvedor][customize-styles]
* [Modificar os modelos usados para as páginas geradas pelo portal][portal-templates] (por exemplo, documentos de API, produtos, autenticação do usuário, etc.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
