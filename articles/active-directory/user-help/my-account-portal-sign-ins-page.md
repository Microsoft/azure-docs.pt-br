---
title: Exiba e pesquise sua atividade de entrada recente na página minhas entradas-Azure Active Directory | Microsoft Docs
description: Detalhes sobre como exibir e pesquisar sua atividade de entrada recente na página Minhas Entradas do portal Minha Conta.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.openlocfilehash: 1062c8dfe416af2d39063bf71f39d52e151e28d3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096065"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>Exibir e pesquisar sua atividade de entrada recente na página minhas entradas

Você pode ver toda a atividade de entrada recente na sua conta corporativa ou de estudante, na página **Minhas Entradas** do portal **Minha Conta**. A análise do histórico de entradas ajuda a verificar atividades incomuns, já que ajuda a ver:

- Se alguém está tentando adivinhar sua senha.
- Se um invasor conseguiu entrar em sua conta e a partir de que localização.
- Quais aplicativos o invasor tentou acessar.

>[!Note]
> Se você vir um erro ao entrar com um conta Microsoft pessoal, ainda poderá entrar usando o nome de domínio da sua organização (como contoso.com) ou a **ID de locatário** da sua organização em uma das seguintes URLs:
>
>   - https://myaccount.microsoft.com?tenantId=*your_domain_name*
>   - https://myaccount.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-recent-sign-in-activity"></a>Exibir sua atividade de entrada recente

1. Entre em sua conta corporativa ou de estudante e vá para a página https://myaccount.microsoft.com/.

2. Selecione **minhas entradas** no painel de navegação esquerdo ou selecione o link **examinar atividade recente** no bloco **minhas** entradas.

    ![Página Minha Conta, mostrando em destaque os links de Atividade recente](media/my-account-portal/my-account-portal-sign-ins.png)

3. Expanda e examine cada um dos itens de entrada, verificando que reconhece cada uma delas. Se você encontrar um item de entrada que não parece familiar, altere sua senha para proteger a conta caso ela esteja comprometida.

    ![Página de atividades recentes, com detalhes de entrada expandidos](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se você vir uma entrada bem-sucedida

Às vezes, ao examinar sua própria atividade de entrada normal, você pode ver uma entrada bem-sucedida de um local, navegador ou sistema operacional desconhecido. Entradas desconhecidas podem significar que um invasor obteve acesso à sua conta. Se você vir a atividade que não foi autorizada, recomendamos que você altere imediatamente sua senha e, em seguida, vá para [informações de segurança](https://mysignins.microsoft.com/security-info) para atualizar suas configurações de segurança.

Antes de determinar que algo está errado, verifique se o que você está vendo não é um falso positivo (quando um item parece estranho, mas não há nada de errado). Por exemplo, determinamos o local e o mapa aproximados com base no seu endereço IP. As redes móveis são especialmente difíceis de localizar com precisão, já que, às vezes, elas roteiam o tráfego através de localizações distantes. Mesmo se você entrar usando seu dispositivo móvel no estado de Washington, o local poderá mostrar a entrada proveniente da Califórnia. É altamente recomendável que você verifique os detalhes além do local. Verifique se o sistema operacional, o navegador e o aplicativo também fazem sentido.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se você vir uma entrada malsucedida

Se você vir uma entrada malsucedida, isso pode significar que você digitou indigitadamente suas credenciais. Também pode significar que um invasor estava tentando adivinhar sua senha. Para responder a esse risco, você não precisa alterar sua senha, mas recomendamos que você se registre na MFA (autenticação multifator) do Azure AD. Com a autenticação multifator, mesmo que o hacker Adivinhe sua senha, ele não será suficiente para acessar a conta.

![Bloco de entrada sem êxito](media/my-account-portal-sign-ins-page/unsuccessful.png)

Se você vir uma entrada malsucedida, com uma observação sob a **atividade de sessão** que diz, `Additional verification failed, invalid code` isso significa que suas credenciais de autenticação primárias foram bem-sucedidas, mas a autenticação multifator falhou. Essa condição pode significar que um invasor adivinhou corretamente sua senha, mas não conseguiu passar no desafio da autenticação multifator. É recomendável que você ainda altere sua senha, pois o invasor pode já tê-la e vá para a página [informações de segurança](https://mysignins.microsoft.com/security-info) para atualizar suas configurações de segurança.

## <a name="search-for-specific-sign-in-activity"></a>Pesquisa de atividade de entrada específica

Você pode pesquisar sua atividade de entrada recente usando qualquer uma das informações disponíveis. Por exemplo, você pode pesquisar sua atividade de entrada recente por sistema operacional, localização, aplicativo, e assim por diante.

1. Na página **Analisar atividade recente**, digite as informações que quer pesquisar na barra **Pesquisa**. Por exemplo, digite `Unsuccessful` para pesquisar todas as atividades de entrada malsucedidas coletadas pelo aplicativo minha conta.

2. Selecione o botão **Pesquisar** para iniciar a pesquisa.

    ![Página Atividade Recente, mostrando a barra de pesquisa realçada, o botão de pesquisa e os resultados](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Confirmar atividade incomum

As entradas sinalizadas como atividades incomuns podem ser confirmadas no bloco para essa atividade na página **minhas** entradas.

![Bloco de entrada incomum para confirmar que você fez ou não tentou a entrada](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>Próximas etapas

Depois de ver sua atividade de entrada recente, você pode:

- Exibir ou gerenciar as [informações de segurança](./security-info-setup-signin.md).

- Visualizar ou gerenciar os [dispositivos](my-account-portal-devices-page.md) conectados.

- Exibir ou gerenciar as [organizações](my-account-portal-organizations-page.md).

- Exibir como a organização [usa os dados relacionados à privacidade](my-account-portal-privacy-page.md).

- Alterar as [configurações do portal Minha Conta](my-account-portal-settings.md)