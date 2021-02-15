---
title: Executar o Azure Functions de um pacote
description: Faça com que o Azure Functions Runtime execute suas funções montando um arquivo de pacote de implantação que contém os arquivos de projeto do aplicativo de funções.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: b2d90cf78263b30b4315199cf1c543186a435f17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88639878"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Executar o Azure Functions de um arquivo de pacote

No Azure, você pode executar suas funções diretamente de um arquivo de pacote de implantação no aplicativo de funções. A outra opção é implantar os arquivos no diretório `d:\home\site\wwwroot` do aplicativo de funções.

Este artigo descreve os benefícios de executar suas funções de um pacote. Ele também mostra como habilitar essa funcionalidade em seu aplicativo de funções.

## <a name="benefits-of-running-from-a-package-file"></a>Benefícios da execução de um arquivo de pacote
  
Há vários benefícios na execução de um arquivo de pacote:

+ Reduz o risco de problemas de bloqueio de cópia de arquivo.
+ Pode ser implantado em um aplicativo de produção (com reinicialização).
+ Você pode ter certeza dos arquivos que estão em execução no seu aplicativo.
+ Melhora o desempenho das [implantações do Azure Resource Manager](functions-infrastructure-as-code.md).
+ Pode reduzir os tempos de inicialização a frio, particularmente para as funções de JavaScript com árvores de pacote npm grandes.

Para obter mais informações, consulte [este comunicado](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Habilitando as funções para execução de um pacote

Para habilitar seu aplicativo de funções para execução de um pacote, basta adicionar a configuração `WEBSITE_RUN_FROM_PACKAGE` às configurações do aplicativo de funções. A configuração `WEBSITE_RUN_FROM_PACKAGE` pode ter um dos seguintes valores:

| Valor  | Descrição  |
|---------|---------|
| **`1`**  | Recomendado para aplicativos de funções em execução no Windows. Execute de um arquivo de pacote na pasta `d:\home\data\SitePackages` do seu aplicativo de funções. Se não estiver [implantando com a implantação de zip](#integration-with-zip-deployment), essa opção exigirá que a pasta também tenha um arquivo chamado `packagename.txt` . Esse arquivo contém apenas o nome do arquivo de pacote na pasta, sem espaços em branco. |
|**`<URL>`**  | Localização de um arquivo de pacote específico que você deseja executar. Ao usar o armazenamento de Blobs, você deve usar um contêiner privado com uma [SAS (Assinatura de Acesso Compartilhado)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para habilitar o runtime do Functions para acessar o pacote. Você pode usar o [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar arquivos de pacote para sua conta de armazenamento de Blobs. Ao especificar uma URL, você também deve [sincronizar gatilhos](functions-deployment-technologies.md#trigger-syncing) depois de publicar um pacote atualizado. |

> [!CAUTION]
> Ao executar um aplicativo de funções no Windows, a opção de URL externa gera um pior desempenho de inicialização a frio. Ao implantar seu aplicativo de funções no Windows, você deve definir `WEBSITE_RUN_FROM_PACKAGE` como `1` e publicar com a implantação de zip.

O exemplo a seguir mostra um aplicativo de funções configurado para ser executado de um arquivo .zip hospedado no armazenamento de Blobs do Azure:

![Configuração de aplicativo WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> No momento, apenas arquivos de pacote .zip têm suporte.

## <a name="integration-with-zip-deployment"></a>Integração com a implantação do zip

[Implantação do zip][Zip deployment for Azure Functions] é um recurso do Serviço de Aplicativo do Azure que permite que você implante seu projeto de aplicativo de funções no diretório `wwwroot`. O projeto é empacotado como um arquivo de implantação .zip. As mesmas APIs podem ser usadas para implantar seu pacote na pasta `d:\home\data\SitePackages`. Com o valor de configuração de aplicativo `WEBSITE_RUN_FROM_PACKAGE` de `1`, as APIs de implantação zip copiam seu pacote para a pasta `d:\home\data\SitePackages` em vez de extrair os arquivos para `d:\home\site\wwwroot`. Ele também cria o arquivo `packagename.txt`. Após uma reinicialização, o pacote é montado `wwwroot` como um sistema de arquivos somente leitura. Para obter mais informações sobre a implantação do zip, consulte [Implantação de zip para o Azure Functions](deployment-zip-push.md).

> [!NOTE]
> Quando ocorre uma implantação, uma reinicialização do aplicativo de funções é disparada. Antes de uma reinicialização, todas as execuções de função existentes podem ser concluídas ou atingir o tempo limite. Para saber mais, confira [comportamentos de implantação](functions-deployment-technologies.md#deployment-behaviors).

## <a name="adding-the-website_run_from_package-setting"></a>Adicionando a configuração WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Solução de problemas

- Executar do pacote torna `wwwroot` -se somente leitura, portanto, você receberá um erro ao gravar arquivos nesse diretório.
- Não há suporte para formatos tar e gzip.
- Esse recurso não compõe o cache local.
- Para obter um desempenho de inicialização a frio aprimorado, use a opção de zip local ( `WEBSITE_RUN_FROM_PACKAGE` = 1).
- Executar do pacote é incompatível com a opção de personalização de implantação ( `SCM_DO_BUILD_DURING_DEPLOYMENT=true` ), a etapa de compilação será ignorada durante a implantação.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantação contínua para Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
