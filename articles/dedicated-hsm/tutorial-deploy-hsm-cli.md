---
title: Tutorial – Implantar em uma rede virtual existente usando a CLI do Azure – HSM Dedicado do Azure | Microsoft Docs
description: Tutorial mostrando como implantar um HSM dedicado usando a CLI em uma rede virtual existente
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: fa1c01c2d9da19ec1f60878de83a509b7cf561e8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606820"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>Tutorial: Implantar HSMs em uma rede virtual existente usando a CLI do Azure

O HSM dedicado do Azure fornece um dispositivo físico para uso exclusivo do cliente, com controle administrativo e responsabilidade de gerenciamento completos. O uso de dispositivos físicos faz com que a Microsoft precise controlar a alocação de dispositivos para gerenciar a capacidade com eficiência. Como resultado, dentro de uma assinatura do Azure, o serviço HSM dedicado normalmente não estará visível para o provisionamento de recursos. Qualquer cliente do Azure que precise de acesso ao serviço de HSM dedicado deve, primeiro, entrar em contato com o responsável pela conta da Microsoft para solicitar o registro no serviço. O provisionamento será possível depois que esse processo tiver sido concluído com êxito. 

Este tutorial mostra um processo de provisionamento típico no qual:

- Um cliente já tem uma rede virtual
- Já tem uma máquina virtual
- E precisa adicionar recursos de HSM ao ambiente existente.

Uma arquitetura de implantação típica, com várias regiões e com alta disponibilidade deve se parecer com isto:

![implantação com várias regiões](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Este tutorial se concentra na integração de HSMs e do Gateway do ExpressRoute exigido (consulte a Sub-rede 1 acima) a uma rede virtual existente (consulte a VNET 1 acima).  Todos os outros recursos são recursos padrão do Azure. O mesmo processo de integração pode ser usado para HSMs na sub-rede 4 na VNET 3 ou acima.

## <a name="prerequisites"></a>Pré-requisitos

O HSM dedicado do Azure não está disponível atualmente no portal do Azure. Toda a interação com o serviço ocorrerá por meio da linha de comando ou usando o PowerShell. Este tutorial usará a CLI (interface de linha de comando) no Azure Cloud Shell. Se você estiver familiarizado com a CLI do Azure, siga estas instruções de introdução: [Introdução à CLI do Azure 2.0](/cli/azure/get-started-with-azure-cli).

Suposições:

- Você concluiu o processo de registro do HSM dedicado do Azure
- Você recebeu aprovação para usar o serviço. Se não for o caso, entre em contato com seu representante da conta Microsoft para obter detalhes.
- Você criou um Grupo de Recursos para esses recursos, e os novos que foram implantados neste tutorial serão incluídos nesse grupo.
- Você já criou a rede virtual, a sub-rede e as máquinas virtuais necessárias de acordo com o diagrama acima e agora deseja integrar dois HSMs à implantação.

Todas as instruções abaixo pressupõem que você já navegou até o portal do Azure e abriu o Cloud Shell (selecione "\>\_" no canto superior direito do portal).

## <a name="provisioning-a-dedicated-hsm"></a>Provisionar um HSM Dedicado

O provisionamento de HSMs e a integração deles a uma rede virtual existente por meio do Gateway do ExpressRoute serão validados usando SSH. Essa validação ajuda a garantir a acessibilidade e a disponibilidade básica do dispositivo HSM para atividades de configuração futuras.

### <a name="validating-feature-registration"></a>Validar o registro de recursos

Conforme mencionado anteriormente, qualquer atividade de provisionamento requer que o serviço HSM Dedicado esteja registrado na sua assinatura. Para validá-lo, execute os comandos a seguir no Cloud Shell do portal do Azure.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Os comandos devem retornar um status "Registrado" (conforme mostrado abaixo). Se os comandos não retornarem "Registrado", você precisará se registrar nesse serviço entrando em contato com seu representante da conta Microsoft.

![status da assinatura](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Criar recursos do HSM

Antes de criar recursos do HSM, há alguns recursos de pré-requisito necessários. Você deve ter uma rede virtual com intervalos de sub-rede para computação, HSMs e gateway. Os comandos a seguir servem como exemplo do que criaria essa rede virtual.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>A configuração mais importante a ser observada em relação à rede virtual é que a sub-rede do dispositivo HSM deve ter as delegações definidas como "Microsoft.HardwareSecurityModules/dedicatedHSMs".  O provisionamento de HSM não funcionará sem isso.

Depois de configurar sua rede, use estes comandos da CLI do Azure para provisionar seus HSMs.

1. Use o comando [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) para provisionar o primeiro HSM. O HSM é chamado de hsm1. Substitua sua assinatura:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   Essa implantação deve levar aproximadamente de 25 a 30 minutos para ser concluída; a maior parte desse tempo deve ser consumida pelos dispositivos HSM.

1. Para ver um HSM atual, execute o comando [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show):

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. Provisione o segundo HSM usando este comando:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. Execute o comando [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) para ver detalhes sobre seus HSMs atuais:

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

Há alguns outros comandos que podem ser úteis. Use o comando [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) para atualizar um HSM:

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

Para excluir um HSM, use o comando [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete):

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>Verificando a implantação

Para verificar os dispositivos que foram provisionados e ver os atributos do dispositivo, execute o conjunto de comandos a seguir. Verifique se o grupo de recursos está definido corretamente e se o nome do recurso é exatamente igual ao que você tem no arquivo de parâmetros.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

A saída será semelhante ao seguinte:

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

Agora você também pode ver os recursos usando o [Azure Resource Explorer](https://resources.azure.com/).   Uma vez no Explorer, expanda "Assinaturas" à esquerda, expanda sua assinatura específica do HSM Dedicado, expanda "grupos de recursos", expanda o grupo de recursos que você usou e, por fim, selecione o item "recursos".

## <a name="testing-the-deployment"></a>Testar a implantação

Para o teste da implantação, você se conecta a uma máquina virtual que pode acessar o HSM(s) para depois se conectar diretamente ao dispositivo HSM. Essas ações confirmarão que o HSM está acessível.
A ferramenta SSH é usada para se conectar à máquina virtual. O comando será semelhante ao apresentado a seguir, mas com o nome de administrador e o nome DNS especificados no parâmetro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

O endereço IP da VM também pode ser usado no lugar do nome DNS no comando acima. Se o comando for bem-sucedido, ele solicitará uma senha; insira essa senha. Depois de fazer logon na máquina virtual, você poderá entrar no HSM usando o endereço IP encontrado no portal para o recurso de adaptador de rede associado ao HSM.

![lista de componentes](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Observe a caixa de seleção "Mostrar tipos ocultos", que, quando selecionada, exibirá recursos do HSM.

Na captura de tela acima, um clique em "HSM1_HSMnic" ou "HSM2_HSMnic" mostraria o endereço IP privado pertinente. Caso contrário, o comando `az resource show` usado acima é outra forma de identificar o endereço IP correto. 

Quando você tiver o endereço IP correto, execute o seguinte comando substituindo o endereço:

`ssh tenantadmin@10.0.2.4`

Se tiver êxito, você receberá uma solicitação de senha. A senha padrão é PASSWORD; primeiro, o HSM solicitará que você altere sua senha, ou seja, defina uma senha forte e use o mecanismo preferido de sua organização para armazenar a senha e evitar sua perda.

>[!IMPORTANT]
>Se você perder a senha, o HSM precisará ser redefinido, o que significa que você perderá as chaves.

Quando estiver conectado ao HSM usando SSH, execute o comando a seguir para ter certeza de que o HSM está funcionando.

`hsm show`

A saída deve se parecer com a mostrada na imagem abaixo:

![A captura de tela mostra a saída na janela do PowerShell.](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

A essa altura, você alocou todos os recursos para ter uma implantação de dois HSMs altamente disponíveis e validou o acesso e o estado operacional. Outras configurações ou testes envolvem mais trabalho com o próprio dispositivo HSM. Para isso, siga as instruções do capítulo 7 do Guia de Administração do Thales Luna 7 HSM para inicializar o HSM e criar partições. Toda a documentação e o software estarão disponíveis para download diretamente na Thales depois que você se registrar no [portal de suporte ao cliente da Thales](https://supportportal.thalesgroup.com/csm) e tiver uma ID do cliente. Baixe o software cliente versão 7.2 para obter todos os componentes necessários.

## <a name="delete-or-clean-up-resources"></a>Excluir ou limpar recursos

Caso tenha terminado de usar somente o dispositivo HSM, ele poderá ser excluído como um recurso e devolvido ao pool gratuito. O motivo óbvio de preocupação aqui diz respeito a dados confidenciais de clientes que estejam no dispositivo. A melhor maneira de "zerar" um dispositivo é inserir a senha incorreta do administrador do HSM três vezes (observação: esse não é o administrador do dispositivo, é o administrador real do HSM). Como medida de segurança para proteger o material da chave, o dispositivo não pode ser excluído como um recurso do Azure até que esteja no estado zerado.

> [!NOTE]
> Caso tenha problema com alguma configuração do dispositivo Thales, entre em contato com o [atendimento ao cliente da Thales](https://supportportal.thalesgroup.com/csm).

Ao concluir o uso de todos os recursos desse grupo de recursos, remova-os com o seguinte comando:

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>Próximas etapas

Após a conclusão das etapas do tutorial, os recursos do HSM dedicado terão sido provisionados e você terá uma rede virtual com os HSMs necessários, além de outros componentes de rede que permitem a comunicação com o HSM.  Agora você pode complementar essa implantação com mais recursos, conforme exigido pela sua arquitetura de implantação preferencial. Para saber mais sobre como ajudar a planejar sua implantação, confira os documentos de Conceitos.
Recomendamos o uso de um design com dois HSMs em uma região primária abordando a disponibilidade no nível do rack e dois HSMs em uma região secundária abordando a disponibilidade regional. 

* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitoring](monitoring.md)
