---
title: Visão geral de Apache Kafka na nuvem confluente – soluções de parceiros do Azure
description: Saiba como usar Apache Kafka na nuvem confluente no Azure Marketplace.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: b4aa00b7a2cc7ca6bc968be70957c127a8e00738
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253322"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>O que é Apache Kafka para nuvem confluente?

Apache Kafka para nuvem confluente é uma oferta do Azure Marketplace que fornece Apache Kafka como um serviço. Ele é totalmente gerenciado para que você possa se concentrar na criação de seus aplicativos em vez de gerenciar os clusters.

Para reduzir a carga do gerenciamento entre plataformas, a Microsoft estabeleceu uma parceria com a nuvem confluente para criar uma camada de provisionamento integrada do Azure para a nuvem confluente. Ele fornece uma experiência consolidada para usar a nuvem confluente no Azure. Você pode facilmente integrar e gerenciar a nuvem antifluente com seus aplicativos do Azure.

Anteriormente, era necessário comprar a oferta de nuvem confluente no Marketplace e configurar separadamente a conta na nuvem confluente. Para gerenciar as configurações e os recursos, você precisava navegar entre os portais do Azure e da nuvem confluente.

Agora, você provisiona os recursos de nuvem confluentes por meio de um provedor de recursos chamado **Microsoft. prefluente**. Você cria e gerencia recursos de organização de nuvem confluentes por meio dos SDKs [portal do Azure](https://portal.azure.com/), [CLI do Azure](/cli/azure/)ou [Azure](/azure/#languages-and-tools). A nuvem confluente possui e executa o aplicativo SaaS (software como serviço), incluindo os ambientes, os clusters, os tópicos, as chaves de API e os conectores gerenciados.

## <a name="capabilities"></a>Funcionalidades

A profunda integração entre a nuvem entre o Fluent e o Azure permite os seguintes recursos:

- Provisione um novo recurso de organização de nuvem confluente do portal do Azure com infraestrutura totalmente gerenciada.
- Simplifique o SSO (logon único) do Azure para a nuvem confluente com o Azure Active Directory (Azure AD). Nenhuma autenticação separada é necessária no portal de nuvem confluente.
- Obtenha uma cobrança unificada do consumo de nuvem de um meio por meio do faturamento de assinatura do Azure.
- Gerencie recursos de nuvem confluentes do portal do Azure e acompanhe-os na página **todos os recursos** com seus outros recursos do Azure.

## <a name="confluent-organization"></a>Organização confluente

Uma organização confluente é um recurso que fornece o mapeamento entre o Azure e os recursos de nuvem confluentes. É o recurso pai para outros recursos de nuvem confluentes.

Cada assinatura do Azure pode conter vários planos confluentes. Cada plano de entrada é mapeado para uma conta de usuário e uma organização no portal do próprio Fluent. Em cada organização confluente, você pode criar vários ambientes, clusters, tópicos e conectores.

Ao provisionar um recurso de nuvem confluente no Azure, você obtém uma ID de organização confluente, um ambiente padrão e uma conta de usuário. Para obter mais informações, consulte [início rápido: introdução à nuvem confluente no Azure](create.md).

Para cobrança, cada oferta de nuvem confluente adquirida no Marketplace é mapeada para uma organização confluente exclusiva.

## <a name="single-sign-on"></a>Logon único

Quando você entra no portal do Azure, suas credenciais também são usadas para entrar no portal de SaaS de nuvem antifluent. A experiência usa o [Azure ad](../../active-directory/fundamentals/active-directory-whatis.md) e o [SSO do Azure ad](../../active-directory/manage-apps/what-is-single-sign-on.md) para fornecer uma maneira segura e conveniente para você entrar.

Para obter mais informações, consulte [logon único](manage.md#single-sign-on).

## <a name="billing"></a>Cobrança

Há duas opções de cobrança disponíveis: plano mensal pago e o plano de compromisso.

- Com o **plano mensal pago conforme o uso**, você receberá encargos de consumo de nuvem confluentes em sua fatura mensal do Azure.
- Com um **plano de compromisso**, você se inscreve para um valor mínimo de gastos e Obtém um desconto no uso confirmado da nuvem confluente.

Você decide qual opção de cobrança usar ao criar o serviço.

## <a name="confluent-links"></a>Links confluentes

Para obter ajuda adicional com o uso de Apache Kafka para nuvem confluente, consulte os links a seguir para o site da sua própria [Fluent](https://docs.confluent.io/home/overview.html).

Para saber mais sobre as opções de cobrança, consulte:

* [Azure Marketplace com pago conforme o uso](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [Azure Marketplace com compromissos](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

Para saber mais sobre como gerenciar as soluções, consulte:

* [Criar um cluster na nuvem confluente](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Ambientes de nuvem confluentes](https://docs.confluent.io/current/cloud/using/environments.html)
* [Noções básicas de nuvem confluente](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

Para obter suporte e termos, consulte:

* [Suporte de apoio](https://support.confluent.io)
* [Termos de serviço](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>Próximas etapas

Para criar uma instância de Apache Kafka para nuvem confluente, confira [início rápido: introdução à nuvem em inglês no Azure](create.md).
