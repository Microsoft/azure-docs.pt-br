---
title: Exemplos de modelo do Azure Resource Manager – Instâncias de Contêiner do Azure
description: Exemplos de modelo do Azure Resource Manager para Instâncias de Contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: 2089f024e1de2e92f6e401549c5876e26db17ebb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325691"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Modelos do Azure Resource Manager para Instâncias de Contêiner do Azure

Os seguintes modelos de exemplo implantam instâncias de contêiner em várias configurações.

Para opções de implantação, consulte a [Implantação](#deployment). Se você quiser criar seus próprios modelos, o modelo de referência do modelo do Azure instâncias de contêiner do [Resource Manager][ref] detalhes e as propriedades disponíveis.

## <a name="sample-templates"></a>Modelos de exemplo

| | |
|-|-|
| **Aplicativos** ||
| [WordPress][app-wp] | Cria um site do WordPress e seu banco de dados MySQL em um grupo de contêineres. O conteúdo do site WordPress e o banco de dados MySQL persistem em um compartilhamento do Azure Files. Também cria um gateway de aplicativo para expor o acesso à rede pública ao WordPress. |
| [MS NAV com SQL Server e IIS][app-nav] | Implanta um único contêiner do Windows com um ambiente Dynamics NAV autossuficiente totalmente equipado / Dynamics 365 Business Central. |
| **Volumes** ||
| [emptyDir][vol-emptydir] | Implanta dois contêineres do Linux que compartilham um volume emptyDir. |
| [gitRepo][vol-gitrepo] | Implanta um contêiner do Linux que clona um repositório GitHub e monta-o como um volume. |
| [secret][vol-secret] | Implanta um contêiner do Linux com um certificado PFX montado como um volume de segredo. |
| **Rede** ||
| [Contêiner de UDP exposto][net-udp] | Implanta um contêiner do Windows ou Linux que expõe uma porta UDP. |
| [Contêiner do Linux com IP público][net-publicip] | Implanta um único contêiner do Linux acessível por meio de um IP público. |
| [Implantar um grupo de contêineres com uma rede virtual (versão prévia)][net-vnet] | Implanta uma nova rede virtual, sub-rede, perfil de rede e grupo de contêineres. |
| **Recursos do Azure** ||
| [Criar conta de armazenamento do Azure e compartilhamento de arquivos][az-files] | Usa a CLI do Azure em uma instância de contêiner para criar uma conta de armazenamento e um compartilhamento do Azure Files.

## <a name="deployment"></a>Implantação

Você tem várias opções para a implantação de recursos com os modelos do Gerenciador de Recursos:

[CLI do Azure][deploy-cli]

[PowerShell do Azure][deploy-powershell]

[Portal do Azure][deploy-portal]

[API REST][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
