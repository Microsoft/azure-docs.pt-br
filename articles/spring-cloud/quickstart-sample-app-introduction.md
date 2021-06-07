---
title: Início rápido – Introdução ao aplicativo de exemplo – Azure Spring Cloud
description: Descreve o aplicativo de exemplo usado nesta série de guias de início rápido para implantação no Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878830"
---
# <a name="introduction-to-the-sample-app"></a>Introdução ao aplicativo de exemplo

::: zone pivot="programming-language-csharp"
Esta série de guias de início rápido usa um aplicativo de exemplo composto por dois microsserviços para mostrar como implantar um aplicativo Steeltoe do .NET Core no serviço Azure Spring Cloud. Você usará as funcionalidades do Azure Spring Cloud, como descoberta de serviço, Config Server, logs, métricas e rastreamento distribuído.

## <a name="functional-services"></a>Serviços funcionais

O aplicativo de exemplo é composto por dois microsserviços:

* O serviço `planet-weather-provider` retorna o texto meteorológico em resposta a uma solicitação HTTP que especifica o nome do planeta. Por exemplo, pode retornar "muito quente" para o planeta Mercúrio. Ele obtém os dados meteorológicos do Config Server. O Config Server obtém os dados meteorológicos de um arquivo YAML em um repositório Git, por exemplo:

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* O serviço `solar-system-weather` retorna dados para quatro planetas em resposta a uma solicitação HTTP. Ele obtém os dados fazendo quatro solicitações HTTP para `planet-weather-provider`. Ele usa o serviço de descoberta do servidor Eureka para chamar `planet-weather-provider`. Ele retorna JSON, por exemplo:

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

O diagrama a seguir ilustra a arquitetura do aplicativo de exemplo:

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Diagrama de aplicativo de exemplo":::

## <a name="code-repository"></a>Repositório de códigos

O aplicativo de exemplo está localizado na pasta [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) do repositório Azure-Samples/Azure-Spring-Cloud-Samples no GitHub.

As instruções nos guias de início rápido a seguir referem-se ao código-fonte conforme necessário.

::: zone-end

::: zone pivot="programming-language-java"
Neste guia de início rápido, usamos um exemplo de finanças pessoais chamado PiggyMetrics para mostrar como implantar um aplicativo no serviço Azure Spring Cloud. O PiggyMetrics demonstra o padrão de arquitetura de microsserviço e realça o detalhamento de serviços. Você verá como ele é implantado no Azure com funcionalidades avançadas do Azure Spring Cloud, incluindo descoberta de serviço, Config Server, logs, métricas e rastreamento distribuído.

Para seguir os exemplos de implantação do Azure Spring Cloud, você precisa apenas da localização do código-fonte, que é fornecido conforme necessário.

## <a name="functional-services"></a>Serviços funcionais

O PiggyMetrics é decomposto em três microsserviços principais. Todos eles são aplicativos implantáveis independentemente organizados por domínios empresariais.

* **Serviço de conta (A ser implantado)** : Contém a validação e a lógica de entrada do usuário gerais: resultados/itens de despesas, economia e configurações de conta.
* **O serviço de estatísticas (Não usado neste guia de início rápido)** : Executa cálculos em parâmetros de estatísticas principais e captura séries temporais para cada conta. O Datapoint contém valores, normalizados para a moeda base e o período de tempo. Esses dados são usados para rastrear a dinâmica do fluxo de caixa no tempo de vida da conta.
* **Serviço de notificação (Não usado neste guia de início rápido)** : Armazena as informações de contato dos usuários e as configurações de notificação, como a frequência de lembrete e de backup. O trabalho agendado coleta informações necessárias de outros serviços e envia mensagens de email para os clientes assinados.

## <a name="infrastructure-services"></a>Serviços de infraestrutura

Há vários padrões comuns em sistemas distribuídos que ajudam a fazer os serviços principais funcionarem. O Azure Spring Cloud fornece ferramentas poderosas que aprimoram o comportamento dos aplicativos Spring Boot para implementar estes padrões: 

* **Serviço de configuração (Hospedado pelo Azure Spring Cloud)** : A Configuração do Azure Spring Cloud é um serviço de configurações centralizado escalável horizontalmente para sistemas distribuídos. Ela usa um repositório conectável que atualmente dá suporte ao armazenamento local, Git e Subversion.
* **Descoberta de serviço (Hospedado pelo Azure Spring Cloud)** : Ela permite a detecção automática de locais de rede para instâncias de serviço, que podem ter endereços atribuídos dinamicamente por causa de dimensionamento automático, falhas e atualizações.
* **Serviço de autenticação (A ser implantado)** : as responsabilidades de autorização são extraídas completamente para um servidor separado, que concede tokens OAuth2 para os serviços de recursos de back-end. O Servidor de Autenticação realiza a autorização do usuário e protege a comunicação entre computadores dentro de um perímetro.
* **Gateway de API (A ser implantado)** : Os três serviços principais expõem uma API externa ao cliente. Em sistemas reais, o número de funções pode crescer muito rapidamente com a complexidade do sistema. Centenas de serviços podem estar envolvidos na renderização de uma página da Web complexa. O Gateway de API é um ponto de entrada único no sistema, usado para lidar com solicitações e roteá-las para o serviço de back-end apropriado ou para invocar vários serviços de back-end, agregando os resultados. 

## <a name="sample-usage-of-piggymetrics"></a>Exemplo de uso do PiggyMetrics

Confira os obter detalhes completos de implementação em [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics). Os exemplos fazem referência ao código-fonte, conforme necessário.
::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Provisionar a instância do Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
