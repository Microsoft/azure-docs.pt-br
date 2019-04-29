---
title: Introdução ao Modelo de Recursos do Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre o Modelo de Recursos do Service Fabric, uma abordagem simplificada para definir aplicativos de Malha do Service Fabric.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 3cee0ada75c4ea265c7e9c598408eb6b01477d6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810761"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introdução ao Modelo de Recurso do Service Fabric

O Modelo de Recursos do Service Fabric descreve uma abordagem simples para definir os recursos que compõem um aplicativo de Malha do Service Fabric. Recursos individuais podem ser implantados em qualquer ambiente do Service Fabric.  O Modelo de Recurso do Service Fabric também é compatível com o Modelo do Azure Resource Manager. Atualmente, há suporte para os seguintes tipos de recursos neste modelo:

- Aplicativos e serviços
- Redes
- Gateways
- Segredos e segredos/valores
- Volumes

Cada recurso é descrito declarativamente em um arquivo de recurso, que é um simple YAML ou o documento JSON que descreve o aplicativo de malha e é provisionado pela plataforma do Service Fabric.

## <a name="applications-and-services"></a>Aplicativos e serviços

Um recurso de aplicativo é a unidade de implantação, controle de versão e tempo de vida de um aplicativo de Malha. Ele é composto por um ou mais recursos de serviço que representam um microsserviço. Cada recurso de serviço, por sua vez, é composto por um ou mais pacotes de códigos que descrevem tudo o que é necessário para executar a imagem de contêiner associada ao pacote de códigos.

![Aplicativos e serviços][Image1]

Um recurso de serviço declara o seguinte:

- Registro, a versão e o nome do contêiner
- Recursos de CPU e memória necessários para cada contêiner
- Pontos de extremidade de rede
- Referências a outros recursos, como redes, volumes e segredos 

Todos os pacotes de código definidos como parte de um recurso de serviço são implantados e ativados juntos como um grupo. O recurso de Serviço também descreve quantas instâncias do serviço para executar e também faz referência a outros recursos (por exemplo, o recurso de rede) ele depende.

Se um Aplicativo de Malha é composto de mais de um serviço, eles não são garantidos para ser executado em conjunto no mesmo nó. Além disso, durante uma atualização do aplicativo, falha de atualização de um único serviço resultará em todos os serviços que está sendo revertidos para sua versão anterior.

Como mencionado anteriormente, o ciclo de vida de cada instância do aplicativo pode ser gerenciado independentemente. Por exemplo, uma instância do aplicativo pode ser atualizada independentemente de outras instâncias do aplicativo. Normalmente, você mantém o número de serviços em um aplicativo bem pequeno, como mais serviços que incluir em um aplicativo, mais difícil torna-se gerenciar cada serviço de modo independente.

## <a name="networks"></a>Redes

Recurso de rede é recurso implantado individualmente, independente de um recurso de aplicativo ou serviço que pode fazer referência a ele como suas dependências. Ele é usado para criar uma rede privada para os aplicativos. Vários Serviços de Aplicativos diferentes podem fazer parte da mesma rede.  Para obter mais informações, leia sobre [rede nos aplicativos da Malha do Service Fabric](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> A visualização atual dá suporte apenas a um mapeamento um-para-um entre aplicativos e redes

![Rede e gateway][Image2]

## <a name="gateways"></a>Gateways
Um recurso de Gateway conecta duas redes e roteia o tráfego.  Um gateway permite que os serviços se comuniquem com clientes externos e fornece uma entrada no(s) serviço(s).  Um gateway também pode ser usado para conectar o aplicativo de malha com sua própria rede virtual existente. Para obter mais informações, leia sobre [rede nos aplicativos da Malha do Service Fabric](service-fabric-mesh-networks-and-gateways.md).

![Rede e gateway][Image2]

## <a name="secrets"></a>Segredos

Recursos de segredos são implantáveis independentes de um recurso de aplicativo ou de serviço que pode fazer referência a ele como sua dependência. Ele é usado para entregar segredos com segurança para os aplicativos. Vários serviços de diferentes aplicativos podem fazer referência a valores do mesmo segredo.

## <a name="volumes"></a>Volumes

Contêineres frequentemente disponibilizam discos temporários. Discos temporários são efêmeros, no entanto, você obtém um novo disco temporário e perde as informações quando um contêiner falha. Também é difícil de compartilhar informações sobre discos temporários com outros contêineres. Os volumes são diretórios que são montados dentro de suas instâncias de contêiner que você pode usar para manter o estado. Os volumes oferecem armazenamento de arquivos de finalidade geral e permitem a você ler/gravar arquivos usando APIs de arquivo de E/S de disco normal. O recurso de volume é uma maneira declarativa para descrever como um diretório é montado e o armazenamento subjacente para ele (Volume de Arquivos do Azure ou Volume Confiável do Service Fabric).  Para obter mais informações, leia [estado de armazenamento](service-fabric-mesh-storing-state.md#volumes).

![Volumes][Image3]

## <a name="programming-models"></a>Modelos de programação
Recurso de serviço requer apenas uma imagem de contêiner em execução, o que é referenciada nos pacotes de código associados ao recurso. Você pode executar qualquer código escrito em qualquer linguagem, usando qualquer estrutura dentro do contêiner sem a necessidade de saber ou usar APIs específicas de Malha do Service Fabric. 

O código do aplicativo permanece portátil até mesmo fora da Malha do Service Fabric e suas implantações de aplicativo permanecem consistentes, independentemente da linguagem ou estrutura usada para implementar seus serviços. Se seu aplicativo é apenas um conjunto de processos e scripts, Go ou ASP.NET Core, o modelo de implantação de recursos de Malha do Service Fabric permanece o mesmo. 

## <a name="packaging-and-deployment"></a>Empacotamento e implantação

Aplicativos da Malha do Service Fabric baseados no modelo de recurso são empacotados como contêineres do Docker.  Malha do Service Fabric é um ambiente compartilhado multilocatário e contêineres fornecem um alto nível de isolamento.  Esses aplicativos são descritos usando um formato JSON ou um formato YAML (que é então convertido em JSON). Ao implantar um aplicativo de malha a malha do Azure Service Fabric, o JSON usado para descrever o aplicativo é um modelo do Azure Resource Manager. Recursos são mapeados para recursos do Azure.  Ao implantar um aplicativo de malha para um cluster do Service Fabric (autônomo ou hospedado no Azure), o JSON usado para descrever o aplicativo é um formato semelhante a um modelo do Azure Resource Manager.  Uma vez implantado, os aplicativos de malha podem ser gerenciados por meio de interfaces HTTP ou pela CLI do Azure. 


## <a name="next-steps"></a>Próximas etapas 
Para saber mais sobre Malha do Service Fabric, leia a visão geral:
- [Visão geral da Malha do Service Fabric](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
