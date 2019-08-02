---
title: Tutorial de limpeza do cluster do Service Fabric autônomo - Azure Service Fabric | Microsoft Docs
description: Neste tutorial, você aprende como limpar seu cluster autônomo
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bebe3a2dc83b651e713ee80d7b11068b13096e04
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385169"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: Limpar seu cluster autônomo

Os clusters autônomos do Service Fabric oferecem a opção de escolher seu próprio ambiente e criar um cluster como parte da abordagem “qualquer sistema operacional, qualquer nuvem” que está sendo adotada pelo Service Fabric. Nesta série de tutoriais, você cria um cluster autônomo hospedado no AWS ou no Azure e instala um aplicativo nele.

Este tutorial é a parte quatro de uma série. Esta parte do tutorial mostra como limpar os recursos do AWS ou do Azure que você criou para hospedar o cluster do Service Fabric.

Na primeira parte da série, você aprende a:

> [!div class="checklist"]
> * Limpar um cluster do Service Fabric
> * Limpar o AWS ou os recursos do Azure

## <a name="clean-up-service-fabric-cluster"></a>Limpar um cluster do Service Fabric

1. RDP para a instância de VM que você usou para instalar o Service Fabric.
2. Abra o PowerShell.
3. Altere o diretório para a pasta extraída do segundo tutorial.
4. Execute o seguinte comando para remover o cluster do Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Insira `Y` quando solicitado; se tiver sido bem-sucedido, a saída será semelhante à mostrada a seguir, com seus próprios endereços IP substituídos:

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="clean-up-aws-resources"></a>Limpe os recursos do AWS

1. Entre na sua conta do AWS.
2. Vá para o Console EC2.
3. Selecione os três nós que você criou na primeira parte do tutorial.
4. Clique em **Ações** > **Estado da instância** > **Terminar**.

## <a name="clean-up-azure-resources"></a>Limpar recursos do Azure

1. Entre no Portal do Azure.
2. Vá para a seção **Máquinas Virtuais**.
3. Marque as caixas de seleção dos três nós que você criou na primeira parte do tutorial.
4. Clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Na parte quatro da série, você aprendeu a limpar os recursos que foram criados nas etapas anteriores.

> [!div class="checklist"]
> * Limpar seus recursos

> [!div class="nextstepaction"]
> [Voltando ao início](service-fabric-tutorial-standalone-create-infrastructure.md)
