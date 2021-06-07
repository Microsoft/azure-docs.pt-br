---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: trbye
ms.openlocfilehash: 4f542970db5cf7015df4e0f276fccd8782d7beee
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105152748"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para Python. Se quiser apenas o nome do pacote para começar por conta própria, execute `pip install azure-cognitiveservices-speech`.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

- O pacote do SDK de Fala do Python está disponível para estes sistemas operacionais:
  - Windows: x64 e x86
  - Mac: macOS X versão 10.12 ou mais recente
  - Linux; confira a lista de [distribuições do Linux e arquiteturas de destino compatíveis](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

- No Windows, é necessário ter os [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) para a sua plataforma. Ao instalá-los pela primeira vez, pode ser necessária uma reinicialização.

- As plataformas Linux com suporte deverão ter determinadas bibliotecas instaladas (`libssl` para suporte do protocolo SSL e `libasound2` para um suporte consistente). Consulte a distribuição abaixo para verificar os comandos necessários para instalar as versões corretas dessas bibliotecas.

  - No Ubuntu/Debian, execute os seguintes comandos para instalar os pacotes necessários:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Se o libssl 1.0.0 não estiver disponível, instale o libssl 1.0.x (em que x é maior que 0) ou o libssl 1.1.

  - No RHEL/CentOS, execute os seguintes comandos para instalar os pacotes necessários:

    ```sh
    sudo yum update
    sudo yum install alsa-lib openssl python3
    ```

> [!NOTE]
> - No RHEL/CentOS 7, siga as instruções sobre [como configurar o RHEL/CentOS 7 para o SDK de Fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - No RHEL/CentOS 8, siga as instruções em [como configurar o OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- No Windows, é necessário ter os [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. Observe que, ao instalá-los pela primeira vez, pode ser necessário reiniciar o Windows antes de continuar com este guia.
- E, por fim, você precisará do [Python 3.6 ao 3.9](https://www.python.org/downloads/). Para verificar a instalação, abra um prompt de comando, digite o comando `python --version` e verifique o resultado. Se ele estiver instalado corretamente, você receberá uma resposta semelhante a "Python 3.8.8".

## <a name="install-the-speech-sdk-from-pypi"></a>Instalar o SDK de Fala do PyPI

Se estiver usando seu próprio ambiente ou ferramentas de build, execute o seguinte comando para instalar o SDK de Fala do [PyPI](https://pypi.org/). Usuários do Visual Studio Code podem ir diretamente para a próxima subseção para ver uma instalação guiada.

```sh
pip install azure-cognitiveservices-speech
```

Se estiver no macOS, talvez seja necessário executar o seguinte comando para fazer o comando `pip` acima funcionar:

```sh
python3 -m pip install --upgrade pip
```

Depois de usar o `pip` com êxito para instalar o `azure-cognitiveservices-speech`, você pode usar o SDK de Fala importando o namespace nos seus projetos do Python.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instalar o SDK de Fala usando o Visual Studio Code

1. Baixe e instale a última versão com suporte do [Python](https://www.python.org/downloads/) para sua plataforma, da 3.6 à 3.9.
   - Os usuários do Windows precisam selecionar "Adicionar Python ao PATH" durante o processo de instalação.
1. Baixe e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra o Visual Studio Code e instale a extensão do Python. Selecione **Arquivo** > **Preferências** > **Extensões** no menu. Procure **Python**  e clique em **Instalar**.

   ![Instalar a extensão do Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Além disso, é possível instalar o pacote Python do SDK de Fala de dentro do Visual Studio Code usando a linha de comando integrada:
   1. Abra um terminal (nos menus suspensos, **Terminal** > **Novo Terminal**)
   1. No terminal que abre, insira o comando `python -m pip install azure-cognitiveservices-speech`

Se você é novo no Visual Studio Code, consulte a [Documentação mais completa do Visual Studio Code](https://code.visualstudio.com/docs). Para obter mais informações sobre o Visual Studio Code e Python, consulte o [Tutorial do Visual Studio Code para Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Python do SDK de Fala são distribuídas por meio do PyPI e anunciadas no [Notas sobre a versão](~/articles/cognitive-services/speech-service/releasenotes.md).
Se uma nova versão estiver disponível, você poderá atualizar para ela com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Verifique qual versão está instalada no momento inspecionando a variável `azure.cognitiveservices.speech.__version__`.

Se você tiver um problema ou se estiver faltando um recurso, confira [Support and help options](../../../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) (Opções de Ajuda e Suporte).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]