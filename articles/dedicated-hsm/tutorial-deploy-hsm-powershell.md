---
title: Tutorial – Implantar em uma rede virtual existente usando o PowerShell – HSM Dedicado do Azure | Microsoft Docs
description: Tutorial mostrando como implantar um HSM dedicado usando o PowerShell em uma rede virtual existente
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 5ed5ac90f446f74c54488f6d0cf23adbd63a3e1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606871"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Tutorial – Implantando HSMs em uma rede virtual existente usando o PowerShell

O serviço HSM Dedicado do Azure fornece um dispositivo físico para uso exclusivo do cliente, com controle administrativo e responsabilidade de gerenciamento completos. Como a Microsoft fornece o hardware, ela precisa controlar como esses dispositivos são alocados para fazer com que a capacidade seja gerenciada com eficiência. Como resultado, dentro de uma assinatura do Azure, o serviço HSM dedicado normalmente não estará visível para o provisionamento de recursos. Qualquer cliente do Azure que precise de acesso ao serviço de HSM dedicado deve, primeiro, entrar em contato com o responsável pela conta da Microsoft para solicitar o registro no serviço. O provisionamento será possível depois que esse processo tiver sido concluído com êxito.
Este tutorial tem como objetivo mostrar um processo de provisionamento típico no qual:

- Um cliente já tem uma rede virtual
- Já tem uma máquina virtual
- E precisa adicionar recursos de HSM ao ambiente existente.

Uma arquitetura de implantação típica, com várias regiões e com alta disponibilidade deve se parecer com isto:

![implantação com várias regiões](media/tutorial-deploy-hsm-powershell/high-availability.png)

Este tutorial se concentra na integração de alguns HSMs e do Gateway do ExpressRoute necessários (consulte a Sub-rede 1 acima) a uma rede virtual existente (consulte a VNET 1 acima).  Todos os outros recursos são recursos padrão do Azure. O mesmo processo de integração pode ser usado para HSMs na sub-rede 4 na VNET 3 ou acima.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Atualmente, o HSM Dedicado do Azure não está disponível no portal do Azure e, portanto, toda a interação com o serviço será pela linha de comando ou usando o PowerShell. Este tutorial usará o PowerShell no Azure Cloud Shell. Se você for novo no PowerShell, execute as instruções de introdução aqui: [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

Suposições:

- Você passou pelo processo de registro do HSM Dedicado do Azure e o uso do serviço foi aprovado. Se não for o caso, entre em contato com seu representante da conta Microsoft para obter detalhes. 
- Você criou um Grupo de Recursos para esses recursos, e os novos que foram implantados neste tutorial serão incluídos nesse grupo.
- Você já criou a rede virtual, a sub-rede e as máquinas virtuais necessárias de acordo com o diagrama acima e agora deseja integrar dois HSMs à implantação.

Todas as instruções abaixo pressupõem que você já navegou até o portal do Azure e abriu o Cloud Shell (selecione "\>\_" na canto superior direito do portal).

## <a name="provisioning-a-dedicated-hsm"></a>Provisionar um HSM Dedicado

O provisionamento dos HSMs e a integração deles a uma rede virtual existente usando o Gateway do ExpressRoute serão validados pela ferramenta de linha de comando SSH para garantir a acessibilidade e a disponibilidade básica do dispositivo HSM para futuras atividades de configuração. Os comandos a seguir usarão um modelo do Resource Manager para criar os recursos de HSM e os recursos de rede associados.

### <a name="validating-feature-registration"></a>Validar o registro de recursos

Conforme mencionado anteriormente, qualquer atividade de provisionamento requer que o serviço HSM Dedicado esteja registrado na sua assinatura. Para validá-lo, execute o comando do PowerShell a seguir no Cloud Shell do portal do Azure. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

O comando deve retornar um status “Registered” (como mostrado abaixo) antes de prosseguir.  Caso você não esteja registrado para esse serviço, entre em contato com o seu representante da conta Microsoft.

![status da assinatura](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>Criar recursos do HSM

Um dispositivo HSM é provisionado em uma rede virtual dos clientes. Isso implica a necessidade de uma sub-rede. A dependência para que o HSM habilite a comunicação entre a rede virtual e o dispositivo físico é um Gateway do ExpressRoute; por fim, é necessária uma máquina virtual para acessar o dispositivo HSM usando o software cliente da Thales. Esses recursos foram coletados em um arquivo de modelo, com o arquivo de parâmetros correspondente, para facilitar o uso. Os arquivos poderão ser disponibilizados diretamente pela Microsoft mediante pedido por email para HSMrequest@Microsoft.com.

Assim que tiver os arquivos, você deverá editar o arquivo de parâmetros a fim de inserir seus nomes preferenciais para os recursos. Isso significa editar linhas com “valor”: “”.

- `namingInfix` Prefixo para nomes de recursos do HSM
- `ExistingVirtualNetworkName` Nome da rede virtual usada para os HSMs
- `DedicatedHsmResourceName1` Nome do recurso do HSM no carimbo 1 do datacenter
- `DedicatedHsmResourceName2` Nome do recurso do HSM no carimbo 2 do datacenter
- `hsmSubnetRange` Intervalo de endereços IP de sub-rede para HSMs
- `ERSubnetRange` Intervalo de endereços IP de sub-rede para o gateway de VNET

Um exemplo dessas alterações é apresentado abaixo:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

O arquivo de modelo do Resource Manager associado cria seis recursos com estas informações:

- Uma sub-rede para os HSMs na VNET especificada
- Uma sub-rede para o gateway de rede virtual 
- Um gateway de rede virtual que conecta a VNET aos dispositivos HSM
- Um endereço IP público para o gateway
- Um HSM no carimbo 1
- Um HSM no carimbo 2

Assim que os valores de parâmetro estiverem definidos, os arquivos precisarão ser carregados no compartilhamento de arquivos de Cloud Shell do portal do Azure para serem usados. No portal do Azure, clique no símbolo "\>\_" do Cloud Shell no canto superior direito, o que transformará a parte inferior da tela em um ambiente de comando. As opções para isso são BASH e PowerShell, sendo que você deve selecionar BASH caso ainda não o tenha feito.

O shell de comando tem uma opção de upload/download na barra de ferramentas, e você deve selecionar essa opção para fazer o upload dos arquivos de modelo e parâmetros para seu compartilhamento de arquivos:

![compartilhamento de arquivos](media/tutorial-deploy-hsm-powershell/file-share.png)

Assim que os arquivos forem carregados, você poderá criar recursos.
Antes da criação de novos recursos do HSM, alguns recursos de pré-requisito devem estar em uso. Você deve ter uma rede virtual com intervalos de sub-rede para computação, HSMs e gateway. Os comandos a seguir servem como exemplo do que criaria essa rede virtual.

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>A configuração mais importante a ser observada em relação à rede virtual é que a sub-rede do dispositivo HSM deve ter as delegações definidas como “Microsoft.HardwareSecurityModules/dedicatedHSMs”.  O provisionamento de HSM não funcionará sem isso.

Assim que todos os pré-requisitos forem atendidos, execute o comando abaixo para usar o modelo do Resource Manager depois de atualizar os valores com seus nomes exclusivos (pelo menos o nome do grupo de recursos):

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Esse comando deve levar aproximadamente 20 minutos para ser concluído. A opção “-verbose” usada faz com que o status seja exibido continuamente.

![status do provisionamento](media/tutorial-deploy-hsm-powershell/progress-status.png)

Quando concluído com êxito, exibido como “provisioningState”: “Succeeded”, você poderá entrar em sua máquina virtual existente e usar o SSH para garantir a disponibilidade do dispositivo HSM.

## <a name="verifying-the-deployment"></a>Verificando a implantação

Para verificar os dispositivos que foram provisionados e ver os atributos do dispositivo, execute o conjunto de comandos a seguir. Verifique se o grupo de recursos está definido corretamente e se o nome do recurso é exatamente igual ao que você tem no arquivo de parâmetros.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![status do provisionamento](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Agora você também pode ver os recursos usando o [Azure Resource Explorer](https://resources.azure.com/).   Uma vez no Explorer, expanda “Assinaturas” à esquerda, expanda sua assinatura específica do HSM Dedicado, expanda “grupos de recursos”, expanda o grupo de recursos que você usou e, por fim, selecione o item “recursos”.

## <a name="testing-the-deployment"></a>Testar a implantação

Para o teste da implantação, você se conecta a uma máquina virtual que pode acessar o HSM(s) para depois se conectar diretamente ao dispositivo HSM. Essas ações confirmarão que o HSM está acessível.
A ferramenta SSH é usada para se conectar à máquina virtual. O comando será semelhante ao apresentado a seguir, mas com o nome de administrador e o nome DNS especificados no parâmetro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

A senha a ser usada é a do arquivo de parâmetros.
Depois de fazer logon na VM do Linux, você pode fazer logon no HSM usando o endereço IP encontrado no portal para o recurso \<prefix>hsm_vnic.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Quando você obtiver o endereço IP, execute o seguinte comando:

`ssh tenantadmin@<ip address of HSM>`

Se tiver êxito, você receberá uma solicitação de senha. A senha padrão é PASSWORD. O HSM solicitará que você altere sua senha, ou seja, defina uma senha forte e use o mecanismo preferido de sua organização para armazenar a senha e evitar sua perda.  

>[!IMPORTANT]
>Se você perder a senha, o HSM precisará ser redefinido, o que significa que você perderá as chaves.

Quando estiver conectado ao dispositivo HSM usando SSH, execute o comando a seguir para ter certeza de que o HSM está funcionando.

`hsm show`

A saída deve se parecer com a imagem abaixo:

![Captura de tela que mostra a saída do comando hsm show.](media/tutorial-deploy-hsm-powershell/output.png)

A essa altura, você alocou todos os recursos para ter uma implantação de dois HSMs altamente disponíveis e validou o acesso e o estado operacional. Outras configurações ou testes envolvem mais trabalho com o próprio dispositivo HSM. Para isso, siga as instruções do capítulo 7 do Guia de Administração do Thales Luna 7 HSM para inicializar o HSM e criar partições. Toda a documentação e o software estarão disponíveis para download diretamente na Thales depois que você se registrar no [portal de suporte ao cliente da Thales](https://supportportal.thalesgroup.com/csm) e tiver uma ID do cliente. Baixe o software cliente versão 7.2 para obter todos os componentes necessários.

## <a name="delete-or-clean-up-resources"></a>Excluir ou limpar recursos

Caso tenha terminado de usar somente o dispositivo HSM, ele poderá ser excluído como um recurso e devolvido ao pool gratuito. O motivo óbvio de preocupação aqui diz respeito a dados confidenciais de clientes que estejam no dispositivo. A melhor maneira de "zerar" um dispositivo é inserir a senha incorreta do administrador do HSM três vezes (observação: esse não é o administrador do dispositivo, é o administrador real do HSM). Como medida de segurança para proteger o material da chave, o dispositivo não pode ser excluído como um recurso do Azure até que esteja no estado zerado.

> [!NOTE]
> Caso tenha problema com alguma configuração do dispositivo Thales, entre em contato com o [atendimento ao cliente da Thales](https://supportportal.thalesgroup.com/csm).

Caso deseje remover o recurso HSM do Azure, use o seguinte comando, substituindo as variáveis "$" pelos parâmetros exclusivos:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Próximas etapas

Depois de concluir as etapas do tutorial, os recursos do HSM Dedicado serão provisionados e ficarão disponíveis em sua rede virtual. Agora você pode complementar essa implantação com mais recursos, conforme exigido pela sua arquitetura de implantação preferencial. Para saber mais sobre como ajudar a planejar sua implantação, confira os documentos de Conceitos. Recomendamos o uso de um design com dois HSMs em uma região primária abordando a disponibilidade no nível do rack e dois HSMs em uma região secundária abordando a disponibilidade regional. O arquivo de modelo usado neste tutorial pode ser usado facilmente como base para uma implantação de dois HSM, mas precisa ter seus parâmetros modificados para atender às suas necessidades.

* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)
* [Monitoring](monitoring.md)
* [Capacidade de suporte](supportability.md)
