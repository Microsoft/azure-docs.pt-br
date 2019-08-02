---
title: Migrar o Gateway de aplicativo do Azure e o Firewall do aplicativo de v1 para v2 Web
description: Este artigo mostra como migrar o Gateway de aplicativo do Azure e o Firewall do aplicativo Web de v1 para v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 0fd605d7d502970dccd37da1f3f70fdadb1094a1
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550443"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrar o Gateway de aplicativo do Azure e o Firewall do aplicativo de v1 para v2 Web

[O Gateway de aplicativo do Azure e o Firewall de aplicativo da Web (WAF) v2](application-gateway-autoscaling-zone-redundant.md) agora está disponível, oferecendo recursos adicionais, como redundância de zona de disponibilidade e de dimensionamento automático. No entanto, os gateways existentes v1 não são atualizados automaticamente para v2. Se você deseja migrar de v1 para v2, siga as etapas neste artigo.

Há duas fases em uma migração:

1. Migrar a configuração
2. Migrar o tráfego do cliente

Este artigo aborda a migração da configuração. Migração de tráfego do cliente varia dependendo do seu ambiente específico. No entanto, algumas recomendações gerais, de alto nível [são fornecidos](#migrate-client-traffic).

## <a name="migration-overview"></a>Visão geral da migração

Um script do PowerShell do Azure está disponível que faz o seguinte:

* Cria um novo gateway Standard_v2 ou WAF_v2 em uma sub-rede de rede virtual que você especificar.
* Perfeitamente copia a configuração associada ao v1 Standard ou WAF gateway para o gateway Standard_V2 ou WAF_V2 recém-criado.

### <a name="caveatslimitations"></a>Caveats\Limitations

* O novo gateway v2 tem novos endereços IP públicos e privados. Não é possível mover os endereços IP associados com o gateway v1 existentes diretamente para a v2. No entanto, você pode alocar um (não alocado) público ou privado endereço IP existente para o novo gateway v2.
* Você deve fornecer um espaço de endereço IP para outra sub-rede dentro de sua rede virtual em que seu gateway v1 está localizado. O script não é possível criar o gateway v2 em todas as sub-redes existentes que já tem um gateway de v1. No entanto, se a sub-rede existente já tiver um gateway v2, que ainda pode funcionar fornecido que há suficiente espaço de endereço IP.
* Para migrar uma configuração de SSL, você deve especificar todos os certificados SSL usados em seu gateway v1.
* Se você tiver o modo FIPS habilitado para seu gateway V1, não serão migrado para o novo gateway v2. O modo FIPS não tem suporte na versão 2.
* v2 não dá suporte a IPv6, portanto, o IPv6 habilitado v1 gateways não são migrados. Se você executar o script, ele pode não ser concluída.
* Se o gateway de v1 tem apenas um endereço IP privado, o script cria um endereço IP público e um endereço IP privado para o novo gateway v2. gateways v2 atualmente não tem suporte apenas a endereços IP privados.

## <a name="download-the-script"></a>Baixe o script

Baixe o script de migração a partir de [da Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Use o script

Há duas opções para você, dependendo de suas preferências e a configuração do ambiente do PowerShell local:

* Se você não tiver os módulos do Azure Az instalados ou não se importa de desinstalar os módulos do Azure Az, a melhor opção é usar o `Install-Script` opção para executar o script.
* Se você precisar manter os módulos do Azure Az, sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se você tiver os módulos do Azure Az instalados, execute `Get-InstalledModule -Name az`. Se você não vir qualquer instalado módulos de Az, então você pode usar o `Install-Script` método.

### <a name="install-using-the-install-script-method"></a>Instalar usando o método de Script de instalação

Para usar essa opção, você não deve ter os módulos do Azure Az instalados em seu computador. Se ele estiverem instalados, o comando a seguir exibe um erro. Você pode desinstalar os módulos do Azure Az ou usar outra opção para baixar o script manualmente e executá-lo.
  
Execute o script com o comando a seguir:

`Install-Script -Name AzureAppGWMigration`

Este comando também instala os módulos necessários do Az.  

### <a name="install-using-the-script-directly"></a>Instalar usando o script diretamente

Se você tiver alguns módulos do Azure Az instalados e não é possível desinstalá-los (ou não deseja desinstalá-los), você pode baixar manualmente o script usando o **Download Manual** guia no link de download do script. O script é baixado como um arquivo nupkg bruto. Para instalar o script desse arquivo nupkg, consulte [Download do pacote Manual](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Use `Connect-AzAccount` para se conectar ao Azure.

1. Use `Import-Module Az` para importar os módulos do Az.

1. Executar `Get-Help AzureAppGWMigration.ps1` para examinar os parâmetros necessários:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parâmetros do script:
   * **resourceId: [String]: Necessário** -esta é a ID de recurso do Azure para seu v1 padrão existentes ou gateway do WAF v1. Para encontrar esse valor de cadeia de caracteres, navegue até o portal do Azure, selecione seu gateway de aplicativo ou recurso de WAF e clique no **propriedades** link para o gateway. A ID de recurso está localizada nessa página.

     Você também pode executar os seguintes comandos do PowerShell do Azure para obter a ID de recurso:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]:  Necessário** -isso é o espaço de endereço IP que você alocou (ou deseja alocar) para uma nova sub-rede que contém seu novo gateway v2. Isso deve ser especificado na notação CIDR. Por exemplo:  10.0.0.0/24. Você não precisará criar essa sub-rede com antecedência. O script cria para você se ele não existir.
   * **appgwName: [String]: Opcional**. Isso é uma cadeia de caracteres que você especificar para usar como o nome do novo gateway Standard_v2 ou WAF_v2. Se esse parâmetro não for fornecido, o nome do seu gateway v1 existente será usado com o sufixo *_v2* acrescentado.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Opcional**.  Uma lista separada por vírgulas de objetos de PSApplicationGatewaySslCertificate que você cria para representar os certificados SSL em seu gateway v1 deve ser carregada para o novo gateway v2. Para cada um dos seus certificados SSL configurados para seu padrão v1 ou gateway do WAF v1, você pode criar um novo objeto PSApplicationGatewaySslCertificate por meio de `New-AzApplicationGatewaySslCertificate` os comandos mostrados aqui. É necessário o caminho do arquivo de certificado SSL e a senha.

       Esse parâmetro só é opcional se você não tiver os ouvintes HTTPS configurados para seu gateway v1 ou WAF. Se você tiver pelo menos uma configuração de Ouvinte HTTPS, você deve especificar esse parâmetro.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      Você pode passar `$mySslCert1, $mySslCert2` (separada por vírgulas) no exemplo anterior como valores para esse parâmetro no script.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Opcional**. Uma lista separada por vírgulas de objetos de PSApplicationGatewayTrustedRootCertificate que você cria para representar o [certificados de raiz confiável](ssl-overview.md) para autenticação de suas instâncias de back-end do seu gateway v2.  

      Para criar uma lista de objetos PSApplicationGatewayTrustedRootCertificate, consulte [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: Opcional**. Um específico endereço IP privado que você deseja associar ao seu novo gateway de v2.  Isso deve ser da mesma rede virtual que você alocar para o novo gateway v2. Se isso não for especificado, o script aloca um endereço IP privado para o gateway de v2.
    * **publicIpResourceId: [String]: Opcional**. O resourceId de um recurso de endereço (SKU standard) do IP público em sua assinatura que você deseja alocar para o novo gateway v2. Se isso não for especificado, o script aloca um novo IP público no mesmo grupo de recursos. O nome é o nome do gateway v2 com *- IP* acrescentado.
   * **validateMigration: [switch]: Opcional**. Use esse parâmetro se desejar que o script para fazer algumas configurações básicas validações de comparação após a criação do gateway v2 e a cópia de configuração. Por padrão, nenhuma validação é feita.
   * **enableAutoScale: [switch]: Opcional**. Use esse parâmetro se desejar que o script para habilitar o dimensionamento automático no novo gateway v2 após sua criação. Por padrão, o dimensionamento automático está desabilitado. Você pode sempre habilitá-lo manualmente mais tarde o gateway v2 recém-criado.

1. Execute o script usando os parâmetros apropriados. Pode levar cinco a sete minutos para ser concluído.

    **Exemplo**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrar o tráfego do cliente

Primeiro, verifique uma segunda vez que o script criado com êxito um novo gateway v2 com a configuração exata migrados ao longo do seu gateway v1. Você pode verificar isso no portal do Azure.

Além disso, envie uma pequena quantidade de tráfego por meio do gateway v2 como um teste manual.
  
Aqui estão alguns cenários em que o gateway de aplicativo atual (padrão) pode receber tráfego de cliente e nossas recomendações para cada um deles:

* **Uma zona DNS personalizada (por exemplo, contoso.com) que aponta para o endereço IP de front-end (usando um registro) associado com seu v1 padrão ou o gateway do WAF v1**.

    Você pode atualizar o registro DNS para apontar para o rótulo IP ou DNS de front-end associado com o gateway de aplicativo Standard_v2. Dependendo da TTL configurado em seu registro de DNS, pode levar algum tempo para todo o tráfego de cliente migrar para o novo gateway v2.
* **Uma zona DNS personalizada (por exemplo, contoso.com) que aponta para o rótulo DNS (por exemplo: *myappgw.eastus.cloudapp.azure.com* usando um registro CNAME) associada ao seu gateway v1**.

   Você tem duas opções:

  * Se você usar endereços IP públicos em seu gateway de aplicativo, você pode fazer um controlado granular migração usando um perfil do Gerenciador de tráfego para rotear o tráfego (método de roteamento de tráfego ponderado) de forma incremental para o novo gateway v2.

    Você pode fazer isso adicionando os rótulos DNS dos gateways de aplicativo o v1 e v2 para a [perfil do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)e vou criar o registro DNS personalizado (por exemplo, www.contoso.com) para o domínio do Traffic Manager (por exemplo, Contoso.trafficmanager.NET).
  * Ou, você pode atualizar o registro DNS de domínio personalizado para apontar para o rótulo DNS do gateway de aplicativo v2 novo. Dependendo da TTL configurado em seu registro de DNS, pode levar algum tempo para todo o tráfego de cliente migrar para o novo gateway v2.
* **Seus clientes se conectarem ao front-end de endereço IP do seu gateway de aplicativo**.

   Atualize seus clientes para usar os endereços IP associados com o gateway de aplicativo v2 recém-criado. É recomendável que você não use endereços IP diretamente. Considere usar o rótulo de nome DNS (por exemplo, yourgateway.eastus.cloudapp.azure.com) associado com o gateway de aplicativo que você pode CNAME para sua própria zona DNS personalizada (por exemplo, contoso.com).

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Há alguma limitação com o script do PowerShell do Azure para migrar a configuração de v1 para v2?

Sim. Ver [advertências/limitações](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>É aplicável para o produto do WAF do Gateway de aplicativo também neste artigo e o script do PowerShell do Azure? 

Sim.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>O script do PowerShell do Azure alternar também sobre o tráfego do meu gateway de v1 para o gateway recém-criado v2?

Não. O script do PowerShell do Azure migra apenas a configuração. Migração de tráfego real é sua responsabilidade e em seu controle.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>O novo gateway v2 criado pelo script do PowerShell do Azure é dimensionado apropriadamente para lidar com todo o tráfego que atualmente é atendido por meu gateway v1?

O script do PowerShell do Azure cria um novo gateway v2 com um tamanho apropriado para tratar do tráfego no gateway v1 existente. Dimensionamento automático é desabilitado por padrão, mas você pode habilitar o dimensionamento automático quando você executa o script.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Configurei meu gateway de v1 para enviar logs para o armazenamento do Azure. O script replicar essa configuração para a v2 também?

Não. O script não replica essa configuração para a v2. Você deve adicionar a configuração de log separadamente para o gateway v2 migrado.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Eu enfrentei alguns problemas com o uso desse script. Como obter ajuda?
  
Você pode enviar um email para appgwmigrationsup@microsoft.com, abra um caso de suporte com o suporte do Azure, ou ambos.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o aplicativo v2 de Gateway](application-gateway-autoscaling-zone-redundant.md)
