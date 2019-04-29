---
title: CORS (Compartilhamento de Recursos entre Origens) no Azure Cosmos DB
description: Este artigo descreve como configurar o CORS (Compartilhamento de Recursos entre Origens) no Azure Cosmos DB, usando os modelos do Azure Resource Manager e portal do Azure.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2018
ms.author: dech
ms.openlocfilehash: 689b0bb08e300efc7c5e93dbf346040b82a7c52d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059749"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Configurar CORS (Compartilhamento de Recursos entre Origens) 

O CORS (Compartilhamento de Recursos entre Origens) é um recurso HTTP que permite que um aplicativo Web em execução em um domínio acesse recursos em outro domínio. Os navegadores da Web implementam uma restrição de segurança, conhecida como política de mesma origem, que impede que uma página da Web chame APIs em um domínio diferente. No entanto, o CORS fornece uma maneira segura de permitir que o domínio de origem chame APIs em outro domínio. A API do SQL Core no Azure Cosmos DB agora dá suporte para CORS (Compartilhamento de Recursos entre Origens) usando o cabeçalho "allowedOrigins". Após habilitar o suporte para CORS na conta do Azure Cosmos, somente as solicitações autenticadas serão avaliadas para determinar se são permitidas de acordo com as regras especificadas.

É possível definir a configuração de CORS (Compartilhamento de Recursos entre Origens) no portal do Azure ou em um modelo do Azure Resource Manager. A API do SQL Core no Azure Cosmos DB dá suporte a uma biblioteca JavaScript que funciona em ambientes Node.js e com base em navegador. Essa biblioteca agora pode aproveitar o suporte do CORS ao usar o modo Gateway. Não há configuração do lado do cliente necessária para usar esse recurso. Com o suporte de CORS, os recursos de um navegador podem acessar diretamente o Azure Cosmos DB por meio da [Biblioteca JavaScript](https://www.npmjs.com/package/@azure/cosmos) ou diretamente da [API REST](https://docs.microsoft.com/rest/api/cosmos-db/) para operações simples. 

## <a name="enable-cors-support-from-azure-portal"></a>Habilitar o suporte de CORS do portal do Azure

Use as etapas a seguir para habilitar o Compartilhamento de Recursos entre Origens, usando o portal do Azure:

1. Navegue até a conta do Azure Cosmos DB. Abra a folha **CORS**.

2. Especifique uma lista de origens separadas por vírgulas que pode fazer chamadas entre origens para a conta do Azure Cosmos DB. Por exemplo, `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Também é possível usar um curinga "\*" para permitir todas as origens e selecione **Enviar**. 

   > [!NOTE]
   > Atualmente, não é possível usar curingas como parte do nome do domínio. Por exemplo, ainda não há suporte para o formato `https://*.mydomain.net`. 
   
   ![Habilitar o compartilhamento de recursos entre origens usando o portal do Azure](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>Habilitar o suporte para CORS a partir do modelo do Resource Manager

Para habilitar o CORS usando um modelo do Resource Manager, adicione a seção "cors" com a propriedade "allowedOrigins" a qualquer modelo existente. O JSON a seguir é um exemplo de um modelo que cria uma nova conta do Azure Cosmos com o CORS habilitado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Usar a biblioteca JavaScript do Azure Cosmos DB a partir de um navegador

Atualmente, a biblioteca JavaScript do Azure Cosmos DB tem apenas a versão CommonJS da biblioteca enviada com o pacote. Para usar essa biblioteca no navegador, é necessário usar uma ferramenta como Rollup ou Webpack para criar uma biblioteca compatível com o navegador. Determinadas bibliotecas Node.js devem ter simulações de navegador. A seguir, um exemplo de um arquivo de configuração webpack que tem as configurações de simulação necessárias.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
A seguir, é apresentado um [código de exemplo](https://github.com/christopheranderson/cosmos-browser-sample) que usa TypeScript e Webpack com a biblioteca do SDK do JavaScript do Azure Cosmos DB para compilar um aplicativo Todo que envia atualizações em tempo real quando novos itens são criados.
Como melhor prática, não use a chave primária para comunicação com Azure Cosmos DB no navegador. Em vez disso, use tokens de recursos para comunicar-se. Para obter mais informações sobre tokens de recursos, consulte o artigo [Proteger o acesso ao Azure Cosmos DB](secure-access-to-data.md#resource-tokens).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outras formas de proteger a conta do Azure Cosmos, consulte os artigos a seguir:

* Artigo [Configurar um firewall para Azure Cosmos DB](how-to-configure-firewall.md).

* [Configurar rede virtual e sub-rede com base em acesso para sua conta do Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
    

