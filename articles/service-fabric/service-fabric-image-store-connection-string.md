---
title: Cadeia de conexão de armazenamento de imagens do Azure Service Fabric | Microsoft Docs
description: Entender a cadeia de conexão de armazenamento de imagens
services: service-fabric
documentationcenter: .net
author: alexwun
manager: chackdan
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 4a56b48c0041e963b89312c59335b45cabacc1bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720156"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Noções básicas sobre a configuração ImageStoreConnectionString

Em algumas das documentações, devemos mencionar brevemente a existência de um parâmetro "ImageStoreConnectionString" sem descrevendo o que realmente significa. E depois de passar por um artigo como [Implantar e remover aplicativos usando o PowerShell][10], tudo o que você faz é copiar / colar o valor, conforme mostrado no manifesto do cluster do cluster de destino. Então, a configuração deve ser configurável por cluster, mas quando você cria um cluster por meio de [portal do Azure][11], não há nenhuma opção para definir esta configuração e é sempre "fabric: ImageStore". Qual é a finalidade dessa configuração?

![Manifesto do cluster][img_cm]

O Service Fabric começou como uma plataforma para consumo interno da Microsoft por muitas equipes diferentes, para alguns aspectos são altamente personalizáveis - o repositório"imagem" é um aspecto tal. Essencialmente, o armazenamento de imagens é um repositório conectável para armazenar pacotes de aplicativos. Quando seu aplicativo é implantado em um nó no cluster, o nó baixa o conteúdo de seu pacote de aplicativos do repositório de imagem. ImageStoreConnectionString é uma configuração que inclui todas as informações necessárias para clientes e nós localizar o armazenamento correto de imagem de um determinado cluster.

Existem três tipos possíveis de provedores de armazenamento de imagens e suas cadeias de conexão correspondentes são os seguintes:

1. Serviço de Armazenamento de Imagens: "fabric:ImageStore"

2. Sistema de Arquivos: "file:[caminho do sistema de arquivos]"

3. Armazenamento do Azure: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

O tipo de provedor usado na produção é o serviço de armazenamento de imagem, que é um serviço de sistema persistente com monitoração de estado que você pode ver no Service Fabric Explorer. 

![Serviço de armazenamento de imagens][img_is]

O armazenamento de imagens em um serviço de sistema dentro do próprio cluster de hospedagem elimina as dependências externas para o repositório de pacotes e nos dá mais controle sobre a localidade de armazenamento. As melhorias futuras no armazenamento de imagens do têm probabilidade do provedor de armazenamento de imagens de destino, primeiro, se não exclusivamente. A cadeia de conexão para o provedor de serviço de armazenamento de imagem não tem nenhuma informação exclusiva desde que o cliente já está conectado ao cluster de destino. O cliente só precisa saber que protocolos direcionando o serviço do sistema devem ser usados.

O provedor do sistema de arquivos é usado em vez do serviço de armazenamento de imagem para clusters de uma caixa locais durante o desenvolvimento para inicializar o cluster ligeiramente mais rápido. A diferença é normalmente pequena, mas é uma otimização útil para a maioria das pessoas durante o desenvolvimento. É possível implantar um cluster local de uma caixa com os outros provedor tipos de armazenamento, bem, mas geralmente não há nenhum motivo para fazer isso, já que o fluxo de trabalho de desenvolvimento/teste permanece o mesmo, independentemente do provedor. O provedor de Armazenamento do Microsoft Azure existe somente para suporte herdado de clusters antigos implantados antes que o provedor de Serviço de Repositório de Imagens foi introduzido.

Além disso, o provedor do sistema de arquivos ou o provedor de armazenamento do Azure não deve ser usado como um método de compartilhamento de um armazenamento de imagem entre vários clusters - isso resultará em corrupção de dados de configuração de cluster como cada cluster pode gravar dados conflitantes para o armazenamento de imagem. Para compartilhar pacotes de aplicativos provisionados entre vários clusters, use os arquivos [sfpkg][12], que podem ser carregados em qualquer repositório externo com um URI de download.

Portanto, embora ImageStoreConnectionString é configurável, você simplesmente usa a configuração padrão. Ao publicar no Azure por meio do Visual Studio, o parâmetro será definido automaticamente para você de forma adequada. Para implantação programática em clusters hospedados no Azure, a cadeia de conexão sempre será "fabric: ImageStore". Entretanto, em caso de dúvida, seu valor pode ser verificado sempre por meio da recuperação do manifesto do cluster pelo [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx) ou [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Os clusters de teste e de produção locais sempre deverão estar configurados para usar o provedor do Serviço de repositório de imagens também.

### <a name="next-steps"></a>Próximas etapas
[Implantar e remover aplicativos usando o PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
