---
title: Implantar o OpenShift no Azure Stack | Microsoft Docs
description: Implante o OpenShift no Azure Stack.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: haroldw
ms.openlocfilehash: 91b37753ae80596612eda9d3ccd34858691e35ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771542"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Implantar o OpenShift Container Platform ou OKD no Azure Stack

O OpenShift pser implantado no Azure Stack. Há algumas diferenças importantes entre o Azure e o Azure Stack, portanto a implantação será um pouco diferentes e os recursos também poderão variar.

Atualmente, o Provedor de Nuvem do Azure não funciona no Azure Stack. Por esse motivo, não será possível anexar um disco como armazenamento persistente no Azure Stack. Em vez disso, você pode configurar outras opções de armazenamento, como NFS, iSCSI, GlusterFS etc. Como alternativa, você pode habilitar o CNS e usar GlusterFS para armazenamento persistente. Se o CNS estiver habilitado, serão implantados três nós adicionais com o armazenamento adicional para uso do GlusterFS.

Você pode usar um dos vários métodos para implantar o OpenShift Container Platform ou OKD no Azure Stack:

- Você pode implantar manualmente todos os componentes de infraestrutura necessários do Azure e, em seguida, seguir a [documentação](https://docs.openshift.com/container-platform) do OpenShift Container Platform ou a [documentação do OKD](https://docs.okd.io).
- Você também pode usar um [modelo do Gerenciador de Recursos](https://github.com/Microsoft/openshift-container-platform/) existente que simplifica a implantação do cluster do OpenShift Container Platform.
- Você também pode usar um [modelo do Resource Manager](https://github.com/Microsoft/openshift-origin) existente que simplifica a implantação do cluster do OKD.

Se estiver usando o modelo do Resource Manager, selecione o branch apropriado (azurestack-release-3.x). Os modelos do Azure não funcionarão, pois as versões de API são diferentes entre o Azure e o Azure Stack. A referência da imagem de RHEL está codificada como uma variável no arquivo azuredeploy.json e precisará ser alterada para corresponder à sua imagem.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Para ambas as opções, é necessária uma assinatura do Red Hat. Durante a implantação, a instância Red Hat Enterprise Linux é registrada na Assinatura do Red Hat e anexada à ID do Pool que contém os direitos para o OpenShift Container Platform.
Certifique-se de que você tenha uma senha, uma ID do Pool e um Nome de usuário de gerente de Assinatura do Red Hat (RHSM) válidos. Como alternativa, você pode usar uma Chave de Ativação, ID da Organização e ID do Pool.  Você pode verificar essas informações entrando em https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Pré-requisitos do Azure Stack

Uma imagem de RHEL (OpenShift Container Platform) ou imagem do CentOS (OKD) precisa ser adicionada ao seu ambiente do Azure Stack para implantar um cluster do OpenShift. Entre em contato com seu administrador do Azure Stack para adicionar essas imagens. Encontre as instruções aqui:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Implantar usando o modelo do Resource Manager do OpenShift Container Platform ou do OKD

Para implantar usando o modelo do Gerenciador de Recursos, um arquivo de parâmetros é usado para fornecer todos os parâmetros de entrada. Para personalizar ainda mais a implantação, bifurque o repositório do GitHub e altere os itens apropriados.

Algumas opções comuns de personalização incluem, mas não estão limitadas a:

- Tamanho de VM de bastião (variável em azuredeploy.json)
- Convenções de nomenclatura (variáveis em azuredeploy.json)
- Especificações de cluster do OpenShift – modificadas por meio do arquivo de hosts (deployOpenShift.sh)
- Tamanho da imagem de RHEL (variável em azuredeploy.json)

Para obter as etapas implantar usando a CLI do Azure, siga a seção apropriada na seção [OpenShift Container Platform](./openshift-container-platform.md) ou na seção [OKD](./openshift-okd.md).

## <a name="next-steps"></a>Próximas etapas

- [Tarefas de pós-implantação](./openshift-post-deployment.md)
- [Solução de problemas de implantação do OpenShift no Azure](./openshift-troubleshooting.md)