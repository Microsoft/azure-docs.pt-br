---
title: Noções básicas sobre experiências de consentimento de aplicativo do Azure AD | Microsoft Docs
description: Saiba mais sobre as experiências de consentimento do Microsoft Azure Active Directory para ver como você pode usá-lo ao gerenciar e desenvolver aplicativos no Microsoft Azure Active Directory
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac9d278053f05f9041a002b1c6ef3f873b0d9efb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324860"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Noções básicas sobre experiências de consentimento de aplicativo do Microsoft Azure Active Directory

Saiba mais sobre a experiência do usuário de consentimento do aplicativo do Microsoft Azure Active Directory. Portanto, você pode inteligentemente gerenciar aplicativos para sua organização e/ou desenvolver aplicativos com uma experiência de consentimento mais transparente.

## <a name="consent-and-permissions"></a>Consentimentos e permissões

Consentimento é o processo de um usuário conceder autorização a um aplicativo para acessar recursos protegidos em seu nome. Um administrador ou usuário pode ser solicitado consentimento para permitir o acesso aos seus dados da empresa/individuais.

A experiência do usuário real de dar consentimento varia de acordo com as políticas definidas no locatário do usuário, o escopo do usuário da autoridade (ou função) e o tipo de [permissões](https://docs.microsoft.com/azure/active-directory/develop/active-directory-permissions) que está sendo solicitado pelo aplicativo cliente. Isso significa que os desenvolvedores de aplicativos e administradores de locatários têm algum controle sobre a experiência de consentimento. Os administradores têm a flexibilidade de configuração e desabilitar políticas em um locatário ou o aplicativo para controlar a experiência de consentimento em seu locatário. Os desenvolvedores de aplicativos podem determinam quais tipos de permissões estão sendo solicitadas e se desejam orientar os usuários por meio do fluxo de consentimento do usuário ou o fluxo de consentimento do administrador.

- **Fluxo de consentimento do usuário** é quando um desenvolvedor de aplicativo direciona os usuários para o ponto de extremidade de autorização com a intenção de autorização de registro para apenas o usuário atual.
- **Fluxo de consentimento do administrador** é quando um desenvolvedor de aplicativo direciona os usuários para o ponto de extremidade de autorização com a intenção registrar o consentimento para o locatário inteiro. Para garantir que o fluxo de consentimento do administrador funcione corretamente, os desenvolvedores de aplicativos devem listar todas as permissões na `RequiredResourceAccess` propriedade no manifesto do aplicativo. Para obter mais informações, veja [Manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Blocos de construção do solicitação de consentimento

A solicitação de consentimento foi projetada para garantir que os usuários tenham informações suficientes para determinar se confiam que o aplicativo cliente para acessar recursos protegidos em seu nome. O reconhecimento sobre os blocos de construção ajudará os usuários, concedendo consentimento tomar decisões mais fundamentadas e ajudará os desenvolvedores a criar melhores experiências de usuário.

O diagrama e a tabela a seguir fornecem informações sobre os blocos de construção da solicitação de consentimento.

![Blocos de construção do solicitação de consentimento](./media/application-consent-experience/consent_prompt.png)

| # | Componente | Finalidade |
| ----- | ----- | ----- |
| 1 | Identificador de usuário | Esse identificador representa o que o aplicativo cliente está solicitando para acessar recursos protegidos em nome de usuário. |
| 2 | Título | As alterações de título baseadas em se os usuários vão percorrer o fluxo de consentimento do usuário ou administrador. No fluxo de consentimento do usuário, o título será "Permissões solicitadas" enquanto o fluxo de consentimento do administrador o título terá uma linha adicional "Aceitar para sua organização". |
| 3 | Logotipo do aplicativo | Essa imagem deve ajudar os usuários a terem uma indicação visual se este aplicativo for o aplicativo que se destinam a acessar. Essa imagem é fornecida por desenvolvedores de aplicativos e a propriedade dessa imagem não é validada. |
| 4 | Nome do aplicativo | Esse valor deve informar qual aplicativo está solicitando acesso aos dados de usuários. Observe que esse nome é fornecido pelos desenvolvedores e a propriedade desse nome de aplicativo não é validada. |
| 5 | Domínio do Editor | Esse valor deve fornecer aos usuários com um domínio, que eles podem até conseguir avaliar a confiabilidade. Este domínio é fornecido pelos desenvolvedores e a propriedade desse domínio de publicador é validada. |
| 6 | Permissões | Esta lista contém as permissões que estão sendo solicitadas pelo aplicativo cliente. Os usuários sempre devem avaliar os tipos de permissões que estão sendo solicitadas para entender quais dados o aplicativo cliente será autorizado a acessar em seu nome, se eles aceitarem. Como desenvolvedor de aplicativos é melhor para solicitar acesso para as permissões com o privilégio mínimo. |
| 7 | Descrição da permissão | Esse valor é fornecido pelo serviço expondo as permissões. Para ver as descrições de permissão, você deve ativar a divisa ao lado de permissão. |
| 8 | Termos do aplicativo | Esses termos contêm links para os termos de serviço e a privacidade do aplicativo. O publicador é responsável por suas regras de estrutura de tópicos em termos de serviço. Além disso, o publicador é responsável por divulgar a maneira de usar e compartilhar dados de usuário em sua declaração de privacidade. Se o publicador não fornecer links para esses valores para aplicativos multilocatário, haverá um aviso em negrito na solicitação de consentimento. |
| 9 | https://myapps.microsoft.com | Este é o link em que os usuários podem examinar e remover todos os aplicativos que não são da Microsoft que atualmente possuem acesso aos seus dados. |

## <a name="common-consent-scenarios"></a>Cenários comuns de consentimento

Aqui estão as experiências de consentimento que um usuário pode ver em cenários comuns de consentimento:

1. Fluxo de autorização de indivíduos que acessam um aplicativo que os direcionarás para o usuário, exigindo um conjunto de permissões no seu escopo de autoridade.
    
    1. Os administradores verão um controle adicional sobre a solicitação de consentimento tradicional que lhes permita consentimento em nome do locatário inteiro. O controle será padronizado como desligado, isso somente quando os administradores verificarem explicitamente a caixa será concedido em nome do locatário inteiro. A partir de hoje, essa caixa de seleção mostrará apenas para a função de Administrador Global, para que o administrador de nuvem e aplicativo Admin não vejam essa caixa de seleção.

        ![Solicitação de consentimento para o cenário 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Os usuários verão a solicitação de consentimento tradicional.

        ![Solicitação de consentimento para o cenário 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Indivíduos acessando um aplicativo que exige pelo menos uma permissão que está fora de seu escopo de autoridade de indivíduos.
    1. Os administradores verão a mesma solicitação como 1.i mostrada acima.
    2. Os usuários serão impedidos de conceder autorização para o aplicativo e serão avisados peça seu administrador de acesso ao aplicativo. 
                
        ![Solicitação de consentimento para o cenário 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Fluxo de autorização de indivíduos que navegam ou são direcionados para o administrador.
    1. Os usuários do administrador verão a solicitação de consentimento tradicional. O título e as descrições de permissão alteradas na solicitação, o realce de alterações, o fato de que aceitar este solicitação concederá o aplicativo de acesso para os dados solicitados em nome do locatário inteiro.
        
        ![Solicitação de consentimento para o cenário 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Usuários não administradores verão a mesma tela 2.ii exibida acima.

## <a name="next-steps"></a>Próximas etapas
- Tenha uma visão geral passo a passo de [como a estrutura de consentimento do Azure AD implementa o consentimento](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Para se aprofundar, veja [como um aplicativo multilocatário pode usar a estrutura de consentimento](active-directory-devhowto-multi-tenant-overview.md) para implementar consentimento de "usuário" e "administrador", dando suporte a padrões mais avançados de aplicativos de várias camadas.
- Saiba [como configurar o domínio do Publicador do aplicativo](howto-configure-publisher-domain.md).
