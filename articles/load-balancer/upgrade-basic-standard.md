---
title: Atualização do público-Azure Load Balancer público básico para o Standard
description: Este artigo mostra como atualizar o Load Balancer público do Azure de SKU básico para SKU Standard
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 3bf910d3309285c8b700c39af68fb90715f8863a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987714"
---
# <a name="upgrade-azure-public-load-balancer"></a>Atualizar Load Balancer públicos do Azure
O [Azure Standard Load Balancer](load-balancer-overview.md) oferece um conjunto avançado de funcionalidades e alta disponibilidade por meio de redundância de zona. Para saber mais sobre Load Balancer SKU, confira [tabela de comparação](./skus.md#skus).

Há dois estágios em uma atualização:

1. Altere o método de alocação de IP de dinâmico para estático.
2. Execute o script do PowerShell para concluir a atualização e a migração de tráfego.

> [!IMPORTANT]
> O script está em manutenção no momento. Você pode consultar as instruções [aqui](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address-upgrade?tabs=option-upgrade-cli%2Coption-migrate-powershell#tabpanel_CeZOj-G++Q_option-upgrade-cli) sobre como atualizar endereços IP públicos de SKU básico e SKU Standard.

## <a name="upgrade-overview"></a>Visão geral da atualização

Há um script de Azure PowerShell disponível que faz o seguinte:

* Cria um Load Balancer de SKU padrão com o local especificado no mesmo grupo de recursos do Standard Load Balancer básico.
* Atualiza o endereço IP público do SKU básico para o SKU padrão in-loco.
* Copia diretamente as configurações do Load Balancer SKU básico para o recém-criado Standard Load Balancer.
* Cria uma regra de saída padrão que habilita a conectividade de saída.

### <a name="caveatslimitations"></a>Caveats\Limitations

* O script só dá suporte à atualização de Load Balancer pública. Para a atualização de Load Balancer básica interna, consulte [esta página](./upgrade-basicinternal-standard.md) para obter instruções.
* O método de alocação do endereço IP público deve ser alterado para "estático" antes da execução do script. 
* Se seu Load Balancer não tiver nenhuma configuração de IP de front-end ou pool de back-ends, provavelmente você encontrará um erro ao executar o script. Verifique se eles não estão vazios.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Alterar o método de alocação do endereço IP público para estático

* * * Aqui estão nossas etapas recomendadas:

    1. Para realizar todas as tarefas deste início rápido, entre no [portal do Azure](https://portal.azure.com).
 
    1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione o **endereço IP público básico associado ao Load Balancer básico** na lista de recursos.
   
    1. Em **configurações**, selecione **configurações**.
   
    1. Em **Atribuição**, selecione **Estático**.
    1. Selecione **Salvar**.
    >[!NOTE]
    >Para VMs que têm IPs públicos, você precisará criar endereços IP padrão primeiro, em que o mesmo endereço IP não é garantido. Desassocie as VMs de IPs básicos e associe-as aos endereços IP padrão recém-criados. Em seguida, você poderá seguir as instruções para adicionar VMs ao pool de back-end de Standard Load Balancer. 

* **Criar novas VMs para adicionar aos pools de back-end do Load Balancer público padrão criado recentemente**.
    * Mais instruções sobre como criar uma VM e associá-las ao Standard Load Balancer podem ser encontradas [aqui](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).


## <a name="download-the-script"></a>Baixar o script

Baixe o script de migração do  [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0).
## <a name="use-the-script"></a>Usar o script

Há duas opções para você dependendo da configuração e das preferências do ambiente do PowerShell local:

* Se você não tiver os módulos AZ do Azure instalados ou não se lembrar de desinstalar os módulos AZ do Azure, a melhor opção é usar a `Install-Script` opção para executar o script.
* Se você precisar manter os módulos AZ do Azure, sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se você tem os módulos AZ do Azure instalados, execute `Get-InstalledModule -Name az` . Se você não vir nenhum módulo AZ instalado, poderá usar o `Install-Script` método.

### <a name="install-using-the-install-script-method"></a>Instalar usando o método de Install-Script

Para usar essa opção, você não deve ter os módulos AZ do Azure instalados no seu computador. Se eles estiverem instalados, o comando a seguir exibirá um erro. Você pode desinstalar os módulos AZ do Azure ou usar a outra opção para baixar o script manualmente e executá-lo.
  
Execute o script com o seguinte comando:

`Install-Script -Name AzurePublicLBUpgrade`

Esse comando também instala os módulos AZ necessários.  

### <a name="install-using-the-script-directly"></a>Instalar usando o script diretamente

Se você tiver alguns módulos AZ do Azure instalados e não puder desinstalá-los (ou não quiser desinstalá-los), poderá baixar manualmente o script usando a guia **download manual** no link de download de script. O script é baixado como um arquivo nupkg bruto. Para instalar o script desse arquivo nupkg, consulte [download de pacote manual](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Use `Connect-AzAccount` para se conectar ao Azure.

1. Use `Import-Module Az` para importar os módulos AZ.

1. Examine os parâmetros necessários:

   * **oldRgName: [String]: Required** – este é o grupo de recursos para o Load Balancer básico existente que você deseja atualizar. Para localizar esse valor de cadeia de caracteres, navegue até portal do Azure, selecione sua fonte de Load Balancer básica e clique na **visão geral** do balanceador de carga. O grupo de recursos está localizado nessa página.
   * **oldLBName: [String]: Required** – este é o nome do balanceador básico existente que você deseja atualizar. 
   * **newLBName: [String]: Required** – este é o nome do Standard Load Balancer a ser criado.
1. Execute o script usando os parâmetros apropriados. Pode levar de cinco a sete minutos para ser concluído.

    **Exemplo**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newLbName "LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Criar uma regra de saída para conexão de saída

Siga as [instruções](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) para criar uma regra de saída para que você possa
* Defina o NAT de saída do zero.
* Dimensione e ajuste o comportamento do NAT de saída existente.

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Há alguma limitação com o script Azure PowerShell para migrar a configuração de v1 para v2?

Sim. Consulte [Advertências/limitações](#caveatslimitations).

### <a name="how-long-does-the-upgrade-take"></a>Quanto tempo a atualização leva?

Geralmente leva cerca de 5-10 minutos para o script ser concluído e pode levar mais tempo dependendo da complexidade de sua configuração de Load Balancer. Portanto, mantenha o tempo de inatividade em mente e planeje o failover, se necessário.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>O script de Azure PowerShell também alterna o tráfego do meu Load Balancer básico para o Standard Load Balancer recém-criado?

Sim. O script Azure PowerShell não apenas atualiza o endereço IP público, copia a configuração de básico para Standard Load Balancer, mas também migra a VM para trás do Load Balancer público padrão recém-criado também. 

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o Standard Load Balancer](load-balancer-overview.md)
