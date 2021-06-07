---
title: 'Guia de início rápido: Compilar o microagente do Defender com base no código-fonte (versão prévia)'
description: Neste guia de início rápido, saiba mais sobre o microagente, que inclui uma infraestrutura que pode ser usada para personalizar a distribuição.
ms.date: 1/18/2021
ms.topic: quickstart
ms.openlocfilehash: a3a8f82989d6abbaf2657e4b45638c77b3b2f704
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384590"
---
# <a name="quickstart-build-the-defender-micro-agent-from-source-code-preview"></a>Guia de início rápido: Compilar o microagente do Defender com base no código-fonte (versão prévia)

O microagente inclui uma infraestrutura, que pode ser usada para personalizar sua distribuição. Para ver uma lista dos parâmetros de configuração disponíveis, confira o arquivo `configs/LINUX_BASE.conf`.

Para uma distribuição individual, modifique o arquivo `.conf` base. 

Caso precise de mais de uma distribuição, herde da configuração base e substitua os valores. 

Para substituir os valores:

1. Crie um arquivo `.dist`.

1. Adicione `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` à parte superior.
 
1. Defina novos valores com o que for necessário, por exemplo: 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. Dê uma referência ao arquivo `.dist` durante o build. Por exemplo, 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="prerequisites"></a>Pré-requisitos

1. Entre em contato com seu gerente de conta para solicitar o acesso ao código-fonte do Defender para IoT.
 
1. Clone ou extraia o código-fonte para uma pasta no disco.

1. Procure esse diretório.

1. Efetue pull dos submódulos usando o seguinte código:

    ```bash
    git submodule update --init
    ```
    
1. Em seguida, efetue pull dos submódulos para o SDK do IoT do Azure com o seguinte código: 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. Adicione uma permissão de execução e execute o script de instalação do ambiente de desenvolvedor:

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. Crie um diretório para as saídas de build: 

    ```bash
    mkdir cmake 
    ```

1. (Opcional) Baixe e instale o [VS Code](https://code.visualstudio.com/download ) 

1. (Opcional) Instale a [extensão do C/C++](https://code.visualstudio.com/docs/languages/cpp ) para VS Code. – Nenhum

## <a name="baseline-configuration-signing"></a>Assinatura da configuração de linha de base 

Por padrão, o agente verifica a autenticidade dos arquivos de configuração que são colocados no disco para atenuar a adulteração.

Você pode interromper esse processo definindo o sinalizador de pré-processador `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE`.

Não recomendamos desativar a verificação de assinatura em ambientes de produção. 

Caso precise de outra configuração para cenários de produção, entre em contato com a equipe do Defender para IoT. 

## <a name="building-the-defender-iot-micro-agent"></a>Como criar o microagente do Defender para IoT 

1. Abra o diretório com o VS Code 

1. Navegue até o diretório `cmake`. 

1. Execute o comando a seguir: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    Por exemplo: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>Próximas etapas

[Configurar sua solução do Azure Defender para IoT](quickstart-configure-your-solution.md).
