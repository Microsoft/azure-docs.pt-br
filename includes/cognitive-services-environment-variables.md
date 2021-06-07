---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 95bd83575809f6ecda716ff751b47b7bb499cae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85073377"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Configurar uma variável de ambiente para autenticação

Os aplicativos precisam autenticar o acesso aos Serviços Cognitivos que eles usam. Para autenticar, é recomendável criar uma variável de ambiente para armazenar as chaves para os Recursos do Azure. 

Depois que você tiver a sua chave, grave-a em uma nova variável de ambiente no computador local que executa o aplicativo. Para definir a variável de ambiente, abra uma janela de console e siga as instruções do seu sistema operacional. Substitua `your-key` por uma das chaves para o recurso.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Depois de adicionar a variável de ambiente, talvez seja necessário reiniciar todos os programas em execução que precisarem ler a variável de ambiente, incluindo a janela do console. Por exemplo, se estiver usando o Visual Studio como seu editor, reinicie-o antes de executar o exemplo.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela de console para que as alterações entrem em vigor.

#### <a name="macos"></a>[macOS](#tab/unix)

Edite seu .bash_profile e adicione a variável de ambiente:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Depois de adicionar a variável de ambiente, execute `source ~/.bash_profile` a partir da janela de console para que as alterações entrem em vigor.

***
