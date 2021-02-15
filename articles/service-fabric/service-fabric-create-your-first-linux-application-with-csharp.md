---
title: 'Criar seu primeiro aplicativo de Service Fabric do Azure no Linux usando C #'
description: Saiba como criar e implantar um aplicativo do Service Fabric usando C# e .NET Core 2.0.
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: 556d8dee4efd492fa98755f1ffd1cdc1c9887856
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82193642"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Criar seu primeiro aplicativo do Azure Service Fabric
> [!div class="op_single_selector"]
> * [Java - Linux (versão prévia)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux (versão prévia)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

O Service Fabric fornece SDKs para compilação de serviços no Linux em .NET Core e Java. Neste tutorial, vamos ver como criar um aplicativo para Linux e compilar um serviço usando C# no .NET Core 2.0.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você [configurar o ambiente de desenvolvimento Linux](service-fabric-get-started-linux.md). Se você estiver usando Mac OS X, poderá [configurar um ambiente de uma caixa do Linux em uma máquina virtual usando Vagrant](service-fabric-get-started-mac.md).

Você também desejará instalar a [CLI do Service Fabric](service-fabric-cli.md)

### <a name="install-and-set-up-the-generators-for-c"></a>Instalar e configurar os geradores para C#
O Service Fabric fornece ferramentas de scaffolding que ajudarão a criar um aplicativo do Service Fabric no terminal usando gerador de modelos Yeoman. Siga estas etapas para configurar os geradores de modelo do Yeoman para C#:

1. Instalar o nodejs e o NPM em seu computador

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. Instalar o gerador de modelos do [Yeoman](https://yeoman.io/) em seu computador por meio do NPM

   ```bash
   npm install -g yo
   ```
3. Instalar o gerador de aplicativos Yeoman C# do Service Fabric no NPM

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Criar o aplicativo
Um aplicativo do Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade do aplicativo. O gerador [Yeoman](https://yeoman.io/) do Service Fabric para C#, instalado na última etapa, que facilita a criação de seu primeiro serviço e a adição de mais serviços posteriormente. Vamos usar Yeoman para criar um novo aplicativo com um único serviço.

1. Em um terminal, digite o comando a seguir para começar a criar o scaffolding: `yo azuresfcsharp`
2. Nome do seu aplicativo.
3. Escolha o tipo de seu primeiro serviço e dê um nome para ele. Para os fins deste tutorial, escolheremos o Serviço Reliable Actor.

   ![Gerador de Yeoman do Service Fabric para C#][sf-yeoman]

> [!NOTE]
> Para obter mais informações sobre as opções, confira [Visão geral do modelo de programação do Service Fabric](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Compilar o aplicativo
Os modelos Yeoman do Service Fabric incluem um script de build para criar o aplicativo por meio do terminal (após navegar até a pasta do terminal).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Implantar o aplicativo

Após a compilação do aplicativo, você pode implantá-lo no cluster local.

1. Conectar-se ao cluster local do Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Use o script de instalação fornecido no modelo para copiar o pacote de aplicativo no repositório de imagens do cluster, registre o tipo de aplicativo e crie uma instância do aplicativo.

    ```bash
    ./install.sh
    ```

A implantação do aplicativo interno é igual a qualquer outro aplicativo do Service Fabric. Consulte a documentação sobre [como gerenciar um aplicativo do Service Fabric com a CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md) para obter instruções detalhadas.

Os parâmetros para esses comandos podem ser encontrados nos manifestos gerados dentro do pacote de aplicativos.

Depois da implantação do aplicativo, abra um navegador e navegue até [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) em `http://localhost:19080/Explorer`. Em seguida, expanda o nó **Aplicativos** e observe que agora há uma entrada para o seu tipo de aplicativo e outra para a primeira instância desse tipo.

> [!IMPORTANT]
> Para implantar o aplicativo em um cluster seguro do Linux no Azure, você precisa configurar um certificado para validar seu aplicativo com o runtime do Service Fabric. Isso permite que os serviços de Reliable Services se comuniquem com as APIs de runtime do Service Fabric subjacente. Para obter mais informações, consulte [Configurar um aplicativo de Reliable Services para executar em clusters do Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Inicie o cliente de teste e execute um failover
Projetos de atores não fazem nada por conta própria. Eles exigem outro serviço ou cliente para enviar mensagens a eles. O modelo de ator inclui um script de teste simples que você pode usar para interagir com o serviço de ator.

1. Execute o script usando o utilitário de inspeção para ver a saída do serviço de ator.

   No caso do MAC OS X, você precisa copiar a pasta myactorsvcTestClient para o mesmo local dentro do contêiner executando os seguintes comandos adicionais.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. No Service Fabric Explorer, localize o nó que hospeda a réplica primária para o serviço de ator. Na captura de tela abaixo, é o nó 3.

    ![Localizar a réplica primária no Service Fabric Explorer][sfx-primary]
3. Clique no nó encontrado na etapa anterior e selecione **Desativar (Reiniciar)** no menu Ações. Esta ação reiniciará um nó no cluster local e forçará um failover para uma das réplicas secundárias em execução em outro nó. Ao fazer isso, preste atenção à saída do cliente de teste e observe que o contador continua a aumentar apesar do failover.

## <a name="adding-more-services-to-an-existing-application"></a>Adicionando mais serviços a um aplicativo existente

Para adicionar outro serviço a um aplicativo já criado usando `yo`, execute as seguintes etapas:
1. Altere o diretório para a raiz do aplicativo existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é o aplicativo criado pelo Yeoman.
2. Execute `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>Próximas etapas

* [Interação com os clusters do Service Fabric usando a CLI do Service Fabric](service-fabric-cli.md)
* Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
