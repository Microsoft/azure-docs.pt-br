---
title: Criar Apache Kafka para a Confluent Cloud – Soluções de parceiros do Azure
description: Este artigo descreve como usar o portal do Azure para criar uma instância do Apache Kafka para a Confluent Cloud.
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98253623"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>Início Rápido: Introdução ao Apache Kafka para Confluent Cloud

Neste início rápido, você usará o portal do Azure para criar uma instância do Apache Kafka para a Confluent Cloud.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma assinatura ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
- Você deve ter a função _Proprietário_ ou _Colaborador_ para a assinatura do Azure. A integração entre o Azure e o Confluent só pode ser configurada por usuários com acesso de _Proprietário_ ou _Colaborador_. Antes de começar, [confirme que você tem o acesso apropriado](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Localizar oferta

Use o portal do Azure para localizar um aplicativo Apache Kafka para Confluent Cloud.

1. Em um navegador da Web, prossiga para o [portal do Azure](https://portal.azure.com/) e entre.

1. Se você acessou o **Marketplace** em uma sessão recente, selecione o ícone nas opções disponíveis. Caso contrário, pesquise _Marketplace_.

    :::image type="content" source="media/marketplace.png" alt-text="Ícone do Marketplace.":::

1. Na página **Marketplace**, você tem duas opções com base no tipo de plano desejado. Você pode se inscrever em um plano pago conforme o uso ou de compromisso. O plano pago conforme o uso está disponível publicamente. O plano de compromisso está disponível para clientes que foram aprovados para uma oferta privada.

   - Para clientes do plano **pago conforme o uso**, pesquise _Apache Kafka na Confluent Cloud_. Selecione a oferta para Apache Kafka na Confluent Cloud.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="pesquisar oferta do Azure Marketplace.":::

   - Para clientes do plano de **compromisso**, selecione o link para **Exibir ofertas privadas**. O plano de compromisso exige que você se inscreva para um valor mínimo de gastos. Use esta opção somente quando você precisar do serviço por um tempo estendido.

     :::image type="content" source="media/view-private-offers.png" alt-text="exibir ofertas privadas.":::

     Procure _Apache Kafka na Confluent Cloud_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="selecionar oferta privada.":::

## <a name="create-resource"></a>Criar recurso

Depois de selecionar a oferta para o Apache Kafka para Confluent Cloud, você estará pronto para configurar o aplicativo.

1. Se você tiver selecionado ofertas privadas na seção anterior, terá duas opções para tipos de plano:

    - Confluent Cloud – Pago conforme o uso
    - Compromisso – para plano de compromisso

   Se você não tiver selecionado ofertas particulares, terá apenas a opção pago conforme o uso.

   Selecione o plano a ser usado e **Configurar + assinar**.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Configurar e assinar.":::

1. Na página de noções básicas **Criar recursos da Confluent Cloud**, forneça os valores a seguir. Quando terminar, selecione **Avançar: Marcas**.

    :::image type="content" source="media/setup-basics.png" alt-text="Formulário para configurar o recurso da Confluent Cloud.":::

    | Propriedade | Descrição |
    | ---- | ---- |
    | **Assinatura** | No menu suspenso, selecione a assinatura do Azure para a qual fazer a implantação. Você precisa ter o acesso de _Proprietário_ ou _Colaborador_. |
    | **Grupo de recursos** | Especifique se deseja criar um grupo de recursos ou usar um grupo de recursos existente. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Para obter mais informações, consulte [Visão geral do Grupo de Recursos do Azure](../../azure-resource-manager/management/overview.md). |
    | **Nome da organização do Confluent** | Dê um nome ao recurso SaaS (software como serviço). |
    | **Região** | No menu suspenso, selecione uma destas regiões: <br/><br/> Leste da Austrália, Canadá Central, EUA Central, Leste dos EUA, Leste dos EUA 2, França Central, Norte da Europa, Sudeste da Ásia, Sul do Reino Unido, Centro-Oeste dos EUA, Oeste da Europa, Oeste dos EUA 2 |
    | **Plano** | Selecione **Pago conforme o uso** ou **Compromisso**. |
    | **Prazo de cobrança** | Preenchido previamente com base no plano de cobrança selecionado. |
    | **Preço** | Preenchido previamente com base no plano do Confluent selecionado. |

1. Em **Marcas**, forneça os pares **nome** e **valor** para as marcas que você deseja aplicar ao recurso. Depois de inserir as marcas, selecione **Examinar + Criar**.

    :::image type="content" source="media/setup-tags.png" alt-text="Adicionar marcas do projeto.":::

1. Examine as configurações fornecidas. Quando estiver pronto, selecione **Criar**.

1. A criação do recurso leva alguns minutos. Você pode exibir o status da implantação em **Notificações**. Depois que a implantação for concluída, selecione o recurso para exibir a página **Visão geral**.

    :::image type="content" source="media/deployment-status.png" alt-text="Status da implantação.":::

   Se você receber um erro, confira [Solucionar problemas de soluções do Apache Kafka para Confluent Cloud](troubleshoot.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o recurso da Confluent Cloud](manage.md)
