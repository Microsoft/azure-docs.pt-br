---
title: Gerenciar aplicativos do Azure Service Fabric usando a CLI do Azure Service Fabric (sfctl)
description: Saiba como implantar e remover aplicativos de um cluster do Azure Service Fabric usando a CLI do Azure Service Fabric
services: service-fabric
author: rockboyfor
manager: digimobile
ms.service: service-fabric
ms.topic: conceptual
origin.date: 07/31/2018
ms.date: 04/29/2019
ms.author: v-yeche
ms.openlocfilehash: 9b0f785a6a43f984708645084a8a8036326d3d24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621370"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Gerenciar um aplicativo do Azure Service Fabric usando a CLI do Azure Service Fabric (sfctl)

Saiba como criar e excluir os aplicativos que estão em execução em um cluster do Azure Service Fabric.

## <a name="prerequisites"></a>Pré-requisitos

* Implantar a CLI do Service Fabric. Em seguida, selecione o cluster do Service Fabric. Para obter mais informações, consulte [Introdução à CLI do Service Fabric](service-fabric-cli.md).

* Tenha um pacote de aplicativos do Service Fabric pronto para ser implantado. Para obter mais informações sobre como criar e empacotar um aplicativo, leia sobre o [modelo de aplicativo do Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Visão geral

Para implantar um novo aplicativo, execute estas etapas:

1. Faça upload de um pacote de aplicativos no repositório de imagens do Service Fabric.
2. Provisione um tipo de aplicativo.
3. Exclua o conteúdo do repositório de imagens.
4. Especifique e crie um aplicativo.
5. Especifique e crie serviços.

Para remover um aplicativo existente, execute estas etapas:

1. Exclua o aplicativo.
2. Desprovisione o tipo de aplicativo associado.

## <a name="deploy-a-new-application"></a>Implantar um novo aplicativo

Para implantar um novo aplicativo, execute as seguintes tarefas:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Carregar um novo pacote de aplicativos no repositório de imagens

Antes de criar um aplicativo, carregue o pacote de aplicativos no repositório de imagens do Service Fabric.

Por exemplo, se o seu pacote de aplicativos está no diretório `app_package_dir`, use os seguintes comandos para carregar o diretório:

```azurecli
sfctl application upload --path ~/app_package_dir
```

Para pacotes de aplicativos grandes, especifique a opção `--show-progress` para exibir o progresso do upload.

### <a name="provision-the-application-type"></a>Provisionar o tipo de aplicativo

Quando o upload for concluído, provisione o aplicativo. Para provisionar o aplicativo, use o seguinte comando:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

O valor de `application-type-build-path` é o nome do diretório em que você carregou o pacote de aplicativos.

### <a name="delete-the-application-package"></a>Excluir o pacote de aplicativos

É recomendável que você remova o pacote de aplicativos depois que o aplicativo for registrado com êxito.  Excluir pacotes de aplicativos do repositório de imagens libera recursos do sistema.  Manter pacotes de aplicativos não utilizados consome o armazenamento em disco e leva a problemas de desempenho do aplicativo. 

Para excluir o pacote de aplicativos do repositório de imagens, use o seguinte comando:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` deve ser o nome do diretório que você carregou ao criar o aplicativo.

### <a name="create-an-application-from-an-application-type"></a>Criar um aplicativo com base em um tipo de aplicativo

Depois de provisionar o aplicativo, use o seguinte comando para nomear e criar seu aplicativo:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` é o nome que você deseja usar para a instância do aplicativo. Você pode obter parâmetros adicionais no manifesto do aplicativo provisionado anteriormente.

O nome do aplicativo deve começar com o prefixo `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Criar serviços para o novo aplicativo

Depois de criar um aplicativo, crie serviços com base no aplicativo. No exemplo a seguir, criamos um novo serviço sem estado com base em nosso aplicativo. Os serviços que podem ser criados por meio de um aplicativo estão definidos em um manifesto do serviço dentro do pacote de aplicativos provisionado anteriormente.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Verificar a integridade e a implantação do aplicativo

Para verificar a integridade de todos os componentes, use os seguintes comandos de integridade:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Para verificar se o serviço está íntegro, use comandos semelhantes para recuperar a integridade do serviço e do aplicativo:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Os serviços e aplicativos íntegros têm um valor `HealthState` igual a `Ok`.

## <a name="remove-an-existing-application"></a>Remover um aplicativo existente

Para remover um aplicativo, execute as seguintes tarefas:

### <a name="delete-the-application"></a>Excluir o aplicativo

Para excluir o aplicativo, use o seguinte comando:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Desprovisionar o tipo de aplicativo

Depois de excluir o aplicativo, você pode desprovisionar o tipo de aplicativo se ele não for mais necessário. Para desprovisionar o tipo de aplicativo, use o seguinte comando:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

O nome e a versão do tipo devem corresponder ao nome e à versão no manifesto do aplicativo provisionado anteriormente.

## <a name="upgrade-application"></a>Atualizar aplicativo

Após criar seu aplicativo, você pode repetir o mesmo conjunto de etapas para provisionar uma segunda versão do aplicativo. Em seguida, com uma atualização do aplicativo do Service Fabric, você pode fazer a transição para executar a segunda versão do aplicativo. Para obter mais informações, consulte a documentação sobre [Atualizações de aplicativo do Service Fabric](service-fabric-application-upgrade.md).

Para realizar uma atualização, primeiro provisione a próxima versão do aplicativo usando os mesmos comandos que antes:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Em seguida, é recomendável realizar uma atualização automática monitorada. Inicie a atualização executando o seguinte comando:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

As atualizações substituem os parâmetros existentes por qualquer conjunto especificado. Parâmetros do aplicativo devem ser transmitidos como argumentos para o comando de atualização, se necessário. Parâmetros do aplicativo devem ser codificados como um objeto JSON.

Para recuperar parâmetros especificados anteriormente, você pode usar o comando `sfctl application info`.

Quando uma atualização de aplicativo estiver em andamento, o status poderá ser recuperado usando o comando `sfctl application upgrade-status`.

Por fim, se uma atualização estiver em andamento e precisar ser cancelada, você poderá usar `sfctl application upgrade-rollback` para reverter a atualização.

## <a name="next-steps"></a>Próximas etapas

* [Noções básicas do Service Fabric](service-fabric-cli.md)
* [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md)
* [Iniciar uma atualização de aplicativo do Service Fabric](service-fabric-application-upgrade.md)

<!--Update_Description: update meta properties -->