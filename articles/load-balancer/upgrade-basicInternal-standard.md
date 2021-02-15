---
title: Atualizar do básico interno para o Azure Load Balancer interno Standard
description: Este artigo mostra como atualizar o Load Balancer interno do Azure de SKU básico para SKU Standard
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: 1b7bdbdb9e1d642f2ef4a715d4993e4f449ccd0a
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050690"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Atualizar o Load Balancer interno do Azure-nenhuma conexão de saída é necessária
O [Azure Standard Load Balancer](load-balancer-overview.md) oferece um conjunto avançado de funcionalidades e alta disponibilidade por meio de redundância de zona. Para saber mais sobre Load Balancer SKU, confira [tabela de comparação](./skus.md#skus).

Este artigo apresenta um script do PowerShell que cria um Standard Load Balancer com a mesma configuração que o Load Balancer básico, juntamente com a migração de tráfego do Load Balancer básico para o Standard Load Balancer.

## <a name="upgrade-overview"></a>Visão geral da atualização

Há um script de Azure PowerShell disponível que faz o seguinte:

* Cria um Load Balancer SKU interno padrão no local que você especificar. Observe que nenhuma [conexão de saída](./load-balancer-outbound-connections.md) não será fornecida pelo Load balancer interno padrão.
* Copia diretamente as configurações do Load Balancer de SKU básico para o Standard Load Balancer recém-criado.
* Mova diretamente os IPs privados do Load Balancer básico para o Standard Load Balancer recém-criado.
* Mova diretamente as VMs do pool de back-end do Load Balancer básico para o pool de back-end do Standard Load Balancer

### <a name="caveatslimitations"></a>Caveats\Limitations

* O script só dá suporte à atualização de Load Balancer interno, em que nenhuma conexão de saída é necessária. Se você precisar de [conexão de saída](./load-balancer-outbound-connections.md) para algumas de suas VMs, consulte esta [página](upgrade-InternalBasic-To-PublicStandard.md) para obter instruções. 
* O Load Balancer básico precisa estar no mesmo grupo de recursos que as VMs de back-end e NICs.
* Se o balanceador de carga padrão for criado em uma região diferente, você não poderá associar as VMs existentes na região antiga ao Standard Load Balancer recém-criado. Para contornar essa limitação, certifique-se de criar uma nova VM na nova região.
* Se seu Load Balancer não tiver nenhuma configuração de IP de front-end ou pool de back-ends, provavelmente você encontrará um erro ao executar o script. Verifique se eles não estão vazios.

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>Altere o método de alocação de IP para estático para configuração de IP de front-end (ignore esta etapa se ela já estiver estática)

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos** e, em seguida, selecione o Load Balancer básico na lista de recursos.

2. Em **configurações**, selecione **configuração de IP de front-end** e selecione a primeira configuração de IP de front-end. 

3. Para **atribuição**, selecione **estático**

4. Repita a etapa 3 para todas as configurações de IP de front-end da Load Balancer básica.


## <a name="download-the-script"></a>Baixar o script

Baixe o script de migração do  [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0).
## <a name="use-the-script"></a>Usar o script

Há duas opções para você dependendo da configuração e das preferências do ambiente do PowerShell local:

* Se você não tiver os módulos AZ do Azure instalados ou não se lembrar de desinstalar os módulos AZ do Azure, a melhor opção é usar a `Install-Script` opção para executar o script.
* Se você precisar manter os módulos AZ do Azure, sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se você tem os módulos AZ do Azure instalados, execute `Get-InstalledModule -Name az` . Se você não vir nenhum módulo AZ instalado, poderá usar o `Install-Script` método.

### <a name="install-using-the-install-script-method"></a>Instalar usando o método de Install-Script

Para usar essa opção, você não deve ter os módulos AZ do Azure instalados no seu computador. Se eles estiverem instalados, o comando a seguir exibirá um erro. Você pode desinstalar os módulos AZ do Azure ou usar a outra opção para baixar o script manualmente e executá-lo.
  
Execute o script com o seguinte comando:

`Install-Script -Name AzureILBUpgrade`

Esse comando também instala os módulos AZ necessários.  

### <a name="install-using-the-script-directly"></a>Instalar usando o script diretamente

Se você tiver alguns módulos AZ do Azure instalados e não puder desinstalá-los (ou não quiser desinstalá-los), poderá baixar manualmente o script usando a guia **download manual** no link de download de script. O script é baixado como um arquivo nupkg bruto. Para instalar o script desse arquivo nupkg, consulte [download de pacote manual](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Use `Connect-AzAccount` para se conectar ao Azure.

1. Use `Import-Module Az` para importar os módulos AZ.

1. Examine os parâmetros necessários:

   * **rgName: [String]: Required** – este é o grupo de recursos para o Load Balancer básico existente e o novo Standard Load Balancer. Para localizar esse valor de cadeia de caracteres, navegue até portal do Azure, selecione sua fonte de Load Balancer básica e clique na **visão geral** do balanceador de carga. O grupo de recursos está localizado nessa página.
   * **oldLBName: [String]: Required** – este é o nome do balanceador básico existente que você deseja atualizar. 
   * **NewLocation: [String]: Required** – este é o local no qual o Standard Load Balancer será criado. É recomendável herdar o mesmo local do Load Balancer básico escolhido para o Standard Load Balancer para uma melhor associação com outros recursos existentes.
   * **newLBName: [String]: Required** – este é o nome do Standard Load Balancer a ser criado.
1. Execute o script usando os parâmetros apropriados. Pode levar de cinco a sete minutos para ser concluído.

    **Exemplo**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Há alguma limitação com o script Azure PowerShell para migrar a configuração de v1 para v2?

Sim. Consulte [Advertências/limitações](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>O script de Azure PowerShell também alterna o tráfego do meu Load Balancer básico para o Standard Load Balancer recém-criado?

Sim, ele migra o tráfego. Se você quiser migrar o tráfego pessoalmente, use [esse script](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) que não move as VMs para você.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o Standard Load Balancer](load-balancer-overview.md)
