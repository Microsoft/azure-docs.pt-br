---
title: Modificar conteúdo da página no portal do desenvolvedor no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como editar o conteúdo da página no portal do desenvolvedor no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: fd9902a83a813daffc52e089e6e8bfc4137c5b8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656619"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Modificar o conteúdo e o layout de páginas no portal do desenvolvedor no Gerenciamento de API do Azure
Há três maneiras básicas para personalizar o portal do desenvolvedor no Gerenciamento de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de layout da página][modify-content-layout] (explicado neste guia)
* [Atualizar os estilos usados para elementos de página entre o portal do desenvolvedor][customize-styles]
* [Modificar os modelos usados para as páginas geradas pelo portal][portal-templates] (por exemplo, documentos de API, produtos, autenticação do usuário, etc.)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="page-structure"> </a>Estrutura das páginas do portal do desenvolvedor

O portal do desenvolvedor é baseado em um sistema de gerenciamento de conteúdo. O layout de cada página é criado com base no conjunto de pequenos elementos de página conhecidos como widgets:

![Estrutura de página do portal do desenvolvedor][api-management-customization-widget-structure]

Todos os widgets são editáveis.
* O conteúdo principal específico de cada página está no widget "Conteúdo". A edição de uma página significa a edição do conteúdo desse widget.
* Todos os elementos de layout da página estão contidos nos widgets restantes. As alterações feitas nesses widgets são aplicadas a todas as páginas. Eles são chamados de "widgets de layout".

Na edição de página de rotina, em geral, a modificação é feita somente no widget de conteúdo, que terá conteúdo diferente para cada página.

## <a name="modify-layout-widget"> </a>Modificação do conteúdo de um widget de layout

O Portal do Desenvolvedor pode ser acessado no Portal do Azure.

1. Clique em **Portal do Desenvolvedor** na barra de ferramentas da sua instância do Gerenciamento de API.
2. Para editar o conteúdo de widgets, clique no ícone composto de dois pincéis do menu à esquerda do portal do **Desenvolvedor**.
3. Para modificar o conteúdo do cabeçalho, role até a seção **Cabeçalho** na lista à esquerda.

    Os widgets são editáveis nos campos.
4. Quando estiver pronto para publicar suas alterações, clique em **Publicar** na parte inferior da página.

Agora você poderá ver o novo cabeçalho em cada página no portal do desenvolvedor.

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
