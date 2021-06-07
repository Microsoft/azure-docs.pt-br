---
title: Inscrição por autoatendimento de Identidades Externas – Azure AD
description: Saiba como permitir que usuários externos se inscrevam em seus próprios aplicativos habilitando a inscrição por autoatendimento. Crie uma experiência de inscrição personalizada, personalizando o fluxo de usuários da inscrição por autoatendimento.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13023ef93cabcf46924cc2cc76dc2d868c4a1ddd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653551"
---
# <a name="self-service-sign-up"></a>Inscrição por autoatendimento

Quando compartilhar um aplicativo com usuários externos, é possível que você não saiba com antecedência quem precisará de acesso a um aplicativo. Como alternativa ao envio de convites diretamente para as pessoas, você pode permitir que usuários externos se inscrevam em aplicativos específicos, habilitando a inscrição por autoatendimento. Você pode criar uma experiência de inscrição personalizada, personalizando o fluxo de inscrição por autoatendimento do usuário. Por exemplo, você pode fornecer opções de inscrição com o Azure AD ou com provedores de identidade social e coletar informações sobre o usuário durante o processo de criação da conta.

> [!NOTE]
> Você pode associar os fluxos de usuários com aplicativos criados por sua organização. Os fluxos de usuários não podem ser usados para aplicativos da Microsoft, como SharePoint ou Teams.

## <a name="user-flow-for-self-service-sign-up"></a>Fluxo de usuários da inscrição por autoatendimento

Um fluxo de usuários da inscrição por autoatendimento cria uma experiência de inscrição para seus usuários externos por meio do aplicativo que você deseja compartilhar. O fluxo do usuário pode ser associado a um ou mais dos seus aplicativos. Primeiro, você habilitará a inscrição para autoatendimento para o locatário e fará a federação dos provedores de identidade que deseja permitir que usuários externos usem para iniciar sessão. Em seguida, você criará e personalizará o fluxo de usuários da inscrição e atribuirá seus aplicativos a ele.
Você pode definir configurações de fluxo de usuários para controlar como o usuário se inscreve no aplicativo:

- tipos de conta usados credenciais; por exemplo, contas de redes sociais, como as do Facebook, ou contas do Azure AD.
- Atributos a serem coletados durante a inscrição do usuário, como nome, CEP ou país/região de residência

Quando um usuário deseja entrar em seu aplicativo, seja ele da Web, móvel, da área de trabalho ou um SPA (aplicativo de página única), o aplicativo inicia uma solicitação de autorização para o ponto de extremidade fornecido pelo fluxo de usuários. O fluxo de usuários define e controla a experiência do usuário. Quando o usuário conclui um fluxo de usuário para inscrição, o Azure AD gera um token que redireciona o usuário para o aplicativo. Após a inscrição ser concluída, uma conta de convidado é provisionada para o usuário no diretório. Vários aplicativos podem usar o mesmo fluxo de usuários.

## <a name="example-of-self-service-sign-up"></a>Exemplo de inscrição por autoatendimento

O exemplo a seguir ilustra como estamos trazendo provedores de identidade social para o Azure AD com recursos de inscrição para autoatendimento para usuários convidados.  
Um parceiro do Woodgrove abre o aplicativo do Woodgrove. Ele decide que deseja se inscrever em uma conta de fornecedor. Ele seleciona Solicitar sua conta de fornecedor, o que inicia o fluxo de inscrição por autoatendimento.

![Exemplo de página inicial de inscrição por autoatendimento](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

Ele usa o email de sua escolha para se inscrever.

![Exemplo mostrando a seleção do Facebook como credenciais](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

O Azure AD cria uma relação com o Woodgrove usando a conta do Facebook do parceiro, e cria uma nova conta de convidado para o usuário após a inscrição.

O Woodgrove quer saber mais sobre o usuário, como nome, nome da empresa, CNPJ e número de telefone.

![Exemplo mostrando atributos de inscrição do usuário](media/self-service-sign-up-overview/example-enter-user-attributes.png)

O usuário insere as informações, continua o fluxo de inscrição e obtém acesso aos recursos de que precisa.

![Exemplo mostrando o usuário conectado](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Próximas etapas

 Para obter detalhes, confira como [adicionar inscrição por autoatendimento a um aplicativo](self-service-sign-up-user-flow.md).