---
title: Diretrizes de marca para aplicativos | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diretrizes de identidade visual do aplicativo para a plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2020
ms.author: ryanwi
ms.reviewer: arielgo, jiml
ms.custom: aaddev, signin_art
ms.openlocfilehash: 236e82ab97244e1428441f83295f6a5d4ed56350
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581985"
---
# <a name="branding-guidelines-for-applications"></a>Diretrizes de identidade visual para aplicativos

Ao desenvolver aplicativos com a plataforma de identidade da Microsoft, você precisará direcionar seus clientes quando quiserem usar sua conta corporativa ou de estudante (gerenciada no Azure AD) ou sua conta pessoal para inscrição e entrada em seu aplicativo.

Neste artigo, você irá:

- Saber sobre os dois tipos de contas de usuário gerenciadas pela Microsoft e como fazer referência às contas do Azure AD em seu aplicativo
- Conheça os requisitos para usar o logotipo da Microsoft em seu aplicativo
- Baixar as imagens oficiais de **Entrar** ou **Entrar com a conta da Microsoft** a serem usadas em seu aplicativo
- Saber sobre as regras de identidade visual e navegação

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Contas pessoais versus contas comerciais ou de estudante da Microsoft

A Microsoft gerencia dois tipos de contas de usuário:

- **Contas pessoais** (anteriormente conhecidas como Windows Live ID). Essas contas representam a relação entre usuários *individuais* e a Microsoft e são usadas para acessar serviços e dispositivos do cliente da Microsoft. Essas contas são destinadas a uso pessoal.
- **Contas corporativas ou de estudante.**  Essas contas são gerenciadas pela Microsoft em nome de organizações que usam o Azure Active Directory. Essas contas são usadas para entrar no Microsoft 365 e outros serviços comerciais da Microsoft.

As contas comerciais ou de estudante da Microsoft  normalmente são atribuídas aos usuários finais (funcionários, estudantes e funcionários federais) por suas organizações (empresa, escola, órgão do governo). Essas contas são modeladas diretamente na nuvem (na plataforma do Azure AD) ou sincronizadas com o Azure AD de um diretório local, como o Windows Server Active Directory. A Microsoft tem a *custódia* das contas comerciais ou de estudante, mas as contas são de propriedade e controladas pela organização.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Fazendo referência às contas do Azure AD em seu aplicativo

A Microsoft não expõe os usuários finais ao Azure ou os nomes de marca do Active Directory, e você também não deve fazê-lo.

- Depois que os usuários estiverem conectados, use o nome e o logotipo da organização o máximo possível. Isso é melhor do que usar termos genéricos como “sua organização.”
- Quando os usuários não estiverem conectados, refira-se às contas deles como "Contas corporativas ou de estudante" e use o logotipo da Microsoft para informar que a Microsoft gerencia essas contas. Não use termos como “conta comercial”, “conta empresarial” ou “conta corporativa”, que confundem o usuário.

## <a name="user-account-pictogram"></a>Imagem da conta de usuário

Em uma versão anterior dessas diretrizes, recomendamos usar uma imagem de "crachá azul". Com base nos comentários de usuários e de desenvolvedores, agora recomendamos o uso do logotipo da Microsoft em vez disso. O logotipo da Microsoft ajudará os usuários a entender que eles podem reutilizar a conta que usam com Microsoft 365 ou outros serviços comerciais da Microsoft para entrar em seu aplicativo.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Inscrever-se e entrar no Azure AD

Seu aplicativo pode apresentar caminhos separados para inscrição e login, e as seções a seguir fornecem orientação visual para os dois cenários.

**Se seu aplicativo suportar a inscrição do usuário final (por exemplo, avaliação gratuita ou modelo freemium)**: Você pode mostrar um botão **entrar** que permite aos usuários acessar seu aplicativo com sua conta corporativa ou sua conta pessoal. O Azure AD mostrará um prompt de consentimento da primeira vez que acessar seu aplicativo.

**Se seu aplicativo exigir permissões que somente os administradores podem conferir ou se o aplicativo exigir licenciamento organizacional**: separe a aquisição do administrador de logon do usuário. O **botão "obter esse aplicativo"** redirecionará os administradores para entrar e pedir que eles concedam autorização em nome dos usuários em sua organização, o que tem o benefício adicional da supressão de solicitações de consentimento do usuário final para seu aplicativo.

## <a name="visual-guidance-for-app-acquisition"></a>Orientação visual para aquisição de aplicativo

O link "obtenha o aplicativo" deve redirecionar o usuário para a página de concessão de acesso (autorização) do Azure AD, para permitir que o administrador da organização autorize seu aplicativo para ter acesso aos dados de sua organização que são hospedados pela Microsoft. Os detalhes sobre como solicitar acesso são debatidos no artigo [Integração de aplicativos com o Active Directory do Azure](./quickstart-register-app.md) .

Após o consentimento dos administradores para seu aplicativo, eles podem optar por adicioná-lo à experiência do iniciador do aplicativo Microsoft 365 do usuário (acessível a partir do waffle e de [https://portal.office.com/myapps](https://portal.office.com/myapps) ). Se você deseja anunciar essa funcionalidade, você pode usar termos como "Adicione esse aplicativo para sua organização" e mostrar um botão como o exemplo a seguir:

![Botão que mostra o logotipo da Microsoft e o texto "Adicionar à minha organização"](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

No entanto, recomendamos que você escreva um texto explicativo em vez de depender de botões. Por exemplo:

> *Se você já usa Microsoft 365 ou outro serviço comercial da Microsoft, é possível conceder <your_app_name> acesso aos dados da sua organização. Isso permitirá que os usuários acessem <your_app_name> com suas contas de trabalho existentes.*

Para baixar o logotipo oficial da Microsoft para uso em seu aplicativo, clique com o botão direito do mouse naquela que você deseja usar e salve-o em seu computador.

| Ativo                                | Formato PNG | Formato SVG |
| ------------------------------------ | ---------- | ---------- |
| Logotipo da Microsoft  | ![Logotipo da Microsoft para download no formato PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.png) | ![Logotipo da Microsoft para download no formato SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Orientação visual para logon

Seu aplicativo deve exibir um botão de logon que redireciona os usuários ao ponto de extremidade de logon, que corresponde ao protocolo que você usa para integrar com o Azure AD. A seção a seguir fornece detalhes sobre como deve ser esse botão.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Ícone e “Entrar com a Conta da Microsoft”

É a associação do logotipo da Microsoft e dos termos para “Entrar com a Conta da Microsoft” que representa exclusivamente o Azure AD entre outros provedores de identidade aos quais seu aplicativo pode oferecer suporte. Se você não tiver espaço suficiente para “Entrar com a Conta da Microsoft”, é possível reduzir para “Entrar.” Você pode usar um esquema de cores escuras ou claras para os botões.

O diagrama a seguir mostra as linhas vermelhas recomendadas da Microsoft ao usar os ativos com seu aplicativo. As linhas vermelhas se aplicam a “Entrar com a conta da Microsoft” ou à versão mais curta “Entrar”.

![Mostra as linhas vermelhas de "Entrar com a conta da Microsoft"](./media/howto-add-branding-in-azure-ad-apps/sign-in-with-microsoft-redlines.png)

Para baixar as imagens oficiais para uso em seu aplicativo, clique com o botão direito do mouse naquela que você deseja usar e salve-o em seu computador.

| Ativo                                | Formato PNG | Formato SVG |
| ------------------------------------ | ---------- | ---------- |
| Entrar com a Microsoft (tema escuro)  | ![PNG do tema escuro do botão "Entrar com a conta da Microsoft" para download](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.png) | ![SVG do tema escuro do botão "Entrar com a conta da Microsoft" para download](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.svg) |
| Entrar com a Microsoft (tema claro) | ![PNG do tema claro do botão "Entrar com a conta da Microsoft" para download](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.png) | ![SVG do tema claro do botão "Entrar com a conta da Microsoft" para download](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.svg) |
| Entrar (tema escuro)                 | ![PNG do tema escuro do botão curto "Entrar" para download](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.png) | ![SVG do tema escuro do botão curto "Entrar" para download](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.svg) |
| Entrar (tema claro)                | ![PNG do tema claro do botão curto "Entrar" para download](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.png) | ![SVG do tema claro do botão curto "Entrar" para download](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.svg) |

## <a name="branding-dos-and-donts"></a>Regras de identidade visual

**USE** “conta corporativa ou de estudante” em combinação com o botão "Entre com a Conta da Microsoft" para fornecer uma explicação adicional que ajude os usuários finais a reconhecer se podem usá-lo. **NÃO USE** outros termos, como "conta comercial", "conta empresarial" ou "conta corporativa".

**Não** use "id de Microsoft 365" ou "ID do Azure". Microsoft 365 também é o nome de uma oferta de consumidor da Microsoft, que não usa o Azure AD para autenticação.

**NÃO** altere o logotipo da Microsoft.

**NÃO** exponha os usuários finais às marcas do Azure ou Active Directory. No entanto, é possível usar esses termos com desenvolvedores, profissionais de TI e administradores.

## <a name="navigation-dos-and-donts"></a>Regras de navegação

**FORNEÇA** uma maneira para que os usuários saiam e alternem para outra conta de usuário. Embora a maioria das pessoas tenha uma única conta pessoal da Microsoft/Facebook/Google/Twitter, as pessoas geralmente são associadas a mais de uma organização. O suporte a vários usuários conectados estará disponível em breve.
