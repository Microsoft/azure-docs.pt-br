---
title: Criar alertas para Azure Cosmos DB usando Azure Monitor
description: Saiba como configurar alertas para Azure Cosmos DB usando Azure Monitor.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: e29db7e31438bc7f6ac609384d0d9b92c275e813
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339522"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>Criar alertas para Azure Cosmos DB usando Azure Monitor
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Os alertas são usados para configurar testes recorrentes para monitorar a disponibilidade e a capacidade de resposta de seus recursos de Azure Cosmos DB. Os alertas podem enviar uma notificação na forma de um email ou executar uma função do Azure quando uma de suas métricas atinge o limite ou se um evento específico é registrado no log de atividades.

Você pode receber um alerta com base nas métricas ou nos eventos do log de atividades em sua conta do Azure Cosmos:

* **Métricas** – o alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite que você atribui. Por exemplo, quando as unidades de solicitação totais consumidas excederem 1000 RU/s. Esse alerta é disparado quando a condição é atendida pela primeira vez e depois quando essa condição não é mais atendida. Consulte o artigo de [referência de dados de monitoramento](monitor-cosmos-db-reference.md#metrics) para obter diferentes métricas disponíveis em Azure Cosmos DB.

* **Eventos do log de atividades** – este alerta é disparado quando ocorre um determinado evento. Por exemplo, quando as chaves de sua conta do Azure cosmos são acessadas ou atualizadas.

Você pode configurar alertas no painel de Azure Cosmos DB ou no serviço de Azure Monitor no portal do Azure. Ambas as interfaces oferecem as mesmas opções. Este artigo mostra como configurar alertas para Azure Cosmos DB usando Azure Monitor.

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta

Esta seção mostra como criar um alerta quando você recebe um código de status HTTP 429, que é recebido quando as solicitações são limitadas por taxa. Para obter exemplos, talvez você queira receber um alerta quando houver 100 ou mais solicitações com taxas limitadas. Este artigo mostra como configurar um alerta para esse cenário usando o código de status HTTP. Você pode usar as etapas semelhantes para configurar outros tipos de alertas também, basta escolher uma condição diferente com base em seu requisito.

1. Entre no [portal do Azure.](https://portal.azure.com/)

1. Selecione **Monitor** na barra de navegação à esquerda e selecione **alertas**.

1. Selecione o botão nova regra de alerta para abrir o painel criar regra de alerta.  

1. Preencha a seção **escopo** :

   * Abra o painel **selecionar recurso** e configure o seguinte:

   * Escolha o nome da sua **assinatura** .

   * Selecione **contas de Azure Cosmos DB** para o **tipo de recurso**.

   * O **local** da sua conta do Azure Cosmos.

   * Depois de preencher os detalhes, será exibida uma lista de contas do Azure Cosmos no escopo selecionado. Escolha aquele para o qual você deseja configurar alertas e selecione **concluído**.

1. Preencha a seção **condição** :

   * Abra o painel **Selecionar condição** para abrir a página **Configurar lógica de sinal** e configure o seguinte:

   * Selecione um sinal. O **tipo de sinal** pode ser uma **métrica** ou um **log de atividades**. Escolha **métricas** para este cenário. Porque você deseja receber um alerta quando houver problemas de limitação de taxa na métrica de unidades de solicitação total.

   * Selecionar **tudo** para o **serviço monitor**

   * Escolha um **nome de sinal**. Para obter um alerta para códigos de status HTTP, escolha o sinal **total de unidades de solicitação** .

   * Na próxima guia, você pode definir a lógica para disparar um alerta e usar o gráfico para exibir as tendências da sua conta do Azure Cosmos. A métrica **total de unidades de solicitação** dá suporte a dimensões. Essas dimensões permitem que você filtre a métrica. Se você não selecionar nenhuma dimensão, esse valor será ignorado.

   * Escolha **StatusCode** como o **nome da dimensão**. Selecione **adicionar valor personalizado** e defina o código de status como 429.

   * Na **lógica de alerta**, defina o **limite** como **estático**. O limite estático usa um valor de limite definido pelo usuário para avaliar a regra, enquanto os limites dinâmicos usam algoritmos de aprendizado de máquina internos para aprender continuamente o padrão de comportamento de métrica e calcular os limites automaticamente.

   * Defina o **operador** como **maior que**, o **tipo de agregação** para **Total** e o **valor do limite** como **100**. Com essa lógica, se o cliente perceber mais de 100 solicitações com um código de status 429, o alerta será disparado. Você também pode configurar o tipo de agregação, a granularidade de agregação e a frequência de avaliação com base em seu requisito.

   * Depois de preencher o formulário, selecione **concluído**. A captura de tela a seguir mostra os detalhes da lógica de alerta:

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="Configurar a lógica para receber alertas para solicitações de taxa limitada/429":::

1. Preencha a seção **grupo de ações** :

   * No painel **criar regra** , selecione um grupo de ações existente ou crie um novo grupo de ação. Um grupo de ações permite que você defina a ação a ser executada quando ocorrer uma condição de alerta. Para este exemplo, crie um novo grupo de ações para receber uma notificação por email quando o alerta for disparado. Abra o painel **Adicionar grupo de ações** e preencha os seguintes detalhes:

   * **Nome do grupo de ações** -o nome do grupo de ações deve ser exclusivo dentro de um grupo de recursos.

   * **Nome curto** -o nome curto do grupo de ações, esse valor é incluído no email e notificações de SMS para identificar qual grupo de ação foi a origem da notificação.

   * Escolha a assinatura e o grupo de recursos no qual esse grupo de ações será criado.  

   * Forneça um nome para a ação e selecione **email/mensagem SMS/Push/voz** como o **tipo de ação**. A captura de tela a seguir mostra os detalhes do tipo de ação:

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="Configurar o tipo de ação, como notificação por email, para receber o alerta":::

1. Preencha a seção **detalhes da regra de alerta** :

   * Defina um nome para a regra, forneça uma descrição opcional, o nível de severidade do alerta, escolha se deseja habilitar a regra na criação de regra e, em seguida, selecione **criar alerta de regra** para criar o alerta de regra de métrica.

Depois de criar o alerta, ele estará ativo em 10 minutos.

## <a name="common-alerting-scenarios"></a>Cenários comuns de alerta

A seguir estão alguns cenários em que você pode usar alertas:

* Quando as chaves de uma conta do Azure cosmos são atualizadas.
* Quando o uso de dados ou índice de um contêiner, banco de dado ou de uma região excede um determinado número de bytes.
* Quando o consumo de RU/s normalizado for maior que determinado percentual. A métrica de consumo de RU normalizada fornece a utilização de taxa de transferência máxima dentro de um conjunto de réplicas. Para saber mais, consulte o artigo [como monitorar as ru/s normalizadas](monitor-normalized-request-units.md) .  
* Quando uma região é adicionada, removida ou se fica offline.
* Quando um banco de dados ou um contêiner é criado, excluído ou atualizado.
* Quando a taxa de transferência do banco de dados ou o contêiner é alterado.

## <a name="next-steps"></a>Próximas etapas

* Como [monitorar a métrica de ru/s normalizada](monitor-normalized-request-units.md) no contêiner Cosmos do Azure.
* Como [monitorar a taxa de transferência ou o uso da unidade de solicitação](monitor-request-unit-usage.md) de uma operação no Azure Cosmos DB.