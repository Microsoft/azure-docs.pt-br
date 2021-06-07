---
title: CORS (Compartilhamento de Recursos entre Origens) no Azure Cosmos DB
description: Este artigo descreve como configurar o CORS (Compartilhamento de Recursos entre Origens) no Azure Cosmos DB, usando os modelos do Azure Resource Manager e portal do Azure.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: eba49ff45ba9ab1f5cfaa1d75973d656ac32ca6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339881"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Configurar CORS (Compartilhamento de Recursos entre Origens)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O CORS (Compartilhamento de Recursos entre Origens) é um recurso HTTP que permite que um aplicativo Web em execução em um domínio acesse recursos em outro domínio. Os navegadores da Web implementam uma restrição de segurança, conhecida como política de mesma origem, que impede que uma página da Web chame APIs em um domínio diferente. No entanto, o CORS fornece uma maneira segura de permitir que o domínio de origem chame APIs em outro domínio. A API básica (SQL) no Azure Cosmos DB agora dá suporte ao compartilhamento de recursos entre origens (CORS) usando o cabeçalho "allowedOrigins". Após habilitar o suporte para CORS na conta do Azure Cosmos, somente as solicitações autenticadas serão avaliadas para determinar se são permitidas de acordo com as regras especificadas.

É possível definir a configuração de CORS (Compartilhamento de Recursos entre Origens) no portal do Azure ou em um modelo do Azure Resource Manager. Para contas do cosmos usando a API básica (SQL), o Azure Cosmos DB dá suporte a uma biblioteca JavaScript que funciona em ambientes de Node.js e baseados em navegador. Essa biblioteca agora pode aproveitar o suporte do CORS ao usar o modo Gateway. Não há configuração do lado do cliente necessária para usar esse recurso. Com o suporte de CORS, os recursos de um navegador podem acessar diretamente o Azure Cosmos DB por meio da [Biblioteca JavaScript](https://www.npmjs.com/package/@azure/cosmos) ou diretamente da [API REST](/rest/api/cosmos-db/) para operações simples.

> [!NOTE]
> O suporte a CORS só é aplicável e tem suporte para a API do Azure Cosmos DB Core (SQL). Ele não é aplicável às APIs de Azure Cosmos DB para Cassandra, Gremlin ou MongoDB, pois esses protocolos não usam HTTP para comunicação de cliente-servidor.

## <a name="enable-cors-support-from-azure-portal"></a>Habilitar o suporte de CORS do portal do Azure

Use as etapas a seguir para habilitar o Compartilhamento de Recursos entre Origens, usando o portal do Azure:

1. Navegue até a conta do Azure Cosmos DB. Abra a folha **CORS**.

2. Especifique uma lista de origens separadas por vírgulas que pode fazer chamadas entre origens para a conta do Azure Cosmos DB. Por exemplo, `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Também é possível usar um curinga "\*" para permitir todas as origens e selecione **Enviar**. 

   > [!NOTE]
   > Atualmente, não é possível usar curingas como parte do nome do domínio. Por exemplo, ainda não há suporte para o formato `https://*.mydomain.net`. 

   :::image type="content" source="./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png" alt-text="Habilitar o compartilhamento de recursos entre origens usando o portal do Azure":::

## <a name="enable-cors-support-from-resource-manager-template"></a>Habilitar o suporte para CORS a partir do modelo do Resource Manager

Para habilitar o CORS usando um modelo do Resource Manager, adicione a seção "cors" com a propriedade "allowedOrigins" a qualquer modelo existente. O JSON a seguir é um exemplo de um modelo que cria uma nova conta do Azure Cosmos com o CORS habilitado.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
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
