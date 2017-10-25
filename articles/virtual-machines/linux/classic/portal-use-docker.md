---
title: "Usando a Extensão de VM do Docker para Linux | Microsoft Docs"
description: "Descreve as extensões de Máquinas Virtuais do Docker e do Azure, além de mostrar como criar Máquinas Virtuais do Azure que sejam hosts do Docker por meio da CLI do Azure no modelo de implantação clássico."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
ms.openlocfilehash: 932744208d9d53c87e31dcdf9e34539750be4bdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Usando a extensão de VM do Docker com o portal clássico do Azure
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

O [Docker](https://www.docker.com/) é uma das abordagens de virtualização mais populares que utiliza [contêineres Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como forma de isolar dados e computar recursos compartilhados. Você pode usar a extensão da VM Docker gerenciada pelo [Agente Linux do Azure] para criar uma VM Docker que hospede diversos contêineres para seus aplicativos no Azure.

> [!NOTE]
> Este tópico descreve como criar uma VM Docker no portal clássico do Azure. Para ver como criar uma máquina virtual Docker na linha de comando, confira [Como usar a extensão de VM Docker da Interface de Linha de Comando (CLI do Azure)]. Para participar de um debate de alto nível sobre contêineres e suas vantagens, confira o [Quadro de comunicações de alto nível do Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>Criar uma nova máquina virtual na Galeria de Imagens
A primeira etapa requer uma máquina virtual do Azure obtida por meio de uma imagem Linux compatível com a extensão de máquina virtual Docker, usando uma imagem Ubuntu 14.04 LTS da Galeria de Imagens como exemplo de imagem do servidor e um desktop Ubuntu 14.04 como cliente. No portal, clique em **+ Novo** no canto inferior esquerdo para criar uma nova instância de máquina virtual e selecione uma imagem Ubuntu 14.04 LTS dentre as opções disponíveis ou na Galeria de Imagens, como mostramos abaixo.

> [!NOTE]
> Atualmente, apenas imagens do Ubuntu 14.04 LTS mais recentes de julho de 2014 dão suporte à extensão VM Docker.
> 
> 

![Criar uma nova imagem Ubuntu](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Criar certificados Docker
Depois de criar a máquina virtual, verifique se o Docker está instalado no computador cliente. Para obter detalhes, confira [Instruções de instalação do Docker](https://docs.docker.com/installation/#installation).

Crie os arquivos de certificado e de chave para comunicação do Docker de acordo com as instruções em [Executando Docker com https] e coloque-os no diretório **`~/.docker`** do computador cliente.

> [!NOTE]
> A extensão de VM Docker no portal atualmente requer credenciais que são codificadas na base 64.
> 
> 

Na linha de comando, use **`base64`** ou outra ferramenta de codificação de sua preferência para criar tópicos codificados em base64. Para executar essas ações com um conjunto simples de arquivos de certificado e de chave, use um código parecido com este:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Adicionar a extensão de máquina virtual Docker
Para adicionar a extensão de máquina virtual Docker, localize a instância de máquina virtual criada e clique em **Extensões** para visualizar as extensões de máquina virtual, como mostramos abaixo.

> [!NOTE]
> Essa funcionalidade somente tem suporte no portal de visualização: https://portal.azure.com/
> 
> 

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>Adicionar uma extensão
Clique em **+ Adicionar** para visualizar as extensões de máquina virtual que podem ser adicionadas à máquina em questão.

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Selecionar a extensão de máquina virtual Docker
Escolha a extensão de VM do Docker, que apresenta a descrição do Docker e links importantes. Em seguida, clique em **Criar** na parte inferior da página para iniciar o procedimento de instalação.

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>Adicionar seus arquivos de certificado e de chaves:
Nos campos do formulário, insira as versões codificadas em base64 do certificado de autoridade de certificação, do certificado do servidor e da chave do servidor, como mostramos na imagem abaixo.

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> Observe que (como na imagem anterior) 2376 é preenchido por padrão. Você pode inserir qualquer ponto de extremidade nesse campo, mas precisará abrir o ponto de extremidade correspondente na próxima etapa. Se alterar o padrão, lembre-se de abrir o ponto de extremidade correspondente na etapa seguinte.
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>Adicionar o ponto de extremidade de comunicação do Docker
Ao exibir o grupo de recursos que você criou, selecione o grupo de segurança de rede associado com sua VM e clique em **Regras de Segurança de Entrada** para exibir as regras, como mostrado aqui.

![](media/portal-use-docker/AddingEndpoint.png)

Clique em **+ Adicionar** para adicionar outra regra e, no caso padrão, insira o nome do ponto de extremidade (neste exemplo, **Docker**) e 'Intervalo de Portas de Destino' 2376. Defina o valor do protocolo mostrando **TCP** e clique em **OK** para criar a regra.

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>Testar o cliente Docker e o host Docker do Azure
Localize e copie o nome do domínio da sua VM, na linha de comando de seu computador cliente, insira `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:2376 info` (onde *dockerextension* é substituído pelo subdomínio da VM).

O resultado deve ter esta aparência:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Depois de concluir as etapas acima, você terá um host de Docker totalmente funcional em execução em uma VM do Azure, configurado para ser conectado remotamente a outros clientes.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
Você está pronto para conferir o [Guia do usuário do Docker] e usar sua máquina virtual Docker. Se quiser automatizar a criação hosts de Docker em VMs do Azure por meio da interface de linha de comando, confira [Como usar a extensão de VM Docker da Interface de Linha de Comando (CLI do Azure)]

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Como usar a extensão de VM Docker da Interface de Linha de Comando (CLI do Azure)]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Agente Linux do Azure]:../agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[Executando Docker com https]:http://docs.docker.com/articles/https/
[Guia do usuário do Docker]:https://docs.docker.com/userguide/
