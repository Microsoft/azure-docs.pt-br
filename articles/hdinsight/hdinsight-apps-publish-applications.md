---
title: Publicar aplicativos Azure HDInsight
description: Saiba como criar um aplicativo HDInsight e, em seguida, publicá-lo no Azure Marketplace.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 05/14/2018
ms.openlocfilehash: d432d20660cd1b7ee59c9ef72f9db04bc0009481
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306578"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publicar um aplicativo HDInsight no Azure Marketplace
É possível instalar um aplicativo Azure HDInsight em um cluster HDInsight baseado em Linux. Neste artigo, aprenda como publicar um aplicativo HDInsight no Azure Marketplace. Para obter informações gerais sobre a publicação no Azure Marketplace, consulte [Publish an offer in the Azure Marketplace](../marketplace/overview.md) (Publicar uma oferta no Azure Marketplace).

Os aplicativos HDInsight usam o modelo *BYOL (Traga sua própria licença)*. Em um cenário BYOL, um provedor de aplicativo é responsável por licenciar o aplicativo para usuários do aplicativo. Os usuários do aplicativo serão cobrados apenas para os recursos do Azure que eles criarem, como o cluster HDInsight e as VMs e nós do cluster. No momento, a cobrança do aplicativo propriamente não ocorre no Azure.

Para obter mais informações, consulte estes artigos relacionados ao aplicativo HDInsight:

* [Instalar aplicativos HDInsight](hdinsight-apps-install-applications.md). Saiba como instalar um aplicativo HDInsight em seus clusters.
* [Instalar aplicativos HDInsight personalizados](hdinsight-apps-install-custom-applications.md). Saiba como instalar e testar aplicativos HDInsight personalizados.

## <a name="prerequisites"></a>Pré-requisitos
Para enviar seu aplicativo personalizado no Marketplace, primeiro [crie e teste seu aplicativo personalizado](hdinsight-apps-install-custom-applications.md).

Também é necessário registrar sua conta de desenvolvedor. Para obter mais informações, consulte [Publish an offer in the Azure Marketplace](../marketplace/overview.md) (Publicar uma oferta no Azure Marketplace) e [Create a Microsoft Developer account](../marketplace/overview.md) (Criar uma conta de Desenvolvedor Microsoft).

## <a name="define-the-application"></a>Definir o aplicativo
Duas etapas são envolvidas na publicação de aplicativos no Marketplace. Primeiro, defina um arquivo *createUiDef.json*. O arquivo createUiDef.json indica com quais clusters seu aplicativo é compatível. Em seguida, publique o modelo no Portal do Azure. Veja um exemplo do arquivo createUiDef.json:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Campo | Descrição | Valores possíveis |
| --- | --- | --- |
| tipos |Os tipos de cluster com os quais o aplicativo é compatível. |Hadoop, HBase, Storm, Spark (ou qualquer combinação deles) |
| versões |Os tipos de cluster HDInsight com os quais o aplicativo é compatível. |3.4 |

## <a name="application-installation-script"></a>Script de instalação do aplicativo
Quando um aplicativo é instalado em um cluster (em um cluster existente ou em um novo), um nó de borda é criado. O script de instalação do aplicativo é executado no nó de borda.

  > [!IMPORTANT]  
  > O nome do script de instalação do aplicativo deve ser único para um cluster específico. O nome do script deve ter o seguinte formato:
  > 
  > "name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > O nome do script tem três partes:
  > 
  > * Um prefixo de nome de script, que deverá incluir o nome do aplicativo ou um nome relevante para o aplicativo.
  > * Um hífen, para facilitar a leitura.
  > * Uma função de cadeia de caracteres exclusiva, com o nome do aplicativo como o parâmetro.
  > 
  > Na lista de ação do script persistente, o exemplo anterior é exibido como **hue-install-v0-4wkahss55hlas**. Consulte um [exemplo de conteúdo JSON](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

O script de instalação deve ter as seguintes características:
* O script é idempotente. Várias chamadas para o script produzem o mesmo resultado.
* O script tem controle de versão adequado. Use um local diferente para o script quando você estiver atualizando ou testando alterações. Isso garante que os clientes que estão instalando o aplicativo não sejam afetados por suas atualizações ou testes. 
* O script tem um registro adequado em cada ponto. Normalmente, os logs de script são a única maneira de depurar os problemas de instalação do aplicativo.
* As chamadas para serviços ou recursos externos têm repetições adequadas, para que a instalação não seja afetada por problemas temporários de rede.
* Se seu script iniciar serviços nos nós, os serviços serão monitorados e configurados para iniciar automaticamente se ocorrer a reinicialização de um nó.

## <a name="package-the-application"></a>Empacotar o aplicativo
Crie um arquivo .zip que contém todos os arquivos necessários para instalar seu aplicativo HDInsight. Use o arquivo .zip para publicar o aplicativo. O arquivo .zip inclui os seguintes arquivos:

* createUiDefinition.json
* mainTemplate.json (Para ver um exemplo, consulte [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).)
* Todos os scripts obrigatórios

> [!NOTE]  
> É possível hospedar os arquivos de aplicativo (incluindo arquivos do aplicativo Web) em qualquer ponto de extremidade publicamente acessível.

## <a name="publish-the-application"></a>Publicar o aplicativo
Para publicar um aplicativo do HDInsight:

1. Entre no Azure Publishing.

2. No menu à esquerda, selecione **Modelos de solução**.
3. Insira um título e selecione **Criar um novo modelo de solução**.
4. Se você ainda não registrou sua organização, selecione **Criar conta do Centro de Desenvolvimento e ingressar no programa do Azure**.  Para obter mais informações, consulte [Create a Microsoft Developer account](../marketplace/overview.md) (Criar uma conta de Desenvolvedor Microsoft).
5. Selecione **Definir algumas topologias para começar**. Um modelo de solução é um "pai" para todas as respectivas topologias. É possível definir várias topologias em uma oferta ou modelo de solução. Quando uma oferta é enviada por push para preparo, todas as respectivas topologias a acompanham. 
6. Insira um nome de topologia e selecione **+** .
7. Insira uma nova versão e, em seguida, selecione **+** .
8. Carregue o arquivo .zip criado quando você empacotou o aplicativo.  
9. Selecione **Solicitar certificação**. A equipe de certificação da Microsoft examina os arquivos e certifica a topologia.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [instalar aplicativos HDInsight](hdinsight-apps-install-applications.md) em seus clusters.
* Saiba como [instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md) e implantar um aplicativo do HDInsight não publicado no HDInsight.
* Saiba como [usar a ação de script para personalizar clusters HDInsight baseados em Linux](hdinsight-hadoop-customize-cluster-linux.md) e adicionar mais aplicativos. 
* Saiba como [criar clusters Apache Hadoop baseados em Linux no HDInsight usando modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Saiba como [usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md) para acessar clusters HDInsight, testar e hospedar aplicativos HDInsight.
