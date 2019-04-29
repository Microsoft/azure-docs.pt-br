---
title: Entre no Azure Notebooks
description: Configure sua conta de usuário para blocos de anotações do Azure usando uma conta da Microsoft ou uma conta de trabalho/estudante.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0d657fcc-26bc-41dd-abf0-3e5cfd66e0e0
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: 311f4d612c9deab23e7537b0c53ff3932c312cd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631988"
---
# <a name="your-user-account-for-azure-notebooks"></a>Sua conta de usuário para o Microsoft Azure Notebooks

Você pode usar o Azure Notebooks com ou sem entrar com uma conta de usuário:

- Sem precisar entrar, você pode criar e executar blocos de anotações, mas não é possível manter blocos de anotações ou arquivos de dados como parte dos projetos. Os usuários que recebem um link para um Azure Notebook, por exemplo, podem aproveitar o bloco de anotações sem a necessidade de entrar.
- Quando conectado, o Azure Notebooks retém todos os seus projetos com sua conta. Usuários conectados também tem uma ID de usuário que permite compartilhar seus projetos e notebooks com outras pessoas.
  - Quando a conta usada para Microsoft Azure Notebooks também está associada uma assinatura do Azure, você obtém benefícios adicionais como a execução de blocos de anotações em servidores mais poderosos, criação de blocos de anotações privados e conceder permissões a blocos de anotações para usuários individuais.

Entrar no Azure Notebooks requer uma Conta da Microsoft ou uma conta "Corporativa ou de estudante". Você será solicitado para sua conta ao selecionar o **Entrar** no canto superior direito da página Notebooks:

![Entrar no comando para blocos de Azure Notebooks](media/accounts/sign-in-command.png)

Todo o trabalho que você pode fazer no Azure Notebooks está associado à sua conta usada para entrar. Cada conta também deve ter uma ID de usuário exclusiva em seu [perfil de usuário](azure-notebooks-user-profile.md). Como resultado, você pode entrar no Azure Notebooks com contas diferentes se você precisa manter conjuntos separados de projetos e identidades separadas. Por exemplo, todos os membros de uma equipe de ciência de dados podem ter ambas as contas individuais, juntamente com como uma conta de grupo compartilhada que eles usam para apresentar seu trabalho para as pessoas fora de sua empresa. Instrutores, da mesma forma, podem manter uma conta para sua função de ensino que é diferente de uma conta usada na consultoria externos ou de trabalho de código-fonte aberto.

## <a name="microsoft-accounts"></a>Contas da Microsoft

As contas da Microsoft são usadas para entrar em qualquer número de produtos da Microsoft e serviços, como o Windows Azure, outlook.com, OneDrive e XBox Live. Se você usar qualquer um desses serviços, é provável que você já tem uma Conta da Microsoft que você pode usar com o Azure Notebooks.

Se você não tiver certeza, selecione o comando **Criar** no prompt de comando. Você pode criar uma nova conta da Microsoft usando qualquer endereço de email de qualquer provedor.

![Comando para criar uma nova conta Microsoft](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Se você tentar criar uma nova conta com um endereço de email que já tenha uma conta associada a ele, você poderá ver a mensagem "você não pode se inscrever aqui com um trabalho ou endereço de email da escola. Use um email pessoal, como Gmail ou Yahoo!, ou obtenha um novo email do Outlook". Nesse caso, tente entrar com o endereço de email de trabalho sem criar uma nova conta.

Para contas de criança, o acesso ao Azure Notebooks é bloqueado por padrão. Entrar com uma conta de criança exibe o erro mostrado abaixo:

![Erro ao tentar entrar com uma conta de criança: algo deu errado, seu responsável bloqueou o acesso](media/accounts/child-account-error.png)

Para habilitar o acessos, os pais devem fazer as seguintes etapas:

1. Visitar `https://account.live.com/mk` e entrar com uma conta primária.
1. Na seção para o filho em questão, selecione **Gerenciar o acesso desta criança para aplicativos de terceiros**.
1. Na próxima página, selecione **Habilitar o acesso**.
1. Quando, em seguida, a conta de criança é usada para fazer logon no Azure Notebooks, selecione **Sim** no prompt de permissões que é exibido.

> [!Warning]
> Habilitar o acesso a aplicativos de terceiros para o Azure Notebooks também permite o acesso para todos os outros aplicativos de terceiros. Os pais são aconselhados a usar o critério ao habilitar o acesso e pode ser útil monitorar a atividade do filho mais de perto.

## <a name="work-or-school-accounts"></a>Contas corporativas ou de estudante

Uma conta corporativa ou de estudante é criada por um administrador da organização para permitir que um membro da organização acesse os serviços de nuvem da Microsoft como o Office 365 e também como uma conta para entrar no Windows em um computador ingressado no domínio. Uma conta corporativa ou de estudante normalmente usa um endereço de email organizacional, tais como any-user@contoso.com.

Entrar no Azure Notebooks com uma conta corporativa ou de estudante pode exigir o consentimento do administrador porque os Azure Notebooks coleta ou usa (mas não revela) informações como o endereço de email da conta e informações sobre o navegador do usuário. (os dados do navegador são usados para otimizar os recursos de acordo com o uso popular).

O administrador de uma conta organizacional deve fornecer o consentimento em nome dos usuários, se os usuários são impedidos de consentimento individualmente. Nesse caso, os usuários veem a mensagem "Você não pode acessar este aplicativo":

![Mensagem "Você não pode acessar este aplicativo" ao usar uma conta corporativa ou de estudante](media/accounts/consent-permissions-denied.png)

Para fornecer consentimento como um administrador, use a [página de consentimento do administrador](https://notebooks.azure.com/account/adminConsent), que orienta você pelo processo.

## <a name="next-steps"></a>Próximas etapas  

> [!div class="nextstepaction"]
> [Editar seu perfil e ID de usuário](azure-notebooks-user-profile.md)
