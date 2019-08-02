---
title: Executar o Azure Functions de um pacote | Microsoft Docs
description: Faça com que o Azure Functions Runtime execute suas funções montando um arquivo de pacote de implantação que contém os arquivos de projeto do aplicativo de funções.
services: functions
documentationcenter: na
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: 96dbe9b15831a349afc0e68c15c39c1cb31b1032
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444061"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Executar o Azure Functions de um arquivo de pacote

No Azure, você pode executar suas funções diretamente de um arquivo de pacote de implantação no aplicativo de funções. A outra opção é implantar os arquivos no diretório `d:\home\site\wwwroot` do aplicativo de funções.

Este artigo descreve os benefícios de executar suas funções de um pacote. Ele também mostra como habilitar essa funcionalidade em seu aplicativo de funções.

> [!IMPORTANT]
> Ao implantar suas funções em um aplicativo de funções do Linux em um [plano Premium](functions-scale.md#premium-plan), você deve sempre executar a partir do arquivo de pacote e [publicar seu aplicativo usando o Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Benefícios da execução de um arquivo de pacote
  
Há vários benefícios na execução de um arquivo de pacote:

+ Reduz o risco de problemas de bloqueio de cópia de arquivo.
+ Pode ser implantado em um aplicativo de produção (com reinicialização).
+ Você pode ter certeza dos arquivos que estão em execução no seu aplicativo.
+ Melhora o desempenho das [implantações do Azure Resource Manager](functions-infrastructure-as-code.md).
+ Pode reduzir os tempos de inicialização a frio, particularmente para as funções de JavaScript com árvores de pacote npm grandes.

Para saber mais, veja [este comunicado](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Habilitando as funções para execução de um pacote

Para habilitar seu aplicativo de funções para execução de um pacote, basta adicionar a configuração `WEBSITE_RUN_FROM_PACKAGE` às configurações do aplicativo de funções. A configuração `WEBSITE_RUN_FROM_PACKAGE` pode ter um dos seguintes valores:

| Valor  | Descrição  |
|---------|---------|
| **`1`**  | Recomendado para aplicativos de funções em execução no Windows. Execute de um arquivo de pacote na pasta `d:\home\data\SitePackages` do seu aplicativo de funções. Se não estiver implantando [com a implantação de zip](#integration-with-zip-deployment), essa opção exigirá que a pasta `packagename.txt`também tenha um arquivo chamado. Esse arquivo contém apenas o nome do arquivo de pacote na pasta, sem espaços em branco. |
|**`<url>`**  | Localização de um arquivo de pacote específico que você deseja executar. Ao usar o armazenamento de Blobs, você deve usar um contêiner privado com uma [SAS (Assinatura de Acesso Compartilhado)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para habilitar o tempo de execução do Functions para acessar o pacote. Você pode usar o [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar arquivos de pacote para sua conta de armazenamento de Blobs.         |

> [!CAUTION]
> Ao executar um aplicativo de funções no Windows, a opção de URL externa gera um pior desempenho de inicialização a frio. Ao implantar seu aplicativo de funções no Windows, você deve definir `WEBSITE_RUN_FROM_PACKAGE` como `1` e publicar com a implantação de zip.

O exemplo a seguir mostra um aplicativo de funções configurado para ser executado de um arquivo .zip hospedado no armazenamento de Blobs do Azure:

![Configuração de aplicativo WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> No momento, apenas arquivos de pacote .zip têm suporte.

## <a name="integration-with-zip-deployment"></a>Integração com a implantação do zip

A [implantação de zip][Zip deployment for Azure Functions] é um recurso do serviço de Azure app que permite que você implante seu projeto `wwwroot` de aplicativo de funções no diretório. O projeto é empacotado como um arquivo de implantação .zip. As mesmas APIs podem ser usadas para implantar seu pacote na pasta `d:\home\data\SitePackages`. Com o valor de configuração de aplicativo `WEBSITE_RUN_FROM_PACKAGE` de `1`, as APIs de implantação zip copiam seu pacote para a pasta `d:\home\data\SitePackages` em vez de extrair os arquivos para `d:\home\site\wwwroot`. Ele também cria o arquivo `packagename.txt`. O aplicativo de funções é executado do pacote após a reinicialização e `wwwroot` se torna somente leitura. Para obter mais informações sobre a implantação do zip, consulte [Implantação de zip para o Azure Functions](deployment-zip-push.md).

## <a name="adding-the-websiterunfrompackage-setting"></a>Adicionando a configuração WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Solução de problemas

- Executar do pacote torna `wwwroot` -se somente leitura, portanto, você receberá um erro ao gravar arquivos nesse diretório.
- Não há suporte para formatos tar e gzip.
- Esse recurso não compõe o cache local.
- Para obter um desempenho de inicialização a frio aprimorado, use a`WEBSITE_RUN_FROM_PACKAGE`opção de zip local (= 1).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantação contínua para Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
