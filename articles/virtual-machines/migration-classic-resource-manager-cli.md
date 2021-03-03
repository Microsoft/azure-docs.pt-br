---
title: Migrar VMs para o Gerenciador de recursos usando CLI do Azure
description: Este artigo percorre a migração de recursos com suporte da plataforma do clássico para o Azure Resource Manager usando CLI do Azure.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 671b27f927c91397d2aacd98cb7b500d8197d1c5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669332"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrar recursos de IaaS do modelo clássico para o Azure Resource Manager usando a CLI do Azure

> [!IMPORTANT]
> Hoje, cerca de 90% das VMs de IaaS estão usando [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partir de 28 de fevereiro de 2020, as VMs clássicas foram preteridas e serão totalmente desativadas em 1º de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre essa reprovação e [como ela afeta você](classic-vm-deprecation.md#how-does-this-affect-me).

Estas etapas mostram como usar a CLI (interface de linha de comando) do Azure para migrar recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o modelo de implantação do Azure Resource Manager. O artigo requer a [CLI clássica do Azure](/cli/azure/install-classic-cli). Como a CLI do Azure só é aplicável para recursos do Azure Resource Manager, ela não pode ser usada para essa migração.

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma repetirá a ação.
> 
> 

<br>
Este é um fluxograma para identificar a ordem em que as etapas precisam ser executadas durante um processo de migração

![Screenshot that shows the migration steps](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Etapa 1: preparar para a migração
Veja a seguir algumas das práticas que recomendamos durante a avaliação de migração dos recursos de IaaS do modelo clássico para o Gerenciador de Recursos:

* Leia a [lista de recursos ou de configurações sem suporte](migration-classic-resource-manager-overview.md). Caso você tenha máquinas virtuais que usam recursos ou configurações sem suporte, recomendamos que aguarde até que o suporte para o recurso/configuração seja anunciado. Como alternativa, é possível remover esse recurso ou mudar a configuração para habilitar a migração, caso ela atenda às suas necessidades.
* Se você tiver scripts automatizados que implantam sua infraestrutura e aplicativos atualmente, tente criar uma configuração de teste semelhante usando esses scripts para migração. Você também pode configurar ambientes de exemplo usando o portal do Azure.

> [!IMPORTANT]
> Atualmente não ha suporte para Gateways de Aplicativo para a migração do clássico para o Resource Manager. Para migrar uma rede virtual clássica com um Gateway de Aplicativo, remova o gateway antes de executar uma operação de Preparação para mover a rede. Depois de concluir a migração, reconecte o gateway no Azure Resource Manager. 
>
>Não é possível migrar automaticamente gateways de ExpressRoute conectando-se a circuitos de ExpressRoute em outra assinatura. Nesses casos, remova o gateway de ExpressRoute, migre a rede virtual e recrie o gateway. Confira [Migrar circuitos de ExpressRoute e redes virtuais associadas do modelo de implantação clássico para o Resource Manager](../expressroute/expressroute-migration-classic-resource-manager.md) para obter mais informações.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Etapa 2: Definir sua assinatura e registrar o provedor
Para cenários de migração, é necessário instalar seu ambiente tanto para o modelo clássico quanto para o Gerenciador de Recursos. [Instale a CLI do Azure](/cli/azure/install-classic-cli) e [selecione sua assinatura](/cli/azure/authenticate-azure-cli).

Entre em sua conta.

```azurecli
azure login
```

Selecione a assinatura do Azure usando o seguinte comando.

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> O registro é uma etapa única, mas é preciso executá-lo uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro 
> 
> *BadRequest: a assinatura não está registrada para migração.* 
> 
> 

Registre-se no provedor de recursos de migração usando o comando a seguir. Observe que, em alguns casos, esse comando atinge o tempo limite. No entanto, o registro será bem-sucedido.

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos para concluir o registro. É possível verificar o status da aprovação usando o comando a seguir. Verifique se RegistrationState é `Registered` antes de continuar.

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

Agora mude a CLI para o modo `asm` .

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Etapa 3: verificar se você tem uma quantidade suficiente de vCPUs de Máquina Virtual do Azure Resource Manager na região do Azure de sua implantação atual ou da VNET
Nesta etapa, você precisará alternar para o modo `arm` . Faça isso com o seguinte comando.

```azurecli
azure config mode arm
```

Você pode usar o seguinte comando de CLI do PowerShell para verificar a quantidade atual de vCPUs no Azure Resource Manager. Para saber mais sobre cotas de vCPUs, veja [Limites e o Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Quando você terminar de verificar esta etapa, volte para o modo `asm` .

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Etapa 4: Opção 1 – Migrar máquinas virtuais em um serviço de nuvem
Obtenha a lista de serviços de nuvem usando o comando a seguir e escolha o serviço de nuvem que deseja migrar. Observe que, se as VMs no serviço de nuvem estiverem em uma rede virtual ou se tiverem funções web/de trabalho, você receberá uma mensagem de erro.

```azurecli
azure service list
```

Execute a comando a seguir para obter o nome da implantação do serviço de nuvem por meio da saída detalhada. Na maioria dos casos, o nome da implantação é igual ao nome do serviço de nuvem.

```azurecli
azure service show <serviceName> -vv
```

Primeiro, valide se você pode migrar o serviço de nuvem usando os seguintes comandos:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Você tem duas opções entre as quais escolher.

Se quiser migrar as máquinas virtuais em uma rede virtual criada por plataforma, use o comando a seguir.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Se quiser migrar para uma rede virtual existente no modelo de implantação do Gerenciador de Recursos, use o comando a seguir.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

Após uma operação de preparação bem-sucedida, você poderá examinar a saída detalhada para obter o estado de migração das VMs e assegurar que as elas estejam no estado `Prepared` .

```azurecli
azure vm show <vmName> -vv
```

Verifique a configuração dos recursos preparados usando a CLI ou o portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Etapa 4: Opção 2 – Migrar máquinas virtuais em uma rede virtual
Selecione a rede virtual que você deseja migrar. Observe que, se a rede virtual contiver funções web/de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Obtenha todas as redes virtuais na assinatura usando o comando a seguir.

```azurecli
azure network vnet list
```

A saída será parecida com esta:

![Captura de tela da linha de comando com o nome inteiro da rede virtual realçado.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

No exemplo acima, **virtualNetworkName** é o nome inteiro **“Grupo classicubuntu16 classicubuntu16”**.

Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Prepare a rede virtual de sua preferência para migração usando o comando a seguir.

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

Verifique a configuração para as máquinas virtuais preparadas usando a CLI ou o portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>Etapa 5: Migrar uma conta de armazenamento
Depois de concluir a migração das máquinas virtuais, recomendamos a migração da conta de armazenamento.

Prepare a conta de armazenamento para migração usando o comando a seguir

```azurecli
azure storage account prepare-migration <storageAccountName>
```

Verifique a configuração da conta de armazenamento preparada usando a CLI ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

```azurecli
azure storage account abort-migration <storageAccountName>
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planejamento para a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Ferramentas da comunidade para ajudar com a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)
* [Confira as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md)
