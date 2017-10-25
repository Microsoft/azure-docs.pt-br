---
title: "Guia para a criação de um Serviço de Dados para o Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre como criar, certificar e implantar um Serviço de Dados para compra no Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 96194198-6991-43b4-918e-ee337e250339
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: c0c9362f1c2e15c947aaaf7187f3383ad243140f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Guia de publicação do Serviço de Dados para o Azure Marketplace
> [!IMPORTANT]
> **Neste momento, não estamos mais realizando a integração de novos editores de Serviço de Dados. Novos serviços de dados não serão ser aprovados para listagem.** Se você tiver um aplicativo de negócios de SaaS que quer publicar no AppSource, encontre mais informações [aqui](https://appsource.microsoft.com/partners). Se você tiver aplicativos de IaaS ou serviços de desenvolvedor para publicar no Azure Marketplace, saiba mais [aqui](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Após concluir a etapa 1, [Criação e registro de conta](marketplace-publishing-accounts-creation-registration.md), orientamos você pelos [Requisitos gerais não técnicos](marketplace-publishing-pre-requisites.md) e [técnicos](marketplace-publishing-data-service-creation-prerequisites.md) de uma oferta de Serviço de Dados no Azure Marketplace. Agora, orientaremos você pelas etapas de criação de uma oferta de Serviço de Dados no [Portal de Publicação][link-pubportal] do Azure Marketplace.

## <a name="1----login-to-the-publishing-portal"></a>1.    Faça logon no Portal de Publicação.
Vá para [https://publish.windowsazure.com](https://publish.windowsazure.com.)

**Ao fazer logon pela primeira vez no Portal de Publicação, use a mesma conta utilizada para o registro de Perfil de Vendedor de sua empresa na Central de desenvolvedores.**  (Posteriormente, você poderá adicionar qualquer funcionário de sua empresa como um coadministrador no Portal de Publicação).

Clique no bloco **Publicar Serviços de Dados** se esse for o primeiro logon no portal de publicação.

## <a name="2----choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2.    Escolha **Serviços de Dados** no menu de navegação à esquerda.
  ![desenho](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3----create-a-new-data-service"></a>3.    Crie um novo Serviço de Dados
Preencha o título da nova Oferta de Serviço de Dados e clique em "+" à direita.

  ![desenho](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4----review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4.    Examine o submenu do Serviço de Dados recém-criado no menu de navegação.
Clique na guia **Passo a passo** e examine todas as etapas necessárias para publicar corretamente o Serviço de Dados no Azure Marketplace.

> [!TIP]
> Você sempre pode clicar nos links na página "Passo a passo" ou usar guias no submenu da oferta de Serviço de Dados no lado esquerdo.
> 
> 

## <a name="5----create-a-new-plan"></a>5.    Crie um novo Plano.
### <a name="offers-plans-transactions"></a>Ofertas, planos, transações.
Cada oferta pode ter vários planos, mas a quantidade mínima de planos é 1 (um). Quando os usuários finais se inscrevem em sua oferta, eles se inscrevem em um dos planos da oferta. Cada plano define como os usuários finais poderão usar o serviço.

Atualmente, o Azure Marketplace dá suporte somente ao modelo baseado em transações da assinatura mensal para Serviços de Dados, ou seja, os usuários finais pagarão uma taxa mensal de acordo com os preços dos planos específicos que assinaram e poderão a cada mês consumir a quantidade de transações definidas pelo plano.

Cada transação é normalmente definida como o número de registros que o Serviço de Dados retornará com base na consulta enviada ao serviço. O padrão é 100. O número de transações retornadas para cada consulta é o número de registros dividido por 100 e arredondado para cima até o inteiro mais próximo.

É responsabilidade da camada de serviço do Azure Marketplace monitorar (medir) o número de transações consumidas por cada consulta.

> [!IMPORTANT]
> Os usuários finais que atingirem o limite de transação durante o mês serão impedidos de continuar a usar o serviço até o final do ciclo de assinatura mensal.
> 
> O plano ou um dos planos pode (mas não é obrigatório) incluir um número ilimitado de transações.
> 
> 

### <a name="create-a-plan"></a>Crie um plano.
1. Clique em **"+"** ao lado de "Adicionar um novo plano".
2. Escolha uma das opções: uso **Ilimitado** ou **Limitado** para esse plano.  Se Limitado, forneça o número de transações mensais permitido pelo plano.
   
    ![desenho](media/marketplace-publishing-data-service-creation/step-5.1.png)  
   
    O Portal de Publicação também sugere um "Identificador de Plano", que é usado para se comunicar com os usuários finais, é usado como nome do plano na interface do usuário e também é usado pelo Serviço Marketplace para identificar o Plano. Se desejar, você pode alterar o "Identificador de Plano".
   
   > [!NOTE]
   > O "Identificador de Plano" deve ser exclusivo dentro do escopo de cada oferta. Como muitos outros identificadores usados no Portal de Publicação, ele será bloqueado após a primeira publicação para produção e você não poderá alterá-lo.
   > 
   > 
3. Clique para aceitar sua escolha.
4. Em seguida, você deve responder a algumas perguntas adicionais sobre seu plano recém-criado.
   
    ![desenho](media/marketplace-publishing-data-service-creation/step-5.2.png)

| Pergunta | Significado |
| --- | --- |
| **Este Plano é gratuito e está disponível em todo o mundo?** |Você pode criar um plano totalmente gratuito. Se é o único plano para essa oferta, significa que você está publicando uma "Oferta Gratuita" no Marketplace. Se é apenas para um plano (ou alguns), ele dá a opção de permitir que os usuários finais saibam mais sobre seu serviço com um número relativamente pequeno de transações por mês.  Se a resposta for "Sim", nenhuma outra pergunta será feita. |

> [!NOTE]
> Os usuários finais sempre poderão atualizar para os planos pagos.
> 
> 

| Pergunta | Significado |
| --- | --- |
| **Existe uma avaliação gratuita disponível?** |Você pode escolher entre "Sem avaliação" ou uma opção para usar seu plano por "Um mês". Os editores gostam de usar essa opção para permitir aos usuários finais compreender os benefícios da oferta gratuitamente por um mês. |

> [!IMPORTANT]
> Os usuários finais só poderão adquirir uma avaliação gratuita se inserirem um instrumento de pagamento, por exemplo, cartão de crédito, contrato empresarial.
> 
> Depois de um mês de avaliação gratuita, o Azure Marketplace começará a cobrar o preço dos clientes a partir da data da assinatura, a menos que o cliente tenha iniciado seu cancelamento. Nenhuma notificação especial será feita para os usuários finais.
> 
> 

| Pergunta | Significado |
| --- | --- |
| **O plano requer um código promocional de compra?** |Os editores têm a opção de limitar o acesso a seus planos de serviço fornecendo um código especial, chamado "Código promocional A", para clientes específicos. Somente os usuários finais com esse código promocional poderão assinar o plano. Se você escolher "Não", concorda que todos da região onde a oferta está disponível (confira o [Guia de conteúdo de marketing do Marketplace](marketplace-publishing-push-to-staging.md) para obter mais detalhes) poderão assinar esse plano. Não será feita nenhuma outra pergunta. |
| **Ocultar esse plano de qualquer pessoa que não tenha um código de promoção válido?** |Se a resposta à pergunta anterior for "Sim", o editor terá a opção de remover completamente a exibição desse plano da interface do usuário no Marketplace. Isso significa que os clientes não verão esse plano na página de detalhes da Oferta. Os usuários finais que receberem um código promocional para comprá-la poderão se inscrever na oferta usado o código  |

## <a name="6----create-your-marketplace-marketing-content"></a>6.    Criar conteúdo de marketing do Marketplace
Para saber como fornecer as informações necessárias nas guias **Marketing, Preço, Suporte e Categorias** , visite [Guia de conteúdo de marketing do Marketplace](marketplace-publishing-push-to-staging.md) , que é comum a todos os artefatos publicados no Azure Marketplace.  

## <a name="7----connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7.    Conecte a oferta ao seu Serviço (baseado no SQL Azure ou em serviço Web).
Clique no submenu **Serviços de Dados** .

Na metade superior da página, insira o **Namespace**da oferta.  

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.png)

A pergunta a seguir define como editor vai expor a oferta recém-criada no Azure Marketplace. (Para obter mais detalhes, confira o [Guia de pré-requisito técnico de Serviços de Dados](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Publicando o serviço baseado em Banco de Dados**

Clique em **Banco de Dados**. A página abaixo será exibida:

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.3.png)

Para criar um mapeamento CSDL para o conjunto de dados baseado no Banco de Dados SQL Azure:

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.4.png)

E para cada tabela

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.6.png)

Se for serviço Web

  ![desenho](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [!IMPORTANT]
> Leia [Mapeando um serviço Web existente para OData por meio de CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) para obter instruções detalhadas e exemplos de como criar um serviço Web CSDL.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Agora que você criou sua oferta de Serviço de Dados, não deixe de concluir as instruções no [Guia de conteúdo de marketing do Marketplace](marketplace-publishing-push-to-staging.md) antes de avançar para [Testando seu Serviço de Dados em preparação](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Consulte também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)
* Se estiver interessado em entender o processo e a finalidade geral do mapeamento de OData, leia este artigo [Mapeamento OData de Serviço de Dados](marketplace-publishing-data-service-creation-odata-mapping.md) para examinar as definições, as estruturas e as instruções.
* Se estiver interessado em aprender e em compreender os nós específicos e seus parâmetros, leia este artigo [Nós do mapeamento OData de Serviço de Dados](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para obter definições, explicações, exemplos e contexto de casos de uso.
* Se estiver interessado em examinar exemplos, leia este artigo [Exemplos de mapeamento OData de Serviço de Dados](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver um código de exemplo e compreender a sintaxe do código e o contexto.

[link-pubportal]:https://publish.windowsazure.com
