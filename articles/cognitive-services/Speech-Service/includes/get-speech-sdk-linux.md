---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: d0298bcd675b1b94999dab3a1ad1c40a6feb7438
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135617"
---
:::row:::
    :::column span="3":::
        O SDK de fala só dá suporte ao **Ubuntu 16.04/18.04/20.04** , **Debian 9/10** , **Red Hat Enterprise Linux (RHEL) 7/8** e **CentOS 7/8** nas seguintes arquiteturas de destino quando usado com o Linux:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) e ARM64 (Debian/Ubuntu) para desenvolvimento em C++
- x64, ARM32 (Debian/Ubuntu) e ARM64 (Debian/Ubuntu) para Java
- x64, ARM32 (Debian/Ubuntu) e ARM64 (Debian/Ubuntu) para .NET Core
- x64 para Python

> [!IMPORTANT]
> Para o C# no Linux ARM64, é necessário ter o pacote do .NET Core 3. x (dotNet-SDK-3. x).

### <a name="system-requirements"></a>Requisitos do sistema

Para um aplicativo nativo, o SDK de fala depende do `libMicrosoft.CognitiveServices.Speech.core.so` . Verifique se a arquitetura de destino (x86, x64) corresponde ao aplicativo. Dependendo da versão do Linux, podem ser necessárias dependências adicionais.

- As bibliotecas compartilhadas da biblioteca GNU C (incluindo a biblioteca de programação de Threads POSIX, `libpthreads`)
- A biblioteca OpenSSL ( `libssl.so.1.0.0` ou `libssl.so.1.0.2` )
- A biblioteca compartilhada para aplicativos ALSA (`libasound.so.2`)

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

> [!NOTE]
> Se o libssl 1.0. x não estiver disponível, instale o libssl 1.1 em vez disso.

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

> [!NOTE]
> Se o libssl 1.0. x não estiver disponível, instale o libssl 1.1 em vez disso.

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 e CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - No RHEL/CentOS 7, siga as instruções sobre [como configurar o RHEL/CentOS 7 para o SDK de Fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - No RHEL/CentOS 8, siga as instruções em [como configurar o OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
