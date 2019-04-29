---
title: Como proteger o acesso ao Catálogo de Dados do Azure
description: Este artigo explica como proteger o catálogo de dados e seus ativos.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 6c09b509399647f4cacbc96427200da5a1b00ac9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61000682"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Como proteger o acesso ao catálogo de dados e ativos de dados
> [!IMPORTANT]
> Esse recurso está disponível somente na Standard Edition do Catálogo de Dados do Azure.

O Catálogo de Dados do Azure permite que você especifique quem pode acessar o catálogo de dados e quais operações (registrar, anotar, apropriar-se) eles podem executar em metadados no catálogo. 

## <a name="catalog-users-and-permissions"></a>Usuários e permissões do catálogo
Para dar a um usuário ou grupo o acesso a um catálogo de dados e definir permissões:

1. Na [home page do seu catálogo de dados](https://www.azuredatacatalog.com), clique em **Configurações** na barra de ferramentas.

    ![catálogo de dados – configurações](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. Na página configurações, expanda a seção **Usuários do Catálogo**.
    ![Catálogo de Usuários – Adicionar](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Clique em **Adicionar**.
4. Insira o **nome de usuário** totalmente qualificado ou nome do **grupo de segurança** no AAD (Azure Active Directory) associado ao catálogo. Use vírgula (', ') como um separador se você estiver adicionando mais de um usuário ou grupo.
    ![Usuários do Catálogo – usuários ou grupos](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Pressione **ENTER** ou **TAB** fora da caixa de texto. 
6.  Confirme que todas as permissões (**Anotar**, **Registrar** e **Apropriar-se**) sejam atribuídos a esses usuários ou grupos por padrão. Ou seja, o usuário ou grupo pode [registrar ativos de dados]( data-catalog-how-to-register.md), [anotar os ativos de dados]( data-catalog-how-to-annotate.md) e [assumir a propriedade de ativos de dados]( data-catalog-how-to-manage.md). 
    ![Usuários do Catálogo – permissões padrão](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Para conceder a um usuário ou grupo apenas o acesso de leitura para o catálogo, desmarque a opção **anotar** para esse usuário ou grupo. Quando você fizer isso, o usuário ou grupo não poderá anotar os ativos de dados no catálogo, mas poderá exibi-los. 
8.  Para negar a um usuário ou grupo o registro de recursos de dados, desmarque a opção **registrar** para esse usuário ou grupo.
9.  Para negar que um usuário assuma a propriedade de um ativo de dados, desmarque a opção **apropriar-se** para esse usuário ou grupo. 
10. Para excluir um usuário/grupo de usuários do catálogo, clique em **x** para o usuário/grupo na parte inferior da lista. 
    ![Usuários do Catálogo – excluir usuário](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > É recomendável que você adicione grupos de segurança para usuários do catálogo em vez de adicionar usuários diretamente e atribuir permissões. Em seguida, adicione usuários a grupos de segurança que correspondam às funções e ao acesso necessário ao catálogo desses usuários.

## <a name="special-considerations"></a>Considerações especiais

- As permissões atribuídas a grupos de segurança são aditivas. Digamos que um usuário esteja em dois grupos. Um grupo tem permissões para anotar e outro grupo não tem permissões para anotar. Nesse caso, o usuário tem permissões para anotar. 
- As permissões atribuídas explicitamente a um usuário substituem as permissões atribuídas a grupos aos quais o usuário pertence. No exemplo anterior, digamos, você adicionou explicitamente o usuário a usuários do catálogo e não atribuiu permissões para anotar. O usuário não pode anotar os ativos de dados mesmo que o usuário seja um membro de um grupo que tenha permissões para anotar.

## <a name="next-steps"></a>Próximas etapas
- [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)

