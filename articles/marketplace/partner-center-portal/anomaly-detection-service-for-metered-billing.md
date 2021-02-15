---
title: Serviço de detecção de anomalias para cobrança limitada-Microsoft Azure Marketplace
description: Descreve como a detecção de anomalias funciona, quando as notificações são enviadas e o que fazer com elas e opções de suporte.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: 909b818c60932f5b1d6071ea1ccf0ee4c76173b4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280542"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Serviço de detecção de anomalias para cobrança limitada

O [serviço de medição do Marketplace](marketplace-metering-service-apis-faq.md) permite que você crie ofertas no programa comercial do Marketplace que são cobrados de acordo com as unidades não padrão. Com a cobrança limitada, você envia eventos de uso para o uso do cliente para a Microsoft e preparamos a cobrança com base nesse uso.

Dados de uso incorretos podem vir de várias causas, como bugs, configurações incorretas em seu controle de consumo ou fraude. Dados de uso incorretos resultarão em cobranças incorretas de clientes e em disputas de faturamento

Para atenuar o risco, nosso serviço de detecção de anomalias aplica algoritmos de aprendizado de máquina para determinar o comportamento de cobrança medido normal, analisar o uso de cobrança medido e descobrir anomalias com intervenção mínima do usuário.

Você será notificado se houver anomalia detectada em seu uso de cobrança limitada. Isso lhe dá a oportunidade de investigar e nos notificar se uma anomalia for confirmada como um problema real, em que as ações de ponto podem ser tomadas para resolver o problema de cobrança do cliente de forma proativa.

Além de picos repentinos, Dips e alterações de tendência de uso de cobrança medido, nosso modelo também conta com efeitos sazonais. Como a cobrança limitada é comunicada por meio de dados excedentes, nosso modelo também é capaz de lidar normalmente com longos períodos de dados ausentes.

Veja a seguir exemplos de resultados de detecção de anomalias. O intervalo esperado é mostrado como uma faixa amarela. O uso de cobrança medido aceitável é mostrado como estrelas verdes na banda. O uso de cobrança fora da banda é exibido como um ponto vermelho.  

Anomalias detectadas fora de uma tendência previsível:

![Ilustra as anomalias detectadas fora de uma tendência previsível.](media/anomaly-1.png)

Anomalias detectadas fora de uma tendência cíclica recorrente:

![Ilustra as anomalias detectadas fora de uma tendência cíclica recorrente.](media/anomaly-2.png)

Anomalias detectadas em uma tendência ascendente:

![Ilustra as anomalias detectadas em uma tendência ascendente.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Como funciona o serviço de detecção de anomalias

A detecção de anomalias é habilitada automaticamente para todo o uso de cobrança medido. Quando você envia os eventos de uso para a Microsoft, o serviço de detecção de anomalias cria um modelo de valores esperados com base nos dados de uso anteriores. Esse modelo é executado semanalmente.

As funções de detecção de anomalias em um nível por medidor e por cliente. Isso significa que cada medidor com cada cliente terá um modelo treinado com base no padrão de uso passado deste cliente desse medidor.

O modelo funciona gerando intervalos de confiança retrospectiva. A previsão de série temporal é um modelo aditivo generalizado que consiste em uma parte de previsão de tendência e uma parte sazonalidade. Como o modelo é formulado como uma tarefa de regressão, ele pode lidar normalmente com longos períodos de dados ausentes. Se uma observação cair fora dos intervalos de confiança previstos, isso significa que a observação não pode ser explicada com base nos padrões históricos da cobrança limitada e, portanto, pode ser uma anomalia.

## <a name="anomaly-detection-notification"></a>Notificação de detecção de anomalias

Enviamos avisos de detecção de anomalias por email semanalmente. Ele inclui todas as anomalias detectadas na semana para todos os medidores e clientes. Este email é enviado para os contatos de **engenharia** e **suporte** fornecidos quando você criou a oferta.

Você deve investigar se as anomalias detectadas são problemas reais e, nesse caso, entrar em contato com a Microsoft para relatar o uso incorreto (consulte a seção de suporte abaixo).

Se você confirmar que as anomalias detectadas são de uso normal, nenhuma ação adicional será necessária. No entanto, se uma anomalia representar um risco financeiro potencialmente alto, poderemos contatá-lo para confirmar o uso.  

## <a name="when-and-how-to-get-support"></a>Quando e como obter suporte

Se você enviou o uso incorreto à Microsoft e isso resultou em uma subcarga do cliente, a Microsoft não iniciará uma fatura para o cliente para uso informado ou pague você pelo uso. Você precisará arcar com a perda de receita devido a erros no relatório de uso.

Se um dos seguintes casos se aplicar, você poderá abrir um tíquete de suporte para solicitar reembolso ou ajuste de cobrança para seus clientes:

- Você confirmou que uma das anomalias que encontramos é um problema real e o uso incorreto resultaria em um **sobrecarga** do cliente.
- Você descobre que você enviou o uso incorreto para nós e o uso incorreto resultaria em um **sobrecarga** do cliente.
- Você gostaria de solicitar um reembolso pelo encargo do uso de cobrança limitado do seu cliente.

Para enviar um tíquete:

1. Vá para a página de suporte. Na **caixa Conte-nos sobre o seu problema**, digite "uso incorreto".
2. Em tópicos de suporte, na lista suspensa dos resultados da pesquisa, selecione uma das seguintes opções:
    - **Marketplace comercial**  >  **Cobrança limitada**  >  **Uso incorreto enviado para a oferta de aplicativos do Azure**ou
    - **Marketplace comercial**  >  **Cobrança limitada**  >  **Uso incorreto enviado para a oferta de SaaS**
3. Na **próxima etapa**, selecione o botão **examinar soluções** para ser direcionado para entrar no Partner Center para enviar um tíquete de suporte.

Para obter mais opções de suporte do Publicador, consulte [suporte para o programa comercial Marketplace no Partner Center](../support.md).

## <a name="next-step"></a>Próxima etapa

- Saiba mais sobre a [API do serviço de medição do Marketplace](marketplace-metering-service-apis.md).
