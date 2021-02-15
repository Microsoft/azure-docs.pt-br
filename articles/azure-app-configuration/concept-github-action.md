---
title: Sincronizar o repositório GitHub com a configuração do aplicativo
description: Use GitHub Actions para atualizar automaticamente sua instância de configuração de aplicativo, quando você atualizar o repositório GitHub.
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 1c290032f7a33079b560d3c4cc1fcb9526e70331
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762147"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Sincronizar o repositório GitHub com a configuração do aplicativo

As equipes que desejam continuar usando suas práticas de controle do código-fonte existentes podem usar GitHub Actions para sincronizar automaticamente seu repositório GitHub com o repositório de configuração do aplicativo. Isso permite que você faça alterações nos arquivos de configuração como faria normalmente, ao mesmo tempo que obtém os benefícios da configuração do aplicativo, como: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Configuração centralizada fora do seu código <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Atualizando a configuração sem reimplantar seu aplicativo inteiro <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integração com serviços como o Funções e Serviço de Aplicativo do Azure. 

Um [fluxo de trabalho](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions#the-components-of-github-actions) de GitHub Actions define um processo automatizado em um repositório GitHub. A ação *Sincronização de Configuração de Aplicativos do Azure* dispara atualizações para uma instância de configuração de aplicativo, quando são feitas alterações no repositório de origem. Ele usa um arquivo YAML (.yml) encontrado no caminho `/.github/workflows/` do seu repositório, para definir as etapas e os parâmetros. Você pode disparar atualizações de configuração ao enviar, revisar ou ramificar arquivos de configuração de aplicativo exatamente como faz com o código do aplicativo.

A [documentação](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions) do GitHub fornece uma visão detalhada dos fluxos de trabalho e ações do GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Habilitar GitHub Actions em seu repositório
Para começar a usar essa ação do GitHub, vá para o repositório e selecione a guia **ações** . Selecione **novo fluxo de trabalho** e, em seguida, **Configure um fluxo de trabalho por conta própria**. Por fim, pesquise "Sincronização de configuração de Aplicativos do Azure" no Marketplace.
> [!div class="mx-imgBorder"]
> ![Selecione a guia Ação](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selecionar a ação de sincronização de configuração de aplicativo](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sincronizar arquivos de configuração após um envio por push
Essa ação sincroniza os arquivos de Configuração de Aplicativos do Azure, quando uma alteração é enviada por push para `appsettings.json`. Quando um desenvolvedor muda para `appsettings.json`, a ação de sincronização de configuração de aplicativo atualiza a instância de configuração de aplicativos com os novos valores.

A primeira seção desse fluxo de trabalho especifica que a ação é disparada *em* um *Push* que contém `appsettings.json` o Branch *principal* . A segunda seção lista os trabalhos executados quando a ação é disparada. A ação verifica os arquivos relevantes e atualiza a instância de configuração do aplicativo usando a cadeia de conexão armazenada como um segredo no repositório.  Para obter mais informações sobre como usar segredos no GitHub, consulte o [Artigo do GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) sobre como criar e usar segredos criptografados.

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-strict-sync"></a>Usar a sincronizar estrita
Por padrão, a ação do GitHub não habilita o modo estrito, o que significa que a sincronização adicionará somente valores-chave do arquivo de configuração à instância de configuração do aplicativo (nenhum par chave-valor será excluído). Habilitar o modo estrito significará que os pares chave-valor que não estão no arquivo de configuração são excluídos da instância de configuração do aplicativo, para que ele corresponda ao arquivo de configuração. Se você estiver sincronizando de várias fontes ou usando Azure Key Vault com a configuração de aplicativo, convém usar diferentes prefixos ou rótulos com sincronização estrita para evitar apagar as definições de configuração de outros arquivos (consulte os exemplos abaixo). 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```
## <a name="sync-multiple-files-in-one-action"></a>Sincronizar vários arquivos em uma ação 

Se sua configuração estiver em vários arquivos, você poderá usar o padrão abaixo para disparar uma sincronização quando o arquivo for modificado. Esse padrão usa a biblioteca glob https://www.npmjs.com/package/glob . Observe que, se o nome do arquivo de configuração contiver uma vírgula, você poderá usar uma barra invertida para escapar a vírgula. 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>Sincronizar por prefixo ou rótulo
A especificação de prefixos ou rótulos em sua ação de sincronização sincronizará apenas esse conjunto específico. Isso é importante ao usar a sincronização estrita com vários arquivos. Dependendo de como a configuração é configurada, um prefixo ou um rótulo pode ser associado a cada arquivo e, em seguida, cada prefixo ou rótulo pode ser sincronizado separadamente para que nada seja substituído. Normalmente, os prefixos são usados para diferentes aplicativos ou serviços e rótulos são usados para ambientes diferentes. 

Sincronizar por prefixo: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

Sincronizar por rótulo: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>Usar um rótulo dinâmico na sincronização
A ação a seguir insere um rótulo dinâmico em cada sincronização, garantindo que cada sincronização possa ser identificada exclusivamente e permitindo que as alterações de código sejam mapeadas para as alterações de configuração.

A primeira seção desse fluxo de trabalho especifica que a ação é disparada *em* um *Push* que contém `appsettings.json` o Branch *principal* . A segunda seção executa um trabalho que cria um rótulo exclusivo para a atualização de configuração com base no hash de confirmação. Em seguida, o trabalho atualiza a instância de configuração do aplicativo com os novos valores e o rótulo exclusivo para essa atualização.

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-azure-key-vault-with-github-action"></a>Usar o Azure Key Vault com ação do GitHub
Os desenvolvedores que usam Azure Key Vault com AppConfiguration devem usar dois arquivos separados, normalmente um appSettings.json e um secretreferences.json. O secretreferences.json conterá a url para o segredo do Key Vault.

{ "mySecret": "{\"uri\":\"https://myKeyVault.vault.azure.net/secrets/mySecret"}" }

A ação do GitHub pode então ser configurada para fazer uma sincronização estrita em appSettings.json, seguida por uma sincronização não estrita em secretreferences.json. O exemplo a seguir disparará uma sincronização quando o arquivo for atualizado:

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>Usar a profundidade máxima para limitar a ação do GitHub
O comportamento padrão para atributos JSON aninhados é mesclar o objeto inteiro.  O JSON abaixo define esse par chave-valor:

| Chave | Valor |
| --- | --- |
| Object:Inner:InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Se o objeto aninhado for destinado ao valor enviado por push para a instância de configuração, você poderá usar o valor de *depth* para interromper o nivelamento na profundidade apropriada. 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Dada uma profundidade de 2, o exemplo acima agora retorna o seguinte par chave-valor:

| Chave | Valor |
| --- | --- |
| Object:Inner | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Entender as entradas da ação
Os parâmetros de entrada especificam os dados usados pela ação durante o tempo de execução.  A tabela a seguir contém parâmetros de entrada aceitos pela sincronização de configuração de aplicativo e os valores esperados para cada um.  Para obter mais informações sobre entradas de ação para GitHub Actions, consulte a [documentação](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions#inputs) do GitHub.

> [!Note]
> As IDs de entrada não diferenciam maiúsculas de minúsculas.


| Nome de entrada | Obrigatório? | Valor |
|----|----|----|
| configurationFile | Sim | Caminho relativo para o arquivo de configuração no repositório.  Os padrões glob têm suporte e podem incluir vários arquivos. |
| format | Sim | Formato do arquivo de configuração.  Os formatos válidos são: JSON, YAML, propriedades. |
| connectionString | Sim | Cadeia de conexão para a instância de Configuração de Aplicativos. A cadeia de conexão deve ser armazenada como um segredo no repositório do GitHub e somente o nome do segredo deve ser usado no fluxo de trabalho. |
| separator | Sim | Separador usado ao mesclar o arquivo de configuração para pares chave-valor.  Os valores válidos são: . , ; : - _ __ / |
| prefixo | Não | Prefixo a ser adicionado ao início das chaves. |
| label | Não | Rótulo usado ao definir pares chave-valor. Se não for especificado, será usado um rótulo nulo. |
| strict | Não | Um valor booliano que determina se o modo estrito está habilitado. O valor padrão é false. |
| depth | Não | Profundidade máxima para mesclar o arquivo de configuração.  A profundidade deve ser um número positivo.  O padrão não terá nenhuma profundidade máxima. |
| marcas | Não | Especifica a marca definida em pares chave-valor.  O formato esperado é uma forma em cadeias de um objeto JSON da seguinte forma: {[propertyName:string]: string;} Cada propriedade nome-valor torna-se uma marca. |

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a ação de sincronização de configuração de aplicativo do GitHub e como ela pode ser usada para automatizar atualizações para sua instância de configuração de aplicativo. Para saber como a configuração de Aplicativos do Azure reage às alterações em pares chave-valor, prossiga para o próximo [artigo](./concept-app-configuration-event.md).
