---
title: Como criar uma conta do Marketplace comercial no Partner Center
description: Saiba como criar uma conta do Marketplace comercial no Partner Center.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 07/05/2019
ms.openlocfilehash: 24ed03cde967f0fef7abdc4c2df1f8e5d6bdb6ab
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327130"
---
# <a name="create-a-commercial-marketplace-account-in-partner-center"></a>Criar uma conta do Marketplace comercial no Partner Center

Para publicar suas ofertas no [Azure Marketplace](https://azuremarketplace.microsoft.com/) ou no [AppSource](https://appsource.microsoft.com/), você precisará criar uma conta no programa do Marketplace comercial no Partner Center.

## <a name="create-a-partner-center-account"></a>Criar uma conta do Partner Center

Neste artigo, abordaremos como criar uma conta do Partner Center, incluindo como: 

- [Registrar-se usando a página de registro do Partner Center](#to-create-a-commercial-marketplace-account-in-partner-center)
- [Entrar com uma conta corporativa](#sign-in-with-a-work-account)
- [Concordar com os termos e condições](#agree-to-terms-and-conditions) 
- [Forneça seu perfil do Publicador](#provide-your-publisher-profile)

>[!Important]
>Se você tiver uma conta no [portal do Cloud Partner (CPP)](https://cloudpartner.azure.com) que foi movida para o Partner Center, não será necessário criar uma nova conta. Consulte [Publicadores mudando de CPP](#publishers-moving-from-cpp) para obter mais informações. 

### <a name="before-you-begin"></a>Antes de começar

Para criar uma conta no Partner Center, verifique se você tem:

- Autoridade para assinar contratos legais em nome de sua empresa.
- O nome de negócios legal, endereço e contato principal da sua empresa (isso pode ser você).

Verificaremos essas informações durante o processo de criação da conta.

### <a name="to-create-a-commercial-marketplace-account-in-partner-center"></a>Para criar uma conta do Marketplace comercial no Partner Center

Examine as informações na página [**Bem-vindo ao registro do Microsoft Partner Center**](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) e registre-se para obter uma conta.

#### <a name="sign-in-with-a-work-account"></a>Entrar com uma conta corporativa

Para que você possa vincular o domínio da conta de email de trabalho da sua empresa à sua nova conta do Partner Center. Ao associar essas contas, os funcionários da empresa podem entrar no Partner Center com seus nomes de usuário e senhas de conta corporativa.

>[!Note]
>Para verificar se sua empresa já tem uma conta corporativa, como criar uma nova conta corporativa ou como configurar várias contas corporativas para usar com o Partner Center, visite [a conta corporativa corporativa e o Partner Center](./company-work-accounts.md). 

#### <a name="agree-to-terms-and-conditions"></a>Concordar com os termos e condições

Você precisará concordar com dois conjuntos de termos e condições, incluindo o [contrato de assinatura do Microsoft Online](https://go.microsoft.com/fwlink/?LinkId=870457)e o contrato de [Microsoft Marketplace editor](https://go.microsoft.com/fwlink/?linkid=843476).

#### <a name="provide-your-publisher-profile"></a>Forneça seu perfil do Publicador

Seu perfil de editor inclui o nome da sua empresa e a ID do MPN. Se você ainda não tiver feito isso, será necessário ingressar no [Microsoft Partner Network](https://partner.microsoft.com/commercial). Depois de ingressar no Microsoft Partner Network, você será fornecido com uma ID MPN. 

Crie uma ID de editor. Sua ID de editor identifica exclusivamente sua empresa e suas ofertas no Marketplace e no AppSource. 

Depois de confirmar as informações de perfil do editor, concorde com os termos e condições e crie sua conta do Partner Center selecionando **aceitar e continuar**. 

>[!Important]
>*Você deve estar autorizado a agir em nome da sua empresa para aceitar esses termos.*

Obrigado por criar uma conta no Partner Center! Agora você será levado para a página [visão geral do Marketplace comercial](./commercial-marketplace-overview.md) .

### <a name="publishers-moving-from-cpp"></a>Publicadores mudando de CPP

Se sua conta tiver sido migrada do [portal do Cloud Partner (CPP)](https://cloudpartner.azure.com), você não precisará criar uma nova conta do Partner Center, mas terá recebido um link personalizado para sua nova conta do Partner Center por email e, em uma notificação de banner exibida após fazendo logon na sua conta CPP existente.

Depois de habilitar sua nova conta do Partner Center visitando este link personalizado, você pode retornar à sua conta visitando o painel do [Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/) no Partner Center.

As informações do contrato de publicação e do perfil da empresa serão migradas para sua nova conta do Partner Center, juntamente com as informações de perfil de pagamento da conta previamente configuradas, contas de usuário e permissões e ofertas ativas associadas à sua conta de CPP. 

Depois que as informações da conta forem movidas do CPP para o Partner Center, você não usará mais o CPP para fazer atualizações de conta ou gerenciar usuários, permissões e cobrança. Por um período limitado, as atualizações de conta feitas no Partner Center serão automaticamente atualizadas em sua conta de CPP somente leitura até que o portal de CPP seja eventualmente preterido.

## <a name="add-new-publishers-to-the-commercial-marketplace-program"></a>Adicionar novos Publicadores ao programa comercial Marketplace

Uma organização pode ter vários Publicadores associados a uma conta comercial do Marketplace. Um usuário existente pode adicionar mais Publicadores depois de fazer logon no Partner Center, selecionando **configurações** -> de conta**Publicadores** -> **Adicionar Publicador**.

>[!Note]
>Antes de adicionar um novo Publicador, você pode examinar seus Publicadores existentes fazendo logon no Partner Center e selecionando **configurações** -> de conta**editores** para ver uma lista de Publicadores existentes.

Outro usuário do mesmo Azure Active Directory locatário pode adicionar um novo Publicador seguindo as etapas abaixo:

1. Iniciar o fluxo de inscrição no [Microsoft Partner Center](https://partner.microsoft.com/en-us/dashboard/account/v3/enrollment/introduction/azureisv).
1. Selecione **entrar com uma conta de trabalho** e insira seu endereço de email de trabalho.
1. Selecione o botão **Adicionar Publicador** .
1. Escolha a ID de MPN que você deseja associar ao Publicador.
1. Atualize os **detalhes** do Publicador no formulário. <br>

   1. **Nome do editor**: O nome que será exibido no Azure Marketplace ou AppSource com a oferta. <br>
   1. **PublisherID**: Um identificador usado pelo Partner Center para identificar exclusivamente o seu editor. O padrão para esse campo é mapeado para um existente e `PublisherID` exclusivo no sistema, que não pode ser reutilizado e, portanto, esse campo precisa ser atualizado. <br>
   1. **Informações de contato**: Atualize as informações de contato quando necessário.

1. Depois de concluir o processo, você pode gerenciar seu Publicador recém-criado acessando o programa do **Marketplace comercial** listado no menu de navegação à esquerda. Se você não vir o programa **comercial do Marketplace** , atualize a página.  O novo editor aparecerá na lista **editores** .

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar sua conta do Marketplace comercial no Partner Center](./manage-account.md) 
