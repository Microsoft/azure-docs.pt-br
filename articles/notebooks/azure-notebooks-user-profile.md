---
title: Perfil e ID de usuário para uso com Azure Notebooks
description: Como criar e gerenciar seu perfil do usuário e a ID de usuário com Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: b8c21b908ca9162a7e44c7af1e222babc6ee1eb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631919"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Perfil e ID de usuário do Azure Notebooks

No espaço de colaboração avançado do Azure Notebooks, o perfil do usuário apresenta a imagem pública para outras pessoas:

[![Uma página de perfil do Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

A ID de usuário faz parte das URLs que você usa para compartilhar projetos e notebooks. A lista a seguir descreve os diferentes padrões de URL:

- `https://notebooks.azure.com/<user_id>`: Sua página de perfil.
- `https://notebooks.azure.com/<user_id>/projects`: Seus projetos. Você vê todos os projetos, mas os outros usuários veem apenas seus projetos públicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Aquivos de projeto.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Clones de um projeto específico.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: A visualização HTML de um arquivo ou um notebook específico.

## <a name="your-user-id"></a>Sua ID de usuário

Ao entrar no Azure Notebooks pela primeira vez, sua conta receberá automaticamente uma ID de usuário temporária como "anon-idr3ca". Enquanto você tiver uma ID de usuário que comece com "anon-", sempre que você entrar no Azure Notebooks a alteração da senha será solicitada:

![Solicitar a criação de uma ID de usuário ao fazer logon no Azure Notebooks](media/accounts/create-user-id.png)

Um comando **Configurar ID de usuário** também aparece ao lado do nome de usuário temporário:

![Comando Configurar ID de Usuário que é exibido quando você está usando uma ID temporária](media/accounts/configure-user-id-command.png)

Você também pode alterar a ID de usuário a qualquer momento na sua página de perfil.

Deve ser composta por uma ID de usuário entre quatro e dezesseis letras, números e hifens. Nenhum outro caractere é permitido, e a ID de usuário não poderá começar ou terminar com um hífen ou usar vários hífens em uma linha. Como as IDs de usuário são exclusivas em todas as contas de blocos de anotações do Azure, você poderá ver a mensagem, "ID de usuário já está em uso." (A mensagem também aparece se você tentar usar uma marca da Microsoft como uma ID de usuário.) Nesses casos, escolha uma ID de usuário diferente.

> [!Important]
> Alterar sua ID invalidará todas as URLs que você possa ter compartilhado usando a ID anterior. É possível alterar sua ID de volta para a ID anterior para revalidar os links. No entanto, é possível que outro usuário reivindique a ID não utilizada nesse meio tempo.

## <a name="your-profile"></a>Seu perfil

Seu perfil é composto de informações visíveis publicamente na URL, `https://notebooks.azure.com/<user_id>`. Sua página de perfil também mostra seus projetos usados recentemente e quaisquer projetos estrelados.

Para editar seu perfil, use o comando **Editar Informações de Perfil** na sua página de perfil. As seções do seu perfil são as seguintes:

| Seção | Conteúdo |
| --- | --- |
| Foto de perfil | Uma imagem que é mostrada na sua página de perfil. |
| Informações da conta | Seu nome de exibição, ID de usuário e conta de email pública. Aqui, a conta de email fornece aos outros usuários uma maneira de como contatá-lo e essa conta pode ser diferente da [conta](azure-notebooks-user-account.md) que você usa para entrar no Azure Notebooks. |
| Informações do perfil | Sua localização, empresa, cargo, site e uma breve descrição sobre você. |
| Perfis sociais | As IDs do Facebook, Twitter e GItHub, se você quiser compartilhá-las. |
| Configurações de privacidade | Fornece dois comandos:<ul><li>**Exportar Meu Perfil**: cria e baixa um arquivo *.zip* contendo todas as informações que o Azure Notebooks salva no seu perfil, incluindo fotos, informações de perfil e logs de segurança.</li><li>**Excluir Minha Conta**: Exclui permanentemente todas as suas informações pessoais armazenadas no Azure Notebooks.</li></ul> |
| Ativar Recursos do Site | Permite controlar aspectos do comportamento do Azure Notebooks:<ul><li>**Front-end unificado para notebooks**: permite inicialização mais rápida do notebook e melhor persistência.</li><li>**Executar em JupyterLab por padrão**: Por padrão, o Azure Notebooks oferece uma interface do usuário simples adequada para a maioria dos usuários. O JupyterLab fornece uma interface mais avançada, porém mais complexa para usuários experientes.</li><li>**Site VNext**: permite o layout da Web modernizado mostrado nesta documentação.</li></ul> |

## <a name="next-steps"></a>Próximas etapas  

> [!div class="nextstepaction"]
> [Tutorial: criar e executar um Jupyter Notebook para fazer uma regressão linear](tutorial-create-run-jupyter-notebook.md)
