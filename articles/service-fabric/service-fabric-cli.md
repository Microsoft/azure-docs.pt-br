---
title: "Introdução à CLI do Azure Service Fabric"
description: Saiba como usar a CLI do Azure Service Fabric. Saiba como se conectar a um cluster e como gerenciar aplicativos.
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.openlocfilehash: a938e300b1510a4f5f4eac3bd3d9a8bb728241ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-cli"></a>CLI do Azure Service Fabric

A interface de linha de comando do Azure Service Fabric (CLI) é um utilitário de linha de comando para interagir com e gerenciar entidades do Service Fabric. A CLI do Service Fabric pode ser usada com clusters do Windows ou Linux. A CLI do Service Fabric é executada em qualquer plataforma onde Python tem suporte.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes da instalação, verifique se seu ambiente tem o Python e o pip instalados. Para saber mais, veja a [documentação de início rápido do pip](https://pip.pypa.io/en/latest/quickstart/) e a [documentação de instalação do Python](https://wiki.python.org/moin/BeginnersGuide/Download) oficial.

A CLI dá suporte às versões 2.7, 3.5 e 3.6 do Python. Python 3.6 é a versão recomendada, pois o suporte ao Python 2.7 chegará ao fim em breve.

### <a name="service-fabric-target-runtime"></a>Tempo de execução de destino do Service Fabric

A CLI do Service Fabric serve para dar suporte à versão de tempo de execução mais recente do SDK do Service Fabric. Use a tabela a seguir para determinar qual versão da CLI instalar:

| Versão da CLI   | versão do tempo de execução com suporte |
|---------------|---------------------------|
| Mais recente (~=2)  | Mais recente (~=6.0)            |
| 1.1.0         | 5.6, 5.7                  |

Opcionalmente, você pode especificar uma versão de destino da CLI para instalação acrescentando ao comando `pip install` o sufixo `==<version>`. Por exemplo, para a versão 1.1.0, a sintaxe seria:

```
pip install -I sfctl==1.1.0
```

Substitua o seguinte comando `pip install` pelo comando mencionado anteriormente, quando for necessário.

## <a name="install-pip-python-and-the-service-fabric-cli"></a>Instale o pip, Python e a CLI do Service Fabric

Há várias maneiras de instalar o pip e o Python em sua plataforma. Aqui estão algumas etapas para obter os principais sistemas operacionais configurar rapidamente com Python 3 e pip.

### <a name="windows"></a>Windows

Para o Windows 10, o Windows Server 2016 e o Windows Server 2012 R2, use as instruções de instalação oficial padrão. O instalador do Python também instala o pip por padrão.

1. Vá até a [página oficial de downloads do Python](https://www.python.org/downloads/) e baixe a versão mais recente do Python 3.6.

2. Inicie o instalador.

3. Na parte inferior do aviso, selecione **Adicionar Python 3.6 a PATH**.

4. Selecione **Instalar Agora** e conclua a instalação.

Agora você deve poder abrir uma nova janela de comando e obter a versão do Python e do pip.

```bat
python --version
pip --version
```

Em seguida, execute o comando a seguir para instalar a CLI do Service Fabric:

```bat
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Ubuntu e Subsistema do Windows para Linux

Para instalar a CLI do Service Fabric, execute os seguintes comandos:

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

Em seguida, você pode testar a instalação com:

```bash
sfctl -h
```

Se você receber um erro de comando não encontrado, como:

`sfctl: command not found`

Certifique-se de que `~/.local/bin` possa ser acessado a partir de `$PATH`:

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .bashrc
```

Se a instalação no subsistema do Windows para Linux falhar com permissões de pasta incorretas, talvez seja necessário tentar novamente com permissões elevadas:

```bash
sudo pip3 install sfctl
```

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

Para MacOS, recomendamos que você use o [Gerenciador de pacotes do HomeBrew](https://brew.sh). Se o HomeBrew ainda não estiver instalado, instale-o ao executar o seguinte comando:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

No terminal, instale 3.6 Python, pip e a CLI do Service Fabric executando os seguintes comandos:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>Sintaxe da CLI

Os comandos sempre são prefixados com `sfctl`. Para obter informações gerais sobre todos os comandos que você pode usar, utilize `sfctl -h`. Para obter ajuda com um único comando, use `sfctl <command> -h`.

Os comandos seguem uma estrutura repetida, com o destino do comando antes do verbo ou da ação.

```azurecli
sfctl <object> <action>
```

Neste exemplo, `<object>` é o destino para `<action>`.

## <a name="select-a-cluster"></a>Selecionar um cluster

Antes de executar qualquer operação, você deve selecionar um cluster para se conectar. Por exemplo, para selecionar e conectar o cluster com o nome `testcluster.com`, execute o seguinte comando:

> [!WARNING]
> Não use clusters do Service Fabric não seguros em ambientes de produção.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

O ponto de extremidade do cluster deve ser antecedido por `http` ou `https`. Ele deve incluir a porta para o gateway HTTP. Essa porta e o endereço são os mesmos da URL do Service Fabric Explorer.

Para clusters protegidos com um certificado, você pode especificar um certificado PEM codificado. O certificado pode ser especificado como um único arquivo ou como um par de certificado e chave. Se for um certificado autoassinado que não é assinado pela autoridade de certificação, você pode passar a `--no-verify` opção para ignorar a verificação de autoridade de certificação.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Para saber mais, confira [Conectar-se a um cluster seguro do Azure Service Fabric](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Operações básicas

As informações de conexão do cluster persistem por várias sessões da CLI do Service Fabric. Depois de selecionar um cluster do Service Fabric, você poderá executar todos os comandos do Service Fabric no cluster.

Por exemplo, para obter o estado de integridade do cluster do Service Fabric, use o seguinte comando:

```azurecli
sfctl cluster health
```

O comando resulta na saída a seguir:

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Dicas e solução de problemas

Veja algumas sugestões e dicas para resolver problemas comuns.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Converter um certificado de PFX para PEM

A CLI do Service Fabric dá suporte aos certificados do cliente como arquivos PEM (extensão .pem). Se você usa arquivos PFX do Windows, deve converter esses certificados em formato PEM. Para converter um arquivo PFX em PEM, use o seguinte comando:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Da mesma forma, para converter um arquivo PEM para um arquivo PFX, você pode usar o seguinte comando (nenhuma senha está sendo fornecida aqui):

```bash
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

Para saber mais, confira a [documentação OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Problemas de conexão

Algumas operações podem gerar a seguinte mensagem:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Verifique se o ponto de extremidade do cluster especificado está disponível e está ouvindo. Verifique também se a interface do usuário do Service Fabric Explorer está acessível no host e na porta. Para atualizar o ponto de extremidade, use `sfctl cluster select`.

### <a name="detailed-logs"></a>Logs detalhados

Os logs detalhados costumam ser úteis para depurar ou relatar um problema. Um sinalizador `--debug` global que aumenta o detalhamento dos arquivos de log.

### <a name="command-help-and-syntax"></a>Sintaxe e ajuda de comando

Para obter ajuda com um comando ou um grupo de comandos específico, use o sinalizador `-h`.

```azurecli
sfctl application -h
```

Veja outro exemplo:

```azurecli
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Atualizar a CLI do Service Fabric 

Para atualizar a CLI do Service Fabric, execute os seguintes comandos (substitua `pip` com `pip3` dependendo do escolhido durante a instalação original):

```bash
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo com a CLI do Azure Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md)
