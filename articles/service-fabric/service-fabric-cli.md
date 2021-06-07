---
title: Introdução à CLI do Azure Service Fabric
description: Saiba como usar a CLI do Azure Service Fabric. Saiba como se conectar a um cluster e como gerenciar aplicativos.
author: jeffj6123
ms.topic: conceptual
ms.date: 5/19/2020
ms.author: jejarry
ms.openlocfilehash: ff322cb3633616e9004bc8fa25b048b6f1263008
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046327"
---
# <a name="azure-service-fabric-cli"></a>CLI do Azure Service Fabric

A interface de linha de comando do Azure Service Fabric (CLI) é um utilitário de linha de comando para interagir com e gerenciar entidades do Service Fabric. A CLI do Service Fabric pode ser usada com clusters do Windows ou Linux. A CLI do Service Fabric é executada em qualquer plataforma onde Python tem suporte.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes da instalação, verifique se seu ambiente tem o Python e o pip instalados. Para saber mais, veja a [documentação de início rápido do pip](https://pip.pypa.io/en/latest/quickstart/) e a [documentação de instalação do Python](https://wiki.python.org/moin/BeginnersGuide/Download) oficial.

A CLI dá suporte a versões do Python 2,7 e 3.6 +, com o Python 3. x recomendado.

### <a name="service-fabric-target-runtime"></a>runtime de destino do Service Fabric

A CLI do Service Fabric serve para dar suporte à versão de runtime mais recente do SDK do Service Fabric. Use a tabela a seguir para determinar qual versão da CLI instalar:

| Versão da CLI   | versão do runtime com suporte |
|---------------|---------------------------|
| Mais recente (~=10) | Mais recente (~=7.1)            |
| 9.0.0         | 7.1                       |
| 8.0.0         | 6.5                       |
| 7.1.0         | 6.4                       |
| 6.0.0         | 6.3                       |
| 5.0.0         | 6.2                       |
| 4.0.0         | 6.1                       |
| 3.0.0         | 6,0                       |
| 1.1.0         | 5.6, 5.7                  |

Opcionalmente, você pode especificar uma versão de destino da CLI para instalação acrescentando ao comando `pip install` o sufixo `==<version>`. Por exemplo, para a versão 1.1.0, a sintaxe seria:

```shell
pip install -I sfctl==1.1.0
```

Substitua o seguinte comando `pip install` pelo comando mencionado anteriormente, quando for necessário.

Para saber mais sobre as versões da CLI do Service Fabric, veja a [documentação do GitHub](https://github.com/Azure/service-fabric-cli/releases).

## <a name="install-pip-python-and-the-service-fabric-cli"></a>Instale o pip, Python e a CLI do Service Fabric

Há várias maneiras de instalar o pip e o Python em sua plataforma. Aqui estão algumas etapas para obter os principais sistemas operacionais configurar rapidamente com Python 3 e pip.

### <a name="windows"></a>Windows

Para o Windows 10, o Windows Server 2016 e o Windows Server 2012 R2, use as instruções de instalação oficial padrão. O instalador do Python também instala o pip por padrão.

1. Acesse a [página de downloads](https://www.python.org/downloads/)oficial do Python e baixe a versão mais recente do Python 3. x.

2. Inicie o instalador.

3. Na parte inferior do prompt, selecione **Adicionar Python 3. x ao caminho**.

4. Selecione **Instalar Agora** e conclua a instalação.

Agora você deve poder abrir uma nova janela de comando e obter a versão do Python e do pip.

```shell
python --version
pip --version
```

Em seguida, execute o seguinte comando para instalar a CLI de malha do serviço do Azure (sfctl) e exibir a página de Ajuda do CLI:

```shell
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
echo "export PATH=$PATH:~/.local/bin" >> .shellrc
```

Se a instalação no subsistema do Windows para Linux falhar com permissões de pasta incorretas, talvez seja necessário tentar novamente com permissões elevadas:

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (suporte à versão prévia do Service Fabric)

Para instalar a CLI do Service Fabric no Red Hat, execute os seguintes comandos:

```bash
sudo yum install -y python38
sudo yum install python38-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

Para testar a instalação, você pode consultar as etapas mencionadas em **subsistema Ubuntu e do Windows para Linux** seção

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

Para MacOS, recomendamos que você use o [Gerenciador de pacotes do HomeBrew](https://brew.sh). Se o HomeBrew ainda não estiver instalado, instale-o ao executar o seguinte comando:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Em seguida, a partir do terminal, instale o último Python 3. x, Pip e a CLI do Service Fabric executando os seguintes comandos:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>Sintaxe da CLI

Os comandos sempre são prefixados com `sfctl`. Para obter informações gerais sobre todos os comandos que você pode usar, utilize `sfctl -h`. Para obter ajuda com um único comando, use `sfctl <command> -h`.

Os comandos seguem uma estrutura repetida, com o destino do comando antes do verbo ou da ação.

```shell
sfctl <object> <action>
```

Neste exemplo, `<object>` é o destino para `<action>`.

## <a name="select-a-cluster"></a>Selecionar um cluster

Antes de executar qualquer operação, você deve selecionar um cluster para se conectar. Por exemplo, para selecionar e conectar o cluster com o nome `testcluster.com`, execute o seguinte comando:

> [!WARNING]
> Não use clusters do Service Fabric não seguros em ambientes de produção.

```shell
sfctl cluster select --endpoint http://testcluster.com:19080
```

O ponto de extremidade do cluster deve ser antecedido por `http` ou `https`. Ele deve incluir a porta para o gateway HTTP. Essa porta e o endereço são os mesmos da URL do Service Fabric Explorer.

Para clusters protegidos com um certificado, você pode especificar um certificado PEM codificado. O certificado pode ser especificado como um único arquivo ou como um par de certificado e chave. Se for um certificado autoassinado que não é assinado pela autoridade de certificação, você pode passar a `--no-verify` opção para ignorar a verificação de autoridade de certificação.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Para saber mais, confira [Conectar-se a um cluster seguro do Azure Service Fabric](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Operações básicas

As informações de conexão do cluster persistem por várias sessões da CLI do Service Fabric. Depois de selecionar um cluster do Service Fabric, você poderá executar todos os comandos do Service Fabric no cluster.

Por exemplo, para obter o estado de integridade do cluster do Service Fabric, use o seguinte comando:

```shell
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

```shell
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Da mesma forma, para converter um arquivo PEM para um arquivo PFX, você pode usar o seguinte comando (nenhuma senha está sendo fornecida aqui):

```shell
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

Para saber mais, confira a [documentação OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Problemas de conexão

Algumas operações podem gerar a seguinte mensagem:

`Failed to establish a new connection`

Verifique se o ponto de extremidade do cluster especificado está disponível e está ouvindo. Verifique também se a interface do usuário do Service Fabric Explorer está acessível no host e na porta. Para atualizar o ponto de extremidade, use `sfctl cluster select`.

### <a name="detailed-logs"></a>Logs detalhados

Os logs detalhados costumam ser úteis para depurar ou relatar um problema. O `--debug` sinalizador aumenta o nível de detalhes da saída.

### <a name="command-help-and-syntax"></a>Sintaxe e ajuda de comando

Para obter ajuda com um comando ou um grupo de comandos específico, use o sinalizador `-h`.

```shell
sfctl application -h
```

Veja outro exemplo:

```shell
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Atualizar a CLI do Service Fabric 

Para atualizar a CLI do Service Fabric, execute os seguintes comandos (substitua `pip` com `pip3` dependendo do escolhido durante a instalação original):

```shell
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo com a CLI do Azure Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md)
