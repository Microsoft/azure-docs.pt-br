---
title: "Usando a extensão de máquina virtual Docker para Linux no Azure"
description: "Descreve as extensões de máquinas virtuais Docker e Azure Virtual, além de mostrar como criar máquinas virtuais que sejam hosts do Docker programaticamente no Azure, por meio da linha de comando, usando a CLI do Azure."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
ms.openlocfilehash: a542332c921862241f1f000e6a8f0a0ae0e8a934
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Usando a extensão VM Docker da interface de linha de comando do Azure (CLI do Azure)
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para obter informações sobre como usar a extensão de VM do Docker com o modelo do Resource Manager, veja [aqui](../dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Este tópico descreve como criar uma máquina virtual com a extensão de VM do Docker do modo de gerenciamento de serviço (asm) na CLI do Azure em qualquer plataforma. O [Docker](https://www.docker.com/) é uma das abordagens de virtualização mais populares que utiliza [contêineres Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como forma de isolar dados e computar recursos compartilhados. Você pode usar a extensão da VM do Docker e o [Agente Linux do Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para criar uma VM do Docker que hospede diversos contêineres para seus aplicativos no Azure. Para participar de um debate de alto nível sobre contêineres e suas vantagens, confira o [Quadro de comunicações de alto nível do Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Como usar a Extensão de VM Docker com o Azure
Para usar a extensão de VM do Docker com o Azure, você deve instalar a versão da [Interface de Linha de Comando do Azure](https://github.com/Azure/azure-sdk-tools-xplat) (CLI do Azure) superior à 0.8.6 (até o momento da redação deste artigo, a versão atual é 0.10.0). Você pode instalar a CLI do Azure no Mac, Linux e Windows.

O processo completo para usar o Docker no Azure é simples:

* Instale a CLI do Azure e suas dependências no computador a partir do qual você deseja controlar o Azure (no Windows, será uma distribuição Linux sendo executada como uma máquina virtual)
* Use os comandos do Docker da CLI do Azure para criar um host do Docker da VM no Azure
* Use os comandos do Docker local para gerenciar os contêineres do seu Docker na sua VM do Docker no Azure.

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Instalar a interface de linha de comando do Azure (CLI do Azure)
Para instalar e configurar a CLI do Azure, confira [Como instalar a Interface de Linha de Comando do Azure](../../../cli-install-nodejs.md). Para confirmar a instalação, digite `azure` no prompt de comando e, após um breve momento, você deverá ver a arte ASCII da CLI do Azure, que lista os comandos básicos disponíveis. Se a instalação for bem-sucedida, você deverá poder digitar `azure help vm` e ver que um dos comandos listados é “docker”.

> [!NOTE]
> O Docker tem ferramentas para Windows, o [Computador do Docker](https://docs.docker.com/installation/windows/), que também pode ser usado para automatizar a criação de um cliente docker que você pode usar para trabalhar com VMs do Azure como hosts do docker.
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Conectar a CLI do Azure à sua conta do Azure
Antes de poder usar a CLI do Azure, você deve associar as credenciais de conta do Azure com a CLI do Azure em sua plataforma. A seção [How to connect to your Azure subscription](../../../xplat-cli-connect.md) (Como se conectar à assinatura do Azure) explica como baixar e importar o arquivo **.publishsettings** ou associar a linha de comando da CLI do Azure a uma ID organizacional.

> [!NOTE]
> Há algumas diferenças no comportamento ao usar um ou os outros métodos de autenticação, portanto, não deixe de ler o documento acima para entender as diferentes funcionalidades.
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Instalar o Docker e usar a extensão de VM do Docker para Azure
Siga as [Instruções de instalação do Docker](https://docs.docker.com/installation/#installation) para instalar o Docker localmente em seu computador.

Para usar o Docker com uma máquina virtual do Azure, o [Agente de VM Linux do Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) deve estar instalado na imagem Linux usada para a VM. No momento, apenas dois tipos de imagens possibilitam isso:

* Uma imagem do Ubuntu da Galeria de Imagens do Azure ou
* Uma imagem Linux personalizada criada por você, na qual o Agente de máquina virtual Linux do Azure esteja instalado e configurado. Confira [Agente de VM Linux do Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre como criar uma VM Linux personalizada com o agente de VM do Azure.

### <a name="using-the-azure-image-gallery"></a>Usando a Galeria de Imagens do Azure
Em uma sessão bash ou de terminal, use o comando da CLI do Azure a seguir para localizar a imagem mais recente do Ubuntu na galeria da máquina virtual.

`azure vm image list | grep Ubuntu-14_04`

e selecione um dos nomes da imagens, como `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`, e use o comando a seguir para criar uma nova máquina virtual usando essa imagem.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

onde:

* *&lt;vm-cloudservice name&gt;* é o nome da VM que se tornará o computador host do contêiner Docker no Azure
* *&lt;username&gt;* é o nome de usuário do usuário raiz padrão da máquina virtual
* *&lt;password&gt;* é a senha da conta de *username* que atende aos padrões de complexidade do Azure

> [!NOTE]
> Atualmente, a senha deve ter pelo menos 8 caracteres, conter um caractere em minúscula e um em maiúscula, um número e um caractere especial, como um dos seguintes caracteres: `!@#$%^&+=`. Não, o ponto no final da frase anterior NÃO é um caractere especial.
> 
> 

Se o comando for bem-sucedido, você verá algo semelhante ao seguinte, dependendo dos argumentos e das opções precisas utilizadas:

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> A criação de uma máquina virtual pode levar alguns minutos, mas após seu provisionamento (o valor de estado é `ReadyRole`), o daemon do Docker (o serviço do Docker) é iniciado e você pode se conectar ao host do contêiner do Docker.
> 
> 

Para testar a VM do Docker que você criou no Azure, digite

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

em que *&lt;vm-name-you-used&gt;* é o nome da máquina virtual que você usou em sua chamada para `azure vm docker create`. Você deve ver algo semelhante ao código a seguir, que indica que sua máquina virtual host do Docker está funcionando no Azure e aguardando seus comandos. 

Agora você pode tentar se conectar usando o cliente do Docker para obter informações (em algumas configurações de cliente do Docker, como no Mac, talvez você precise usar `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Apenas para ter certeza de que está tudo funcionando, é possível examinar a extensão do Docker da VM:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Autenticação da máquina virtual host do Docker
Além de criar a VM do Docker, o comando `azure vm docker create` também cria automaticamente os certificados necessários para permitir que seu computador cliente Docker se conecte ao host do contêiner do Azure usando HTTPS, e os certificados são armazenados nas máquinas cliente e host, conforme adequado. Em tentativas subsequentes, os certificados existentes são reutilizados e compartilhados com o novo host.

Por padrão, os certificados são colocados no `~/.docker`e o Docker será configurado para ser executado na porta **2376**. Se você desejar usar uma porta ou diretório diferentes, use uma das opções da linha de comando `azure vm docker create` a seguir para configurar a VM host do contêiner do Docker para usar outra porta ou outros certificados para conectar clientes:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

O daemon do Docker no host é configurado para escutar e autenticar conexões clientes na porta especificada usando os certificados gerados pelo comando `azure vm docker create` . O computador cliente deve ter esses certificados para obter acesso ao host do Docker.

> [!NOTE]
> Um host em rede em execução sem esses certificados ficará vulnerável a qualquer um que possa conectar-se ao computador. Antes de modificar a configuração padrão, certifique-se de que você compreende os riscos para seu computador e outros aplicativos.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Você está pronto para conferir o [Guia do usuário do Docker] e usar sua máquina virtual Docker. Para criar uma máquina virtual habilitada para o Docker no novo portal, confira [Como usar a extensão de máquina virtual Docker com o Portal].
* A extensão de VM do Docker do Azure também oferece suporte ao Docker Compose, que usa um arquivo YAML declarativo para utilizar um aplicativo modelado por desenvolvedores em qualquer ambiente e gerar uma implantação consistente. Consulte [Introdução ao Docker e Redigir para definir e executar um aplicativo de contêiner múltiplos em uma máquina virtual do Azure].  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[Como usar a extensão de máquina virtual Docker com o Portal]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guia do usuário do Docker]:https://docs.docker.com/userguide/

[Introdução ao Docker e Redigir para definir e executar um aplicativo de contêiner múltiplos em uma máquina virtual do Azure]:../docker-compose-quickstart.md
