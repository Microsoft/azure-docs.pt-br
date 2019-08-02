---
title: Perguntas frequentes sobre bancos de dados MySql do ClearDB com o Serviço de Aplicativo do Azure
description: Respostas a perguntas comuns sobre o uso de bancos de dados MySQL ClearDB com o Serviço de Aplicativo do Azure.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7b6d38f7755ebd97791959a5a414b09fe4ac769b
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718007"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Perguntas frequentes sobre bancos de dados MySql do ClearDB com o Serviço de Aplicativo do Azure
Estas perguntas frequentes respondem a dúvidas comuns sobre como usar e adquirir os bancos de dados MySQL do ClearDB para aplicativos Web do Azure.

> [!IMPORTANT]
> Desde 13 de junho de 2018, o ClearDB transferiu os clientes baseados no Azure, atualmente cobrados pela Microsoft, para um modelo de cobrança direta com este banco de dados. No momento, as informações deste artigo estão desatualizadas. Não será mais possível criar ou atualizar bancos de dados ClearDB criados no Microsoft Azure.
>
> Para obter mais detalhes e ver as etapas seguintes, confira [Alterações aos planos de serviço do ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Quais são as minhas opções para o MySQL no Azure?
Consulte [ClearDB](https://w2.cleardb.net/) para ver as informações mais recentes sobre esse serviço. O ClearDB é um serviço de hospedagem de MySQL e gerencia a infraestrutura do MySQL para você. 

Você tem várias outras opções para hospedar MySQL no Azure:
* [Banco de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql/)
* [Cluster MySQL em execução em uma VM do Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Instância única do MySQL em execução em uma VM do Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Preciso de um cartão de crédito para o modelo aplicativo Web + MySQL no Azure Marketplace?
Isso depende do tipo de assinatura que você está usando. Veja a seguir alguns tipos de assinatura normalmente usadas:

* [Pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/): exige um cartão de crédito, e quando você comprar um banco de dados MySQL pago, ele será cobrado no cartão de crédito.
* [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/): inclui créditos para uso com os serviços do Microsoft Azure, mas não permite a compra de recursos de terceiros. Para comprar serviços de terceiros ou um banco de dados MySQL pago, você precisa usar uma assinatura habilitada para cartão de crédito. Para aplicativos Web, você pode criar um banco de dados MySQL do ClearDB GRATUITO.
* [Assinatura do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) e **MSDN de desenvolvimento/teste pago conforme o uso**: semelhante à Avaliação gratuita, uma assinatura do MSDN exige que você tenha um cartão de crédito para comprar uma solução paga do MySQL da ClearDB.
* [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): os clientes de EA recebem em uma fatura consolidada e separada por seu EA a cada trimestre referente a todas as suas compras (de terceiros) no Azure Marketplace. Você será cobrado fora do compromisso monetário por qualquer compra no marketplace. Observe que, no momento, o Armazenamento do Azure não está disponível para clientes inscritos no Azerbaijão, na Croácia, na Noruega e em Porto Rico. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Por que recebi uma cobrança de US$ 3,50 por um aplicativo Web + MySQL no Azure Marketplace?
A opção de banco de dados padrão é Titan, que custa US$ 3,50. Não mostramos o custo durante a criação do banco de dados e talvez você compre um banco de dados que não pretendia por engano. Estamos tentando encontrar uma maneira de melhorar a experiência, mas até lá, você deverá verificar todos os tipos de preços selecionados para o aplicativo Web e o banco de dados antes de clicar em **Criar** e iniciar a implantação dos recursos.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Estou executando o MySQL em minha própria máquina virtual do Azure. Posso conectar meu aplicativo Web do Azure ao meu banco de dados?
Sim. Você pode conectar seu aplicativo Web ao seu banco de dados desde que sua VM do Azure tenha acesso remoto ao seu aplicativo Web. Para obter mais informações, confira [Instalar o MySQL em uma máquina virtual](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>Em quais países/regiões são clusters MySQL do ClearDB Premium tem suportadas?
Os clusters MySQL do ClearDB Premium estão disponíveis em todas as regiões do Azure no mundo, com exceção da Índia, da Austrália, do Sul do Brasil e da China.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Posso criar um novo cluster antes de criar um banco de dados com a solução de cluster ClearDB Premium?
Não, não há suporte para a criação de clusters ClearDB vazios. O portal do Azure permite que você crie bancos de dados em um cluster, o que permite a criação de um novo cluster ao mesmo tempo.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Serei avisado se tentar excluir um banco de dados MySQL do ClearDB que esteja sendo usado por um de meus aplicativos?
Não, o Azure não avisará se você excluir uma compra feita no Marketplace da qual seu aplicativo dependa.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Em quais regiões eu posso criar os bancos de dados ClearDB?
O Azure Marketplace não está disponível para clientes inscritos no Azerbaijão, na Croácia, na Noruega e em Porto Rico. O ClearDB não está disponível nessas regiões.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Qual tipo de preço eu devo escolher para um aplicativo Web de produção e banco de dados?
Use Basic ou um tipo de preço mais alto para aplicativos Web. Para o ClearDB, recomendamos um plano Saturn ou Jupiter. Examine os recursos e limitações de cada tipo de preço para os [aplicativos Web](https://azure.microsoft.com/pricing/details/app-service/) e os [bancos de dados MySQL do ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) e escolha aquele que atende às suas necessidades.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Como atualizar meu banco de dados ClearDB de um plano para outro?
No [portal do Azure](https://portal.azure.com), você pode escalar verticalmente um banco de dados de hospedagem compartilhada ClearDB. Leia este [artigo](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) para saber mais. Atualmente, não oferecemos suporte à atualização de clusters Premium do ClearDB no portal do Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Por que não consigo ver meu banco de dados ClearDB no portal do Azure?
Se você criou um banco de dados ClearDB no modo clássico, não será capaz de ver no seu banco de dados do [portal do Azure](https://portal.azure.com). Não há nenhuma solução alternativa para esse cenário.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Quem devo contatar para obter suporte quando meu banco de dados ficar inativo?
Entre em contato com o [Suporte do ClearDB](https://www.cleardb.com/developers/help/support) para quaisquer problemas relacionados ao banco de dados. Esteja preparado para fornecer as informações de sua assinatura do Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Posso criar usuários adicionais para minha solução de cluster de banco de dados MySQL ClearDB?
Nº Não é possível criar usuários adicionais, mas você pode criar bancos de dados adicionais no cluster do banco de dados ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Os bancos de dados das séries Basic/Pro podem ser atualizados no local semelhante aos planos Planetary atualmente no portal ClearDB?
Sim, a série de bancos de dados Basic pode ser atualizada no local (Basic 60 à Basic 500). A série Pro pode ser atualizada in-loco (Pro 125 à Pro 1000), com exceção da Pro 60. Não damos suporte para atualizar o banco de dados Pro 60 atualmente. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Quando migrar meus recursos de uma assinatura para outra, meu banco de dados MySQL ClearDB será migrado também?
Quando você executar recursos de migração entre assinaturas, serão aplicadas algumas [limitações](azure-resource-manager/move-support-resources.md) . O banco de dados MySQL ClearDB é um serviço de terceiros e, portanto, não será migrado durante a migração da assinatura do Azure. Se você não gerenciar a migração do banco de dados MySQL antes da migração de recursos do Azure, seus bancos de dados MySQL ClearDB poderão ser desabilitados. Primeiro, migre manualmente os bancos de dados e, em seguida, realize a migração da assinatura do Azure para seu aplicativo Web. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Eu atingir o limite de gastos em minha assinatura. Eu removi o limite e o meu Serviço de Aplicativo está online, no entanto, o banco de dados não está acessível. Como habilitar novamente o banco de dados ClearDB?
Contate o [Suporte do ClearDB](https://www.cleardb.com/developers/help/support) para habilitar novamente o banco de dados. Forneça suas informações de assinatura do Azure e nome do banco de dados.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Posso transferir um banco de dados ClearDB de uma assinatura de cartão de crédito para uma assinatura de EA?
Os bancos de dados ClearDB existentes usam o cartão de crédito associado às assinaturas existentes. Para usar uma assinatura de EA, você precisará migrar seus dados para um novo banco de dados:

* Compre um novo banco de dados ClearDB com sua assinatura do EA.
* Migre seus dados para o novo banco de dados.
* Atualize seu aplicativo para usar o novo banco de dados.
* Exclua o banco de dados ClearDB antigo.

Quando você cria um novo aplicativo Web com o MySQL (ClearDB) ou cria um banco de dados MySQL (ClearDB), a assinatura escolhida determina como você pagará pelo serviço. Com uma assinatura de EA, não bloquearemos a aquisição dos serviços de terceiros, como ClearDB, no portal do Azure. As assinaturas de EA são cobradas fora do Compromisso Monetário e são cobradas trimestralmente e mediante inadimplência. O cliente de EA precisa configurar um método de pagamento, como um cartão de crédito, a fim de pagar por quaisquer serviços de terceiros no marketplace.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Onde posso ver os encargos para os recursos do ClearDB em uma assinatura de EA?
Para clientes diretos do EA, os encargos do Azure Marketplace ficam visíveis no Portal Empresarial. Observe que todas as compras e consumo no marketplace são faturadas fora do Compromisso monetário e são cobradas trimestralmente e mediante inadimplência. Os clientes de EA precisam pagar diretamente aos provedores de serviços de terceiros e podem fazer isso habilitando um método de pagamento, como um cartão de crédito, com suas contas EA.

Os clientes indiretos do EA podem encontrar suas assinaturas do Azure Marketplace na página **Gerenciar Assinaturas** do Portal Empresarial, mas os preços ficam ocultos. Os clientes devem entrar em contato com seus LSPs para saber mais sobre encargos do Marketplace.

O acesso ao Azure Marketplace para serviços de terceiros pode ser gerenciado por seus administradores de registro do EA Azure. Eles podem desabilitar ou reabilitar o acesso a compras de terceiros no Armazenamento em Gerenciar Contas e Assinaturas na seção Contas no Portal Empresarial.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Quem devo contatar para perguntas sobre minha fatura para serviços do ClearDB em minha assinatura do EA?
Entre em contato com o [Atendimento ao Cliente Corporativo](https://aka.ms/AzureEntSupport) sobre a cobrança em seu registro no EA. A Equipe de Suporte do Portal de EA responderá à sua pergunta ou ajudará a resolver o problema.

## <a name="more-information"></a>Mais informações
[Perguntas frequentes sobre o Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)

