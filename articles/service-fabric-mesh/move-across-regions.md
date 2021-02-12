---
title: Mover um aplicativo de malha Service Fabric para outra região
description: Você pode mover Service Fabric recursos de malha implantando uma cópia do modelo atual em uma nova região do Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1b59d482b8b88e37da2d61636ff3f254a46ba5c2
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626080"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Mover um aplicativo de malha Service Fabric para outra região do Azure

> [!IMPORTANT]
> A visualização da malha de Service Fabric do Azure foi desativada. Novas implantações não serão mais permitidas por meio da API de malha Service Fabric. O suporte para implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, consulte desativação da [Visualização da malha de Service Fabric do Azure](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Este artigo descreve como mover seu Service Fabric aplicativo de malha e seus recursos para uma região diferente do Azure. Você pode mover seus recursos para outra região por vários motivos. Por exemplo, em resposta a interrupções, para obter recursos ou serviços disponíveis somente em regiões específicas, para atender aos requisitos internos de políticas e governança, ou em resposta aos requisitos de planejamento de capacidade.

 A [malha de Service Fabric não oferece suporte](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) à capacidade de mover recursos diretamente entre regiões do Azure. No entanto, você pode mover os recursos indiretamente implantando uma cópia do seu modelo de Azure Resource Manager atual na nova região de destino e, em seguida, redirecionando o tráfego de entrada e as dependências para o aplicativo de malha Service Fabric recém-criado.

## <a name="prerequisites"></a>Pré-requisitos

* Controlador de entrada (como o [Gateway de aplicativo](../application-gateway/index.yml)) para servir como um intermediário para roteamento de tráfego entre clientes e seu aplicativo de malha de Service Fabric
* Disponibilidade de malha de Service Fabric (versão prévia) na região do Azure de destino ( `westus` , `eastus` ou `westeurope` )

## <a name="prepare"></a>Preparar

1. Tire um "instantâneo" do estado atual do seu aplicativo de malha de Service Fabric exportando o modelo de Azure Resource Manager e os parâmetros da implantação mais recente. Para fazer isso, siga as etapas em [Exportar modelo após a implantação](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) usando o portal do Azure. Você também pode usar [CLI do Azure](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou [API REST](/rest/api/resources/resourcegroups/exporttemplate).

2. Se aplicável, [exporte outros recursos no mesmo grupo de recursos](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) para reimplantação na região de destino.

3. Examine (e edite, se necessário) o modelo exportado para garantir que os valores de propriedade existentes sejam aqueles que você deseja usar na região de destino. O novo `location` (região do Azure) é um parâmetro que você fornecerá durante a reimplantação.

## <a name="move"></a>Mover

1. Crie um novo grupo de recursos (ou use um existente) na região de destino.

2. Com o modelo exportado, siga as etapas em [implantar recursos de modelo personalizado](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) usando o portal do Azure. Você também pode usar [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)ou [API REST](../azure-resource-manager/templates/deploy-rest.md).

3. Para obter orientação sobre como mover recursos relacionados, como [contas de armazenamento do Azure](../storage/common/storage-account-move.md), consulte a orientação para serviços individuais listados no tópico [movendo recursos do Azure entre regiões](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Verificar

1. Quando a implantação for concluída, teste os pontos de extremidade do aplicativo para verificar a funcionalidade do seu aplicativo.

2. Você também pode verificar o status do seu aplicativo verificando o status do aplicativo ([AZ mesh App Show](/cli/azure/ext/mesh/mesh/app#ext-mesh-az-mesh-app-show)) e revisando os comandos logs do aplicativo e ([AZ mesh código-Package-log](/cli/azure/ext/mesh/mesh/code-package-log)) usando a [CLI da malha de Service Fabric do Azure](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Commit

Depois de confirmar a funcionalidade equivalente do seu aplicativo de malha de Service Fabric na região de destino, configure o controlador de entrada (por exemplo, o [Gateway de aplicativo](../application-gateway/redirect-overview.md)) para redirecionar o tráfego para o novo aplicativo.

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Para concluir a movimentação do aplicativo de malha Service Fabric, [exclua o aplicativo de origem e/ou o grupo de recursos pai](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Próximas etapas

* [Mover recursos do Azure entre regiões](../azure-resource-manager/management/move-region.md)
* [Suporte para mover recursos do Azure entre regiões](../azure-resource-manager/management/region-move-support.md)
* [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Suporte da operação de movimentação para recursos](../azure-resource-manager/management/move-support-resources.md
)
