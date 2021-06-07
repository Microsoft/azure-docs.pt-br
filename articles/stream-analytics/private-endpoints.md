---
title: Criar e excluir pontos de extremidade privados e gerenciados em um cluster do Azure Stream Analytics
description: Saiba como gerenciar pontos de extremidade privados em um cluster do Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 9939130782594c03a497d98ce6cd9b33b28eadec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101718396"
---
# <a name="create-and-delete-managed-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Criar e excluir pontos de extremidade privados e gerenciados em um cluster do Azure Stream Analytics

Você pode conectar os seus trabalhos do Azure Stream Analytics em execução em um cluster para a entrada e a saída de recursos que são protegidos por um firewall ou por uma VNet (Rede Virtual) do Azure. Primeiro, crie um ponto de extremidade privado e gerenciado para um recurso, como o Hub de Eventos do Azure ou o Banco de Dados SQL do Azure, no cluster do Stream Analytics. Em seguida, aprove a conexão de ponto de extremidade privado da entrada ou saída.

Depois que você aprovar a conexão, qualquer trabalho em execução no cluster do Stream Analytics poderá acessar o recurso por meio do ponto de extremidade privado. Este artigo mostra como criar e excluir pontos de extremidade privados em um cluster do Stream Analytics. Você pode criar pontos de extremidade privados para o Banco de Dados SQL do Azure, Armazenamento do Azure, Azure Data Lake Storage Gen2, Hub de Eventos do Azure e Barramento de Serviço do Azure. Pontos de extremidade privados para outros serviços serão adicionados em breve. 

## <a name="create-managed-private-endpoint-in-stream-analytics-cluster"></a>Criar um ponto de extremidade privado e gerenciado no cluster do Stream Analytics

Nesta seção, você aprenderá a criar um ponto de extremidade privado em um cluster do Stream Analytics.

1. No portal do Azure, localize e selecione seu cluster do Stream Analytics.

1. Em **Configurações**, selecione **Pontos de extremidade privados e gerenciados**.

1. Clique em **Novo** e insira as informações abaixo para escolher o recurso que você deseja acessar com segurança por meio de um ponto de extremidade privado.

   |Configuração|Valor|
   |---|---|
   |Nome|Insira qualquer nome para seu ponto de extremidade privado. Se esse nome já estiver sendo usado, crie um nome exclusivo.|
   |Método de conexão|Selecione **Conectar-se a um recurso do Azure em meu diretório**.<br><br>Você pode escolher um dos seus recursos para se conectar com segurança usando o ponto de extremidade privado ou pode se conectar ao recurso de outra pessoa usando uma ID do recurso ou um alias que ela compartilhou com você.|
   |Subscription|Selecione sua assinatura.|
   |Tipo de recurso|Escolha o [tipo de recurso que é mapeado para o seu recurso](../private-link/private-endpoint-overview.md#private-link-resource).|
   |Recurso|Selecione o recurso ao qual você deseja se conectar usando o ponto de extremidade privado.|
   |Sub-recurso de destino|O tipo de sub-recurso para o recurso selecionado acima que o seu ponto de extremidade privado será capaz de acessar.|

   ![A experiência de criação do ponto de extremidade privado](./media/private-endpoints/create-private-endpoint.png)

1. Aprove a conexão do recurso de destino. Por exemplo, se você criou um ponto de extremidade privado para uma instância do Banco de Dados SQL do Azure na etapa anterior, deverá acessar essa instância e ver uma conexão pendente que deve ser aprovada. Pode levar alguns minutos para que a solicitação de conexão seja exibida.

    ![aprovar o ponto de extremidade privado](./media/private-endpoints/approve-private-endpoint.png)

1. Você pode voltar para o cluster do Stream Analytics para ver a alteração de estado de **Aprovação de cliente pendente** para **Configuração de DNS Pendente** e para **Configuração completa** em alguns minutos.

## <a name="delete-a-managed-private-endpoint-in-a-stream-analytics-cluster"></a>Excluir um ponto de extremidade privado e gerenciado em um cluster do Stream Analytics

1. No portal do Azure, localize e selecione seu cluster do Stream Analytics.

1. Em **Configurações**, selecione **Pontos de extremidade privados e gerenciados**.

1. Selecione o ponto de extremidade privado que você deseja excluir e selecione **Excluir**.

   ![excluir o ponto de extremidade privado](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Próximas etapas

Agora você tem uma visão geral de como gerenciar pontos de extremidade privados em um cluster do Azure Stream Analytics. Em seguida, você pode aprender a escalar os seus clusters e executar trabalhos no seu cluster:

* [Escalar um cluster do Azure Stream Analytics](scale-cluster.md)
* [Gerenciar trabalhos do Stream Analytics em um cluster do Stream Analytics](manage-jobs-cluster.md)
