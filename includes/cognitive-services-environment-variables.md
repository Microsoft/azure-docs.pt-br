---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841597"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Configurar uma variável de ambiente para autenticação

É necessário autenticar o acesso ao cognitivas serviços que utilizam aplicativos. Para autenticar, é recomendável criar uma variável de ambiente para armazenar as chaves para os seus recursos do Azure. 

Depois de ter sua chave, escrevê-lo a uma nova variável de ambiente no computador local executando o aplicativo. Para definir a variável de ambiente, abra uma janela de console e siga as instruções do seu sistema operacional. Substitua `your-key` com uma das chaves do recurso.

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    Depois de adicionar a variável de ambiente, talvez seja necessário reiniciar todos os programas em execução que precisarem ler a variável de ambiente, incluindo a janela do console. Por exemplo, se estiver usando o Visual Studio como seu editor, reinicie-o antes de executar o exemplo.

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela de console para que as alterações entrem em vigor.
    
* macOS
    
    Edite seu .bash_profile e adicione a variável de ambiente:
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela de console para que as alterações entrem em vigor.
