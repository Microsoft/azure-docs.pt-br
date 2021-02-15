---
title: Migrar contas do Portal do Microsoft Cloud Partner para o marketplace comercial da Microsoft
description: Saiba como migrar sua conta do Portal do Cloud Partner para o Partner Center no marketplace comercial da Microsoft para o Azure
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 09/23/2019
ms.openlocfilehash: 9e9f3b7ea3d99de51b9398bda82b690d2c249a1d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520958"
---
# <a name="how-to-migrate-your-account-from-cloud-partner-portal-to-partner-center"></a>Como migrar sua conta do Portal do Cloud Partner para o Partner Center

Se você tiver uma conta do Portal do Cloud Partner (CPP) existente, suas configurações de conta precisarão ser migradas para o Partner Center.

## <a name="account-migration-process"></a>Processo de migração de conta

Se você for um usuário com a função de proprietário no CPP para uma determinada conta, uma faixa amarela será mostrada na página de perfil do editor. Você poderá pertencer a um dos dois casos a seguir:

- Sua conta já foi migrada e você já pode gerenciar as Configurações de Conta no Partner Center.
- Sua conta não foi migrada para o Partner Center e você precisa realizar uma ação adicional.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Sua conta foi migrada para o Partner Center

Agora você gerencia sua conta no Partner Center. Alterações como adição/exclusão de usuário serão sincronizadas de volta para o CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Você ainda não migrou sua conta para o Partner Center

Clique na faixa para iniciar o processo de migração de sua conta. Você deve inserir os seguintes itens:

1. Endereço de email de trabalho: <br> <br> Na maioria dos casos, entre com o endereço de email que você usa para entrar no CPP. Em determinados casos, um endereço de email diferente deve ser usado:

    * Conta Microsoft: Se a conta do CPP for uma conta da Microsoft, insira um email de trabalho válido associado ao locatário para o qual a ID do Microsoft Partner Network (MPN) está registrada. Para obter mais informações, consulte [Inscrever-se no programa Microsoft Partner Network](#sign-up-for-microsoft-partner-network-program).

    * Incompatibilidade de locatário: Se seu endereço de email de trabalho não pertencer ao locatário associado à ID do Microsoft Partner Network presente na sua conta do CPP, você verá um erro. Para ultrapassar esse erro, insira um endereço de email associado ao locatário. Uma mensagem de erro fornecerá o nome do locatário.

2. Inscreva-se no programa Microsoft Partner Network

    Se sua conta do CPP não tiver uma ID do Microsoft Partner Network ou tiver uma que seja inválida, você precisará se inscrever no programa Microsoft Partner Network como parte do processo de ativação.

## <a name="publishers-moving-from-cpp"></a>Editores saindo do CPP

Se sua conta foi migrada do CPP, você não precisa criar uma nova conta do Partner Center. Você deve ter recebido um link personalizado para sua nova conta do Partner Center por email e em uma notificação por faixa depois de fazer logon na sua conta de CPP existente.

Depois de habilitar sua nova conta do Partner Center visitando este link personalizado, você pode retornar à sua conta visitando o [painel do marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) no Partner Center.

As informações do contrato de publicação e do perfil da empresa serão migradas para sua nova conta do Partner Center, juntamente com as informações do perfil de pagamento da conta previamente configuradas, contas de usuário e permissões e ofertas ativas associadas à sua conta do CPP.

Depois que as informações da conta forem migradas do CPP para o Partner Center, você não usará mais o CPP para fazer atualizações na conta ou gerenciar usuários, permissões e cobrança. Por um período limitado, as atualizações de conta feitas no Partner Center serão automaticamente atualizadas em sua conta do CPP somente leitura até que o portal do CPP seja eventualmente preterido.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Inscreva-se no programa Microsoft Partner Network

As empresas que desejam fazer parceria com a Microsoft devem ingressar no Microsoft Partner Network (MPN) e obter uma ID do MPN. Se você já é membro do Microsoft Partner Network e tem uma ID do MPN, mantenha as informações à mão, pois você precisará delas durante o processo de ativação da conta.  

Se você não for membro do Microsoft Partner Network, poderá [ingressar aqui](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) para obter uma ID do MPN. Anote sua ID do MPN, pois você precisará inseri-la durante o processo de ativação da conta.

Para saber mais sobre o Microsoft Partner Network, consulte [Ingressar no Microsoft Partner Network](https://partner.microsoft.com/membership) no site do parceiro. Para saber mais sobre os benefícios do ISV no Microsoft Partner Network, consulte o [Hub de Recursos do ISV](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Migrar as ofertas do Dynamics 365 e do PowerApps para o Partner Center

Para simplificar a conta e oferecer gerenciamento para o Dynamics 365 Customer Engagement, o PowerApps e o Dynamics 365 Operations, as ofertas foram migradas para o [Partner Center](https://partner.microsoft.com/). A migração garante que o mesmo conteúdo esteja disponível para catálogos públicos e do vendedor.

Para obter informações específicas sobre o que precisava ser feito até **15 de outubro de 2019** para suas ofertas do Dynamics 365 Customer Engagement, PowerApps, e Dynamics 365 Operations, siga as instruções abaixo.

> [!NOTE]
> Isso não se aplica às ofertas do Dynamics 365 Business Central.  

1. Se sua conta de associação do MPN foi originalmente criada no Partner Membership Center (PMC), entre no [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) para confirmar que sua conta foi migrada. Se você vir uma tela de perfil com sua ID do MPN, você já pode continuar. Caso contrário, você precisará iniciar a migração da sua conta seguindo os prompts no [Partner Membership Center](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Se precisar de ajuda, visite a página de [suporte](https://partner.microsoft.com/support?issueid=100-0077).
2. Acesse a página de visão geral do [ marketplace comercial no Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Se você vir “marketplace comercial” no painel de navegação à esquerda, você estará registrado e deverá continuar para a próxima etapa. Caso contrário, [inscreva-se no marketplace comercial](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) agora.
3. Confirme se suas ofertas estão no AppSource [pesquisando suas ofertas](https://appsource.microsoft.com/). Se suas ofertas já estiverem no AppSource, vá para a próxima etapa. Para qualquer oferta que não esteja no AppSource, crie uma [nova oferta do Dynamics 365 Customer Engagement](create-new-customer-engagement-offer.md) ou uma [nova oferta do Dynamics 365 Operations](create-new-operations-offer.md).
4. Na página de [Contratos do Partner Center](https://partner.microsoft.com/dashboard/account/agreements), verifique se você examinou e aceitou o **Adendo do ISV do Business Applications**.
5. Nas [configurações de conta](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)do Partner Center, verifique se suas informações de cobrança estão completas.
6. Envie todas as ofertas novas e existentes para certificação e publicação, mesmo que suas ofertas tenham sido certificadas anteriormente.
    * Complete as telas de informações, inclusive, forneça seu aplicativo para certificação, bem como informações de marketing. Selecione **Enviar** (canto superior direito da tela) até **15 de outubro de 2019**. Essas etapas devem ser concluídas para evitar impactar a disponibilidade da oferta.
    * Se qualificado, você pode solicitar a participação na camada premium durante esse processo.
    * A certificação ou a recertificação exige que seu aplicativo dê suporte à versão mais recente de nossa plataforma do Business Applications.
    * Depois que seu aplicativo tiver sido aprovado, você receberá um email para retornar à oferta e selecionar “entrar no ar” para permitir que a oferta fique ativa no Microsoft AppSource.

## <a name="additional-resources"></a>Recursos adicionais

Obtenha ajuda de especialistas e colegas em fóruns e descubra blogs, webinars, vídeos, eventos e muito mais no [Microsoft Dynamics CRM](https://community.dynamics.com/crm?wa=wsignin1.0).

Se você precisar de ajuda para publicar, certificar ou gerenciar suas ofertas do marketplace, [envie um tíquete de suporte](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-step"></a>Próxima etapa

- [Gerenciar sua conta do marketplace comercial no Partner Center](./manage-account.md)
