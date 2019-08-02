---
title: Diretrizes de identidade visual para aplicativos | Microsoft Docs
description: Um guia abrangente para recursos para desenvolvedores do Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: arielgo
ms.custom: aaddev, signin_art
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92770acb19bf95d6a19507184bd8bea10806d5b7
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483024"
---
# <a name="branding-guidelines-for-applications"></a>Diretrizes de identidade visual para aplicativos

Ao desenvolver aplicativos com o Azure AD (Azure Active Directory), você precisará direcionar os clientes quando eles quiserem usar sua conta corporativa ou de estudante (gerenciada no Azure AD) ou a conta pessoal para inscrição e conexão no seu aplicativo.

Neste artigo, você vai:

- Saber sobre os dois tipos de contas de usuário gerenciadas pela Microsoft e como fazer referência às contas do Azure AD em seu aplicativo
- Descobrir o que você precisa fazer para adicionar o logotipo da Microsoft para uso em seu aplicativo
- Baixar as imagens oficiais de **Entrar** ou **Entrar com a conta da Microsoft** a serem usadas em seu aplicativo
- Saber sobre as regras de identidade visual e navegação

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Contas pessoais versus contas comerciais ou de estudante da Microsoft

A Microsoft gerencia dois tipos de contas de usuário:

- **Contas pessoais** (anteriormente conhecidas como Windows Live ID). Essas contas representam a relação entre usuários *individuais* e a Microsoft e são usadas para acessar serviços e dispositivos do cliente da Microsoft. Essas contas são destinadas a uso pessoal.
- **Contas comerciais ou de estudante.** Essas contas são gerenciadas pela Microsoft em nome de organizações que usam o Azure Active Directory. Essas contas são usadas para fazer logon no Office 365 e em outros serviços comerciais da Microsoft.

As contas comerciais ou de estudante da Microsoft  normalmente são atribuídas aos usuários finais (funcionários, estudantes e funcionários federais) por suas organizações (empresa, escola, órgão do governo). Essas contas são dominadas diretamente na nuvem (na plataforma do Azure AD) ou sincronizadas para o Azure AD de um diretório local, como o Active Directory do Windows Server. A Microsoft tem a *custódia* das contas comerciais ou de estudante, mas as contas são de propriedade e controladas pela organização.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Fazendo referência às contas do Azure AD em seu aplicativo

A Microsoft não expõe os usuários finais ao Azure ou os nomes de marca do Active Directory, e você também não deve fazê-lo.

- Depois que os usuários estiverem conectados, use o nome e o logotipo da organização o máximo possível. Isso é melhor do que usar termos genéricos como “sua organização.”
- Quando os usuários não estiverem conectados, refira-se às contas deles como "Contas corporativas ou de estudante" e use o logotipo da Microsoft para informar que a Microsoft gerencia essas contas. Não use termos como “conta comercial”, “conta empresarial” ou “conta corporativa”, que confundem o usuário.

## <a name="user-account-pictogram"></a>Imagem da conta de usuário

Em uma versão anterior dessas diretrizes, recomendamos usar uma imagem de "crachá azul". Com base nos comentários de usuários e de desenvolvedores, agora recomendamos o uso do logotipo da Microsoft em vez disso. O logotipo da Microsoft ajudará os usuários a entender que eles podem reutilizar a conta que usam com o Office 365 ou com outros serviços corporativos da Microsoft para fazer logon no seu aplicativo.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Inscrever-se e entrar no Azure AD

Seu aplicativo pode apresentar caminhos separados para inscrição e login, e as seções a seguir fornecem orientação visual para os dois cenários.

**Se o aplicativo der suporte à inscrição do usuário final (por exemplo, grátis para avaliação ou modelo freemium)** : Você pode mostrar um botão de **entrar** que permite aos usuários acessar seu aplicativo com sua conta corporativa ou conta pessoal. O Azure AD mostrará um prompt de consentimento da primeira vez que acessar seu aplicativo.

**Se o aplicativo exige permissões com as quais somente os administradores podem consentir ou se o aplicativo exige licenciamento organizacional**: Aquisição de administrador separada de entrada do usuário. O **botão "obter esse aplicativo"** redirecionará os administradores para entrar e pedir que eles concedam autorização em nome dos usuários em sua organização, o que tem o benefício adicional da supressão de solicitações de consentimento do usuário final para seu aplicativo.

## <a name="visual-guidance-for-app-acquisition"></a>Orientação visual para aquisição de aplicativo

O link "obtenha o aplicativo" deve redirecionar o usuário para a página de concessão de acesso (autorização) do Azure AD, para permitir que o administrador da organização autorize seu aplicativo para ter acesso aos dados de sua organização que são hospedados pela Microsoft. Os detalhes sobre como solicitar acesso são debatidos no artigo [Integração de aplicativos com o Active Directory do Azure](quickstart-v1-integrate-apps-with-azure-ad.md) .

Depois que os administradores derem consentimento ao seu aplicativo, eles podem optar por adicioná-lo à experiência do inicializador de aplicativos do Office 365 de seus usuários (acessível do waffle e de [https://portal.office.com/myapps](https://portal.office.com/myapps)). Se você deseja anunciar essa funcionalidade, você pode usar termos como "Adicione esse aplicativo para sua organização" e mostrar um botão como o exemplo a seguir:

![Botão que mostra o logotipo da Microsoft e o texto "Adicionar à minha organização"](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

No entanto, recomendamos que você escreva um texto explicativo em vez de depender de botões. Por exemplo:

> *Se você já usa o Office 365 ou outro serviço corporativo da Microsoft, pode conceder o acesso <nome_aplicativo> aos dados de sua organização. Isso permitirá que os usuários acessem o <nome_aplicativo> com suas contas de trabalho existentes.*

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

**NÃO** use “ID do Office 365” ou “ID do Azure.” Office 365 também é o nome de uma oferta ao consumidor da Microsoft que não usa o Azure AD para autenticação.

**NÃO** altere o logotipo da Microsoft.

**NÃO** exponha os usuários finais às marcas do Azure ou Active Directory. No entanto, é possível usar esses termos com desenvolvedores, profissionais de TI e administradores.

## <a name="navigation-dos-and-donts"></a>Regras de navegação

**Forneça** uma maneira de os usuários fazerem logout e trocarem para outra conta de usuário. Embora a maioria das pessoas tenha uma única conta pessoal da Microsoft/Facebook/Google/Twitter, as pessoas geralmente são associadas a mais de uma organização. O suporte a vários usuários conectados estará disponível em breve.
