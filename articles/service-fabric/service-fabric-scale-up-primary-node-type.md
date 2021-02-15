---
title: Escalar verticalmente um tipo de nó primário Service Fabric do Azure
description: Dimensione verticalmente seu cluster Service Fabric adicionando um novo tipo de nó e removendo o anterior.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251158"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Dimensionar um tipo de nó primário do cluster do Service Fabric

Este artigo descreve como escalar verticalmente um tipo de nó primário de Cluster Service Fabric com tempo de inatividade mínimo. A estratégia geral para atualizar um tipo de nó de Cluster Service Fabric é:

1. Adicione um novo tipo de nó ao Cluster Service Fabric, apoiado pela sua configuração e SKU do conjunto de dimensionamento de máquinas virtuais (ou modificado). Essa etapa também envolve a configuração de um novo balanceador de carga, sub-rede e IP público para o conjunto de dimensionamento.

1. Depois que os conjuntos de dimensionamento original e atualizado estiverem em execução lado a lado, desabilite as instâncias de nó originais uma de cada vez para que os serviços do sistema (ou réplicas dos serviços com estado) migrem para o novo conjunto de dimensionamento.

1. Verifique se o cluster e os novos nós estão íntegros e, em seguida, remova o conjunto de dimensionamento original (e os recursos relacionados) e o estado do nó para os nós excluídos.

O seguinte guiará você pelo processo de atualização do tamanho da VM e do sistema operacional de VMs do tipo de nó primário de um cluster de exemplo com [durabilidade prateada](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), apoiada por um único conjunto de dimensionamento com cinco nós. Atualizaremos o tipo de nó primário:

- Do tamanho da VM *Standard_D2_V2* à *D4_V2 padrão* e
- No sistema operacional da VM, o *Windows server 2016 datacenter com contêineres* para o *Windows Server 2019 datacenter com contêineres*.

> [!WARNING]
> Antes de tentar esse procedimento em um cluster de produção, é recomendável que você estude os modelos de exemplo e verifique o processo em um cluster de teste. O cluster também pode estar indisponível por um curto período de tempo.
>
> Não tente um procedimento de expansão do tipo de nó primário se o status do cluster não estiver íntegro, pois isso desestabilizará ainda mais o cluster.

Aqui estão os modelos de implantação passo a passo do Azure que usaremos para concluir este cenário de atualização de exemplo: https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>Configurar o cluster de teste

Vamos configurar o cluster inicial de teste de Service Fabric. Primeiro, [Baixe](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) o Azure Resource Manager modelos de exemplo que usaremos para concluir este cenário.

Em seguida, entre na sua conta do Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Em seguida, abra o [*parameters.jsno*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) arquivo e atualize o valor de `clusterName` para algo exclusivo (no Azure).

Os comandos a seguir irão orientá-lo na geração de um novo certificado autoassinado e na implantação do cluster de teste. Se você já tiver um certificado que deseja usar, pule para [usar um certificado existente para implantar o cluster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Gerar um certificado autoassinado e implantar o cluster

Primeiramente, atribua as variáveis necessárias para a implantação do cluster do Service Fabric. Ajuste os valores de `resourceGroupName`, `certSubjectName`, `parameterFilePath` e `templateFilePath` da sua conta e ambiente específicos:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> Verifique se o `certOutputFolder` local existe no computador local antes de executar o comando para implantar um novo cluster Service Fabric.

Em seguida, implante o cluster de teste do Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Quando a implantação for concluída, localize o arquivo *.pfx* (`$certPfx`) no seu computador local e importe-o para o repositório de certificados:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

A operação retornará a impressão digital do certificado, que agora você pode usar para [se conectar ao novo cluster](#connect-to-the-new-cluster-and-check-health-status) e verificar seu status de integridade. (Pule a seção a seguir, que é uma abordagem alternativa para a implantação de cluster.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Usar um certificado existente para implantar o cluster

Como alternativa, você pode usar um certificado de Azure Key Vault existente para implantar o cluster de teste. Para fazer isso, você precisará [obter referências ao seu Key Vault e à](#obtain-your-key-vault-references) impressão digital do certificado.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Em seguida, designe um nome de grupo de recursos para o cluster e defina os `templateFilePath` `parameterFilePath` locais e:

> [!NOTE]
> O grupo de recursos designado já deve existir e estar localizado na mesma região que o seu Key Vault.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

Por fim, execute o seguinte comando para implantar o cluster de teste inicial:

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Conectar-se ao novo cluster e verificar o status da integridade

Conecte-se ao cluster e verifique se todos os cinco nós estão íntegros (substitua `clusterName` as `thumb` variáveis e pelos seus próprios valores):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Com isso, estamos prontos para iniciar o procedimento de atualização.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>Implantar um novo tipo de nó primário com o conjunto de dimensionamento atualizado

Para atualizar (dimensionar verticalmente) um tipo de nó, primeiro precisaremos implantar um novo tipo de nó apoiado por um novo conjunto de dimensionamento e recursos de suporte. O novo conjunto de dimensionamento será marcado como primário ( `isPrimary: true` ), assim como o conjunto de dimensionamento original (a menos que você esteja fazendo uma atualização de tipo de nó não primário). Os recursos criados na seção a seguir serão, em última instância, o novo tipo de nó primário no cluster e os recursos do tipo de nó primário original serão excluídos.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Atualizar o modelo de cluster com o conjunto de dimensionamento atualizado

Aqui estão as modificações seção a seção do modelo de implantação de cluster original para adicionar um novo tipo de nó primário e recursos de suporte.

As alterações necessárias para esta etapa já foram feitas para você na [*Step1-AddPrimaryNodeType.jsno*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) arquivo de modelo, e o seguinte explicará essas alterações em detalhes. Se preferir, você pode ignorar a explicação e continuar a [obter suas referências de Key Vault](#obtain-your-key-vault-references) e [implantar o modelo atualizado](#deploy-the-updated-template) que adiciona um novo tipo de nó primário ao cluster.

> [!Note]
> Certifique-se de usar nomes que sejam exclusivos do tipo de nó original, conjunto de dimensionamento, balanceador de carga, IP público e sub-rede do tipo de nó primário original, pois esses recursos serão excluídos em uma etapa posterior no processo.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>Criar uma nova sub-rede na rede virtual existente

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>Criar um novo IP público com um domainNameLabel exclusivo

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>Criar um novo balanceador de carga para o IP público

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>Criar um novo conjunto de dimensionamento de máquinas virtuais (com SKUs de VM e de so atualizados)

Referência de tipo de nó

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

SKU da VM

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

SKU DO SO

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Além disso, certifique-se de incluir extensões adicionais que são necessárias para sua carga de trabalho.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Adicionar um novo tipo de nó primário ao cluster

Agora que o novo tipo de nó (vmNodeType1Name) tem seu próprio nome, sub-rede, IP, balanceador de carga e conjunto de dimensionamento, ele pode reutilizar todas as outras variáveis do tipo de nó original (como `nt0applicationEndPort` , `nt0applicationStartPort` e `nt0fabricTcpGatewayPort` ):

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

Depois de implementar todas as alterações em seus arquivos de modelo e de parâmetros, vá para a próxima seção para adquirir suas referências de Key Vault e implantar as atualizações no cluster.

### <a name="obtain-your-key-vault-references"></a>Obter suas referências de Key Vault

Para implantar a configuração atualizada, você precisará de várias referências ao certificado de cluster armazenado em seu Key Vault. A maneira mais fácil de encontrar esses valores é por meio de portal do Azure. Você precisará do seguinte:

* **A URL de Key Vault do seu certificado de cluster.** Em seu Key Vault em portal do Azure, selecione **certificados**  >  *o*  >  **identificador secreto** do certificado desejado:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **A impressão digital do seu certificado de cluster.** (Provavelmente você já terá isso se estiver [conectado ao cluster inicial](#connect-to-the-new-cluster-and-check-health-status) para verificar seu status de integridade.) Na mesma folha de certificado (**certificados**  >  *seu certificado desejado*) em portal do Azure, copie **a impressão digital X. 509 SHA-1 (em Hex)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **A ID de recurso do seu Key Vault.** Em seu Key Vault em portal do Azure, selecione **Propriedades**  >  **ID do recurso**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado

Ajuste o `templateFilePath` conforme necessário e execute o seguinte comando:

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Quando a implantação for concluída, verifique a integridade do cluster novamente e verifique se todos os nós em ambos os tipos de nó estão íntegros.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>Migrar nós de semente para o novo tipo de nó

Agora estamos prontos para atualizar o tipo de nó original como não primário e começar a desabilitar seus nós. À medida que os nós são desabilitados, os serviços do sistema do cluster e os nós de propagação migram para o novo conjunto de dimensionamento.

### <a name="unmark-the-original-node-type-as-primary"></a>Desmarcar o tipo de nó original como primário

Primeiro, remova a `isPrimary` designação no modelo do tipo de nó original.

```json
{
    "isPrimary": false,
}
```

Em seguida, implante o modelo com a atualização. Isso iniciará a migração de nós de semente para o novo conjunto de dimensionamento.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> Levará algum tempo para concluir a migração do nó semente para o novo conjunto de dimensionamento. Para garantir a consistência dos dados, apenas um nó semente pode ser alterado por vez. Cada alteração de nó semente requer uma atualização do cluster. Portanto, a substituição de um nó semente exige duas atualizações do cluster (uma para adição e outra para remoção do nó). Atualizar os cinco nós semente neste cenário de exemplo resultará em dez atualizações do cluster.

Use Service Fabric Explorer para monitorar a migração de nós de semente para o novo conjunto de dimensionamento. Os nós do tipo de nó original (nt0vm) devem ser *false* na coluna de **nó semente is** e os do novo tipo de nó (nt1vm) serão *verdadeiros*.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>Desabilitar os nós no conjunto de dimensionamento do tipo de nó original

Depois que todos os nós de semente forem migrados para o novo conjunto de dimensionamento, você poderá desabilitar os nós do conjunto de dimensionamento original.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

Use Service Fabric Explorer para monitorar a progressão de nós no conjunto de dimensionamento original, desde a *desabilitação* até o status *desabilitado* .

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="Service Fabric Explorer mostrando o status dos nós desabilitados":::

Para durabilidade prata e ouro, alguns nós entrarão em estado desabilitado, enquanto outros podem permanecer em um estado de *desabilitação* . Em Service Fabric Explorer, verifique a guia **detalhes** dos nós no estado desabilitando. Se eles mostrarem uma *verificação de segurança pendente* do tipo *EnsurePartitionQuorem* (garantindo o quorum para partições de serviço de infraestrutura), será seguro continuar.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="Você pode continuar com a interrupção de dados e a remoção de nós presos no status ' desabilitando ' se eles mostrarem uma verificação de segurança pendente de tipo ' EnsurePartitionQuorum '.":::

Se o cluster for de durabilidade bronze, aguarde até que todos os nós atinjam o estado *desabilitado* .

### <a name="stop-data-on-the-disabled-nodes"></a>Parar dados nos nós desabilitados

Agora você pode parar os dados nos nós desabilitados.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>Remover o tipo de nó original e limpar seus recursos

Estamos prontos para remover o tipo de nó original e seus recursos associados para concluir o procedimento de dimensionamento vertical.

### <a name="remove-the-original-scale-set"></a>Remover o conjunto de dimensionamento original

Primeiro, remova o conjunto de dimensionamento de backup do tipo de nó.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>Excluir o IP original e os recursos do balanceador de carga

Agora você pode excluir o IP original e os recursos do balanceador de carga. Nesta etapa, você também atualizará o nome DNS.

> [!Note]
> Esta etapa é opcional se você já estiver usando um IP público de SKU *padrão* e um balanceador de carga. Nesse caso, você poderia ter vários conjuntos de dimensionamento/tipos de nós no mesmo balanceador de carga.

Execute os comandos a seguir, modificando o `$lbname` valor conforme necessário.

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>Remover estado do nó do tipo de nó original

Os nós do tipo de nó original agora mostrarão *erro* para seu **estado de integridade**. Remova o estado do nó do cluster.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer agora deve refletir apenas os cinco nós do novo tipo de nó (nt1vm), tudo com os valores de estado de integridade *OK*. O estado de integridade do cluster ainda mostrará um *erro*. Corrigiremos isso em seguida atualizando o modelo para refletir as alterações e reimplantação mais recentes.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>Atualizar o modelo de implantação para refletir o tipo de nó primário dimensionado recentemente

As alterações necessárias para esta etapa já foram feitas para você na [*Step3-CleanupOriginalPrimaryNodeType.jsno*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) arquivo de modelo, e as seções a seguir explicarão essas alterações de modelo em detalhes. Se preferir, você pode ignorar a explicação e continuar a [implantar o modelo atualizado](#deploy-the-finalized-template) e concluir o tutorial.

#### <a name="update-the-cluster-management-endpoint"></a>Atualizar o ponto de extremidade de gerenciamento de cluster

Atualize o cluster `managementEndpoint` no modelo de implantação para fazer referência ao novo IP (Atualizando *VmNodeType0Name* com *vmNodeType1Name*).

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>Remover a referência de tipo de nó original

Remova a referência do tipo de nó original do recurso Service Fabric no modelo de implantação:

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>Configurar políticas de integridade para ignorar erros existentes

Somente para clusters de durabilidade e mais alta, atualize o recurso de cluster no modelo e configure as políticas de integridade para ignorar a `fabric:/System` integridade do aplicativo adicionando *applicationDeltaHealthPolicies* em Propriedades de recurso de cluster, conforme indicado abaixo. A política abaixo irá ignorar os erros existentes, mas não permitir novos erros de integridade.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>Remover recursos de suporte para o tipo de nó original

Remova todos os outros recursos relacionados ao tipo de nó original do modelo ARM e do arquivo de parâmetros. Exclua:

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>Implantar o modelo finalizado

Por fim, implante o modelo de Azure Resource Manager modificado.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> Essa etapa levará um tempo, geralmente de até duas horas.

A atualização irá alterar as configurações para o *InfrastructureService*; Portanto, é necessária uma reinicialização de nó. Nesse caso, *forceRestart* é ignorado. O parâmetro `upgradeReplicaSetCheckTimeout` especifica o tempo máximo que Service Fabric aguarda até que uma partição esteja em um estado seguro, se ainda não estiver em um estado seguro. Depois que as verificações de segurança forem aprovadas para todas as partições em um nó, Service Fabric continuará com a atualização nesse nó. O valor do parâmetro `upgradeTimeout` pode ser reduzido para 6 horas, mas para a segurança máxima 12 horas deve ser usada.

Após a conclusão da implantação, verifique portal do Azure se o status do recurso Service Fabric está *pronto*. Verifique se você pode acessar o novo ponto de extremidade Service Fabric Explorer, se o **estado de integridade do cluster** está *OK* e se todos os aplicativos implantados funcionam corretamente.

Com isso, você dimensionou verticalmente um tipo de nó primário de cluster!

## <a name="next-steps"></a>Próximas etapas

* Saiba como [adicionar um tipo de nó a um cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre [escalabilidade de aplicativo](service-fabric-concepts-scalability.md).
* [Reduzir horizontalmente ou escalar horizontalmente um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure de forma programática](service-fabric-cluster-programmatic-scaling.md) usando a SDK fluente de computação do Azure.
* [Dimensione um cluster autônomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).
