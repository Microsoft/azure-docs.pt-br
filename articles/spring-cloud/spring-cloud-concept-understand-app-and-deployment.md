---
title: Entender o aplicativo e a implantação no Azure Spring Cloud
description: A distinção entre aplicativo e implantação no Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: c053a7830e02eb7c460bd030ca3c6a10c00ea78a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323630"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Entender o aplicativo e a implantação no Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

O **aplicativo** e a **implantação** são os dois conceitos principais do modelo de recurso do Azure Spring Cloud. No Azure Spring Cloud, um *aplicativo* é uma abstração de um aplicativo de negócios ou de um microserviço.  Uma versão do código ou binário implantado como o *aplicativo* é executado em uma *implantação*.  Os aplicativos são executados em uma *instância de serviço de nuvem do Azure Spring*ou simplesmente *instância de serviço*, conforme mostrado a seguir.

 ![Aplicativos e implantações](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

Você pode ter várias instâncias de serviço em uma única assinatura do Azure, mas o serviço de nuvem do Azure Spring é mais fácil de usar quando todos os aplicativos que compõem um aplicativo de negócios ou um microserviço residem em uma única instância de serviço.

A camada padrão do Azure Spring Cloud permite que um aplicativo tenha uma implantação de produção e uma implantação de preparo, para que você possa fazer uma implantação azul/verde com facilidade.

## <a name="app"></a>Aplicativo
Os seguintes recursos/propriedades são definidos no nível do aplicativo.

| Recursos | Descrição |
|:--|:----------------|
| Público</br>Ponto de extremidade | A URL para acessar o aplicativo |
| Personalizado</br>Domínio | Registro CNAME que protege o domínio personalizado |
| Serviço</br>Associação | Conexão integrada com outros serviços do Azure |
| Gerenciados</br>Identidade | A identidade gerenciada por Azure Active Directory permite que seu aplicativo acesse facilmente outros recursos protegidos pelo Azure AD, como Azure Key Vault |
| Persistente</br>Armazenamento | Configuração que permite que os dados persistam além da reinicialização do aplicativo |

## <a name="deployment"></a>Implantação

Os seguintes recursos/propriedades são definidos no nível de implantação e serão trocados ao trocar a implantação de produção/preparo.

| Recursos | Descrição |
|:--|:----------------|
| CPU | Número de vcores por instância de aplicativo |
| Memória | GB de memória por instância de aplicativo|
| Instância</br>Contagem | O número de instâncias do aplicativo, definido manualmente ou automaticamente |
| Dimensionamento automático | Dimensionar automaticamente a contagem de instâncias com base em regras e agendas predefinidas |
| JVM</br>Opções | Definindo opções de JVM  |
| Ambiente</br>Variáveis | Configurando variáveis de ambiente |
| Runtime</br>Versão | Java 8/Java 11|

## <a name="restrictions"></a>Restrições

* **Um aplicativo deve ter uma implantação de produção**: a exclusão de uma implantação de produção é bloqueada pela API. Ele deve ser trocado para preparo antes da exclusão.
* **Um aplicativo pode ter no máximo duas implantações**: a criação de mais de duas implantações é bloqueada pela API. Implante seu novo binário para a implantação existente ou para preparo de produção.
* O **Gerenciamento de implantação não está disponível na camada básica**: Use a camada Standard para Blue-Green recurso de implantação.

## <a name="see-also"></a>Confira também
* [Configurar um ambiente de preparo no Azure Spring Cloud](spring-cloud-howto-staging-environment.md)
