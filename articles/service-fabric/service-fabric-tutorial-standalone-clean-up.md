---
title: Limpar um cluster autônomo
description: Neste tutorial, você aprenderá a excluir os recursos da AWS ou do Azure no cluster autônomo do Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842879"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: Limpar seu cluster autônomo

Os clusters autônomos do Service Fabric oferecem a opção de escolher o próprio ambiente para hospedar o Service Fabric. Nesta série de tutoriais, você criará um cluster autônomo hospedado na AWS ou no Azure e implantará um aplicativo nele.

Este tutorial é a parte quatro de uma série. Esta parte do tutorial mostra como excluir os recursos da AWS ou do Azure que você criou para hospedar o cluster do Service Fabric.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Remover um cluster do Service Fabric
> * Limpar os recursos da AWS ou do Azure

## <a name="remove-a-service-fabric-cluster"></a>Remover um cluster do Service Fabric

1. RDP para a instância de VM que você usou para instalar o Service Fabric.
2. Abra o PowerShell.
3. Altere o diretório para a pasta extraída do segundo tutorial.
4. Execute o seguinte comando para remover o cluster do Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Insira `Y` quando solicitado. Se tiver êxito, sua saída terá a seguinte aparência (com os próprios endereços IP):

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

## <a name="delete-aws-resources"></a>Excluir recursos da AWS

1. Entre na sua conta do AWS.
2. Vá para o Console EC2.
3. Selecione os três nós que você criou na primeira parte do tutorial.
4. Selecione **Ações** > **Estado da Instância** > **Terminar**.

## <a name="delete-azure-resources"></a>Excluir recursos do Azure

1. Entre no portal do Azure.
2. Vá para a seção **Máquinas Virtuais**.
3. Marque as caixas de seleção dos três nós que você criou na primeira parte do tutorial.
4. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a excluir os recursos criados nas etapas anteriores.

> [!div class="checklist"]
> * Limpar seus recursos

> [!div class="nextstepaction"]
> [Voltando ao início](service-fabric-tutorial-standalone-create-infrastructure.md)
