---
title: Definir o controle de acesso de usuário global
description: Em grandes organizações, as permissões de usuário podem ser complexas e podem ser determinadas por uma estrutura organizacional global, além da estrutura de site e zona padrão.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/08/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 81ede3506337e76c84e20203ac27a3d441fdcef8
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522267"
---
# <a name="define-global-access-control"></a>Definir o controle de acesso global

Em grandes organizações, as permissões de usuário podem ser complexas e podem ser determinadas por uma estrutura organizacional global, além da estrutura de site e zona padrão.

Para dar suporte à demanda de permissões de acesso de usuário que são globais e mais complexas, você pode criar uma topologia de negócios global baseada em unidades de negócios, regiões e sites. Em seguida, você pode definir permissões de acesso de usuário em relação a essas entidades.

Trabalhar com as ferramentas de acesso para topologia de negócios ajuda as organizações a implementar estratégias de confiança zero, controlando melhor onde os usuários gerenciam e analisam dispositivos na plataforma Azure defender para IoT.

## <a name="about-access-groups"></a>Sobre grupos de acesso

O controle de acesso global é estabelecido por meio da criação de grupos de acesso de usuário. Os grupos de acesso consistem em regras sobre quais usuários podem acessar entidades comerciais específicas. Trabalhar com grupos permite que você controle o acesso de exibição e configuração ao defender para IoT para funções de usuário específicas em unidades de negócios, regiões e sites relevantes.

Por exemplo, permita que analistas de segurança de um grupo de Active Directory acessem todas as linhas de produção do automotivo e de vidro da Europa Ocidental, juntamente com uma linha de plástico em uma região.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Diagrama do grupo de Active Directory de analistas de segurança.":::

Antes de criar grupos de acesso, recomendamos que você:

- Configure cuidadosamente sua topologia de negócios. Para obter mais informações sobre topologia de negócios, consulte [trabalhar com exibições de mapa do site](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views).

- Planeje quais usuários estão associados aos grupos de acesso que você criar. Duas opções estão disponíveis para atribuir usuários a grupos de acesso:

  - **Atribuir grupos de grupos de Active Directory**: Verifique se você configurou uma instância de Active Directory para integrar com o console de gerenciamento local.
  
  - **Atribuir usuários locais**: Verifique se você criou usuários. Para obter mais informações, consulte [definir usuários](how-to-create-and-manage-users.md#define-users).

Os usuários administradores não podem ser atribuídos a grupos de acesso. Esses usuários têm acesso a todas as entidades de topologia de negócios por padrão.

## <a name="create-access-groups"></a>Criar grupos de acesso

Esta seção descreve como criar grupos de acesso. As regiões e as unidades de negócios globais padrão são criadas para o primeiro grupo que você criar. Você pode editar as entidades padrão ao definir seu primeiro grupo.

Para criar grupos:

1. Selecione **grupos de acesso** no menu lateral do console de gerenciamento local.

2. Selecione :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::. Na caixa de diálogo **Adicionar grupo de acesso** , insira um nome para o grupo de acesso. O console do oferece suporte a 64 caracteres. Atribua o nome de forma que o ajude a distinguir facilmente esse grupo de outros grupos.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="A caixa de diálogo Adicionar grupo de acesso onde você cria grupos de acesso.":::

3. Se a opção **atribuir um grupo de Active Directory** for exibida, você poderá atribuir um Active Directory grupo de usuários a esse grupo de acesso.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Atribua um grupo de Active Directory na caixa de diálogo Criar grupo de acesso.":::

   Se a opção não aparecer e você quiser incluir grupos de Active Directory nos grupos de acesso, selecione **configurações do sistema**. No painel **integrações** , defina os grupos. Insira um nome de grupo exatamente como ele aparece nas configurações de Active Directory e em minúsculas.

5. No painel **usuários** , atribua quantos usuários forem necessários para o grupo. Você também pode atribuir usuários a grupos diferentes. Se você trabalhar dessa forma, deverá criar e salvar o grupo de acesso e as regras e, em seguida, atribuir usuários ao grupo no painel **usuários** .

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Gerencie as funções de seus usuários e atribua-as conforme necessário.":::

6. Crie regras na caixa de diálogo **adicionar regras para o *nome*** com base nos requisitos de acesso da topologia de negócios. As opções que aparecem aqui são baseadas na topologia que você criou nas janelas de **exibição empresarial** e **Gerenciamento de site** . 

   Você pode criar mais de uma regra por grupo. Talvez seja necessário criar mais de uma regra por grupo quando você estiver trabalhando com granularidade de acesso complexo em vários sites. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Adicione uma regra ao seu sistema.":::

As regras que você cria aparecem na caixa de diálogo **Adicionar grupo de acesso** . Nele, você pode excluí-los ou editá-los.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Exiba e edite todos os seus grupos de acesso nessa janela.":::

### <a name="about-rules"></a>Sobre regras

Quando você estiver criando regras, esteja ciente das seguintes informações:

- Quando um grupo de acesso contém várias regras, a lógica de regra agrega todas as regras. Por exemplo, o uso de regras e lógica, não ou lógica.

- Para que uma regra seja aplicada com êxito, você deve atribuir sensores a zonas na janela de **Gerenciamento do site** .

- Você pode atribuir apenas um elemento por regra. Por exemplo, você pode atribuir uma unidade de negócios, uma região e um site para cada regra. Crie mais regras para o grupo se, por exemplo, você quiser que os usuários em um grupo de Active Directory tenham acesso a diferentes unidades de negócios em regiões diferentes.

- Se você alterar uma entidade e a alteração afetar a lógica da regra, a regra será excluída. Se as alterações feitas em uma entidade de topologia afetarem a lógica da regra, de modo que todas as regras sejam excluídas, o grupo de acesso permanecerá, mas os usuários não poderão entrar no console de gerenciamento local. Os usuários são notificados para entrar em contato com o administrador para entrar.

- Se nenhuma unidade de negócios ou região for selecionada, os usuários terão acesso a todas as regiões e unidades de negócios definidas.

## <a name="next-steps"></a>Próximas etapas

[Sobre os usuários do console do defender para IoT](how-to-create-and-manage-users.md)
