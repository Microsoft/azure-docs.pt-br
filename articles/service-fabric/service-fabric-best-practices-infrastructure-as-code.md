---
title: Infraestrutura de Service Fabric do Azure como práticas recomendadas de código
description: Práticas recomendadas e considerações de design para gerenciar o Azure Service Fabric como uma infraestrutura como código.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: b765d92778df40caec0864dc6f547324216fdb07
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611973"
---
# <a name="infrastructure-as-code"></a>Infraestrutura como código

Em um cenário de produção, crie clusters do Azure Service Fabric usando modelos do Resource Manager. Os modelos do Resource Manager fornecem maior controle das propriedades de recurso e garantem que você tenha um modelo de recursos consistente.

Os modelos de exemplo do Resource Manager estão disponíveis para Windows e Linux nos [Exemplos do Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Esses modelos podem ser usados como ponto de partida para o modelo de cluster. Faça o download de `azuredeploy.json` e de `azuredeploy.parameters.json` e edite-os para atender aos seus requisitos personalizados.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para implantar os modelos `azuredeploy.json` e `azuredeploy.parameters.json` baixados acima, use os seguintes comandos da CLI do Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az deployment group create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Como criar um recurso usando o Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Recursos do Azure Service Fabric

É possível implantar aplicativos e serviços em seu cluster do Service Fabric por meio do Azure Resource Manager. Confira [Gerenciar aplicativos e serviços como recursos do Azure Resource Manager](./service-fabric-application-arm-resource.md) para obter detalhes. Veja a seguir os recursos específicos de aplicativo do Service Fabric considerados como prática recomendada a serem incluídos em seus recursos de modelo do Resource Manager.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Para implantar o seu aplicativo usando o Azure Resource Manager, primeiramente, você deve [criar um sfpkg](./service-fabric-package-apps.md#create-an-sfpkg), pacote de aplicativo do Service Fabric. O seguinte script python é um exemplo de como criar um sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Configuração de atualização automática do sistema operacional da máquina virtual do Azure 
A atualização de suas máquinas virtuais é uma operação iniciada pelo usuário e é recomendável que você use a [atualização do sistema operacional automático do conjunto de dimensionamento de máquinas virtuais](service-fabric-patch-orchestration-application.md) para o gerenciamento de patches do host do Azure Service Fabric clusters; O aplicativo de orquestração de patch é uma solução alternativa destinada a quando hospedado fora do Azure, embora POA possa ser usado no Azure, com sobrecarga de Hospedagem de POA no Azure sendo um motivo comum para preferir a atualização automática do sistema operacional da máquina virtual em POA. A seguir estão as propriedades do modelo do Gerenciador de recursos do conjunto de dimensionamento de máquinas virtuais de computação para habilitar a atualização automática do sistema operacional:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Ao usar atualizações automáticas do sistema operacional com o Service Fabric, a nova imagem do sistema operacional é distribuída em um domínio de atualização por vez para manter a alta disponibilidade dos serviços em execução no Service Fabric. Para utilizar atualizações automáticas do sistema operacional no Service Fabric, o cluster deve ser configurado para usar a camada de durabilidade Prata ou superior.

Verifique se a seguinte chave do registro está definida como false para impedir que suas máquinas host do Windows iniciem atualizações não coordenadas: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

A seguir estão as propriedades do modelo do Gerenciador de recursos do conjunto de dimensionamento de máquinas virtuais de computação para definir a chave do registro do WindowsUpdate como false:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Configuração de atualização de cluster do Azure Service Fabric
A seguir está a Service Fabric propriedade de modelo do Gerenciador de recursos de cluster para habilitar a atualização automática:
```json
"upgradeMode": "Automatic",
```
Para atualizar manualmente o cluster, baixe a distribuição cab/Deb para uma máquina virtual de cluster e, em seguida, invoque o PowerShell a seguir:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Próximas etapas

* Crie um cluster em VMs ou em computadores que estejam executando o Windows Server: [Criação de um cluster do Service Fabric para o Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Criar um cluster em VMs ou em computadores que estejam executando o Linux: [Criar um cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md)
