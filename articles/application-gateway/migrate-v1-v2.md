---
title: Migrar do gateway v1 para v2-Aplicativo Azure
description: Este artigo mostra como migrar Aplicativo Azure gateway e o Firewall do aplicativo Web da v1 para a v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 4757a8237aa6226b78e7c1e79ba50710e31d28e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594258"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrar Aplicativo Azure gateway e firewall do aplicativo Web da v1 para a v2

[Aplicativo Azure gateway e o WAF (firewall do aplicativo Web) V2](application-gateway-autoscaling-zone-redundant.md) já estão disponíveis, oferecendo recursos adicionais como dimensionamento automático e redundância de zona de disponibilidade. No entanto, os gateways existentes v1 não são atualizados automaticamente para v2. Se você quiser migrar do v1 para o v2, siga as etapas neste artigo.

Há dois estágios em uma migração:

1. Migrar a configuração
2. Migrar o tráfego do cliente

Este artigo aborda a migração de configuração. A migração de tráfego do cliente varia dependendo do seu ambiente específico. No entanto, algumas recomendações gerais de alto nível [são fornecidas](#migrate-client-traffic).

## <a name="migration-overview"></a>Visão geral da migração

Há um script de Azure PowerShell disponível que faz o seguinte:

* Cria um novo Standard_v2 ou WAF_v2 gateway em uma sub-rede de rede virtual que você especificar.
* Copia diretamente a configuração associada ao gateway v1 Standard ou WAF para o gateway Standard_V2 ou WAF_V2 recém-criado.

### <a name="caveatslimitations"></a>Caveats\Limitations

* O novo gateway V2 tem novos endereços IP públicos e privados. Não é possível mover os endereços IP associados ao gateway v1 existente diretamente para a v2. No entanto, você pode alocar um endereço IP público ou privado existente (não alocado) para o novo gateway v2.
* Você deve fornecer um espaço de endereço IP para outra sub-rede em sua rede virtual onde o gateway v1 está localizado. O script não pode criar o gateway V2 em nenhuma sub-rede existente que já tenha um gateway v1. No entanto, se a sub-rede existente já tiver um gateway v2, isso ainda poderá funcionar, desde que haja espaço de endereço IP suficiente.
* Se você tiver um grupo de segurança de rede ou rotas definidas pelo usuário associadas à sub-rede de gateway v2, verifique se eles aderem aos requisitos de [NSG](../application-gateway/configuration-infrastructure.md#network-security-groups) e [requisitos de UDR](../application-gateway/configuration-infrastructure.md#supported-user-defined-routes) para uma migração bem-sucedida
* [As políticas de ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoint-policies-overview.md) atualmente não têm suporte em uma sub-rede do Gateway de Aplicativo.
* Para migrar uma configuração de TLS/SSL, você deve especificar todos os certificados TLS/SSL usados no seu gateway v1.
* Se você tiver o modo FIPS habilitado para o seu gateway v1, ele não será migrado para seu novo gateway v2. O modo FIPS não tem suporte na v2.
* v2 não dá suporte a IPv6, portanto, os gateways v1 habilitados para IPv6 não são migrados. Se você executar o script, ele poderá não ser concluído.
* Se o gateway v1 tiver apenas um endereço IP privado, o script criará um endereço IP público e um endereço IP privado para o novo gateway v2. os gateways v2 atualmente não dão suporte apenas a endereços IP privados.
* Os cabeçalhos com nomes que contenham algo diferente de letras, dígitos, hifens e sublinhados não são passados para seu aplicativo. Isso se aplica somente aos nomes de cabeçalho, não aos valores de cabeçalho. Essa é uma alteração significativa do v1.

## <a name="download-the-script"></a>Baixar o script

Baixe o script de migração do  [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Usar o script

Há duas opções para você dependendo da configuração e das preferências do ambiente do PowerShell local:

* Se você não tiver os módulos AZ do Azure instalados ou não se lembrar de desinstalar os módulos AZ do Azure, a melhor opção é usar a `Install-Script` opção para executar o script.
* Se você precisar manter os módulos AZ do Azure, sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se você tem os módulos AZ do Azure instalados, execute `Get-InstalledModule -Name az` . Se você não vir nenhum módulo AZ instalado, poderá usar o `Install-Script` método.

### <a name="install-using-the-install-script-method"></a>Instalar usando o método de Install-Script

Para usar essa opção, você não deve ter os módulos AZ do Azure instalados no seu computador. Se eles estiverem instalados, o comando a seguir exibirá um erro. Você pode desinstalar os módulos AZ do Azure ou usar a outra opção para baixar o script manualmente e executá-lo.
  
Execute o script com o seguinte comando para obter a versão mais recente:

`Install-Script -Name AzureAppGWMigration -Force`

Esse comando também instala os módulos AZ necessários.  

### <a name="install-using-the-script-directly"></a>Instalar usando o script diretamente

Se você tiver alguns módulos AZ do Azure instalados e não puder desinstalá-los (ou não quiser desinstalá-los), poderá baixar manualmente o script usando a guia **download manual** no link de download de script. O script é baixado como um arquivo nupkg bruto. Para instalar o script desse arquivo nupkg, consulte [download de pacote manual](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Use `Connect-AzAccount` para se conectar ao Azure.

1. Use `Import-Module Az` para importar os módulos AZ.

1. Execute `Get-Help AzureAppGWMigration.ps1` para examinar os parâmetros necessários:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parâmetros para o script:
   * **ResourceId: [String]: Required** -esta é a ID de recurso do Azure para seu gateway Standard v1 ou WAF v1 existente. Para localizar esse valor de cadeia de caracteres, navegue até o portal do Azure, selecione o recurso de gateway de aplicativo ou WAF e clique no link **Propriedades** para o gateway. A ID do recurso está localizada nessa página.

     Você também pode executar os seguintes comandos Azure PowerShell para obter a ID do recurso:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]: Required** -este é o espaço de endereço IP que você alocou (ou deseja alocar) para uma nova sub-rede que contém o novo gateway v2. Isso deve ser especificado na notação CIDR. Por exemplo: 10.0.0.0/24. Você não precisa criar essa sub-rede com antecedência. O script o criará para você se ele não existir.
   * **appgwName: [String]: opcional**. Essa é uma cadeia de caracteres que você especifica para usar como o nome para o novo Standard_v2 ou WAF_v2 gateway. Se esse parâmetro não for fornecido, o nome do gateway v1 existente será usado com o sufixo *_v2* acrescentado.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: opcional**.  Uma lista separada por vírgulas de objetos PSApplicationGatewaySslCertificate que você cria para representar os certificados TLS/SSL do seu gateway v1 deve ser carregada para o novo gateway v2. Para cada um dos certificados TLS/SSL configurados para seu gateway Standard v1 ou WAF v1, você pode criar um novo objeto PSApplicationGatewaySslCertificate por meio do `New-AzApplicationGatewaySslCertificate` comando mostrado aqui. Você precisa do caminho para seu arquivo de certificado TLS/SSL e a senha.

     Esse parâmetro só será opcional se você não tiver ouvintes HTTPS configurados para seu gateway v1 ou WAF. Se você tiver pelo menos uma instalação de ouvinte HTTPS, deverá especificar esse parâmetro.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Você pode passar `$mySslCert1, $mySslCert2` (separados por vírgula) no exemplo anterior como valores para esse parâmetro no script.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: opcional**. Uma lista separada por vírgulas de objetos PSApplicationGatewayTrustedRootCertificate que você cria para representar os [certificados raiz confiáveis](ssl-overview.md) para autenticação de suas instâncias de back-end do seu gateway v2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Para criar uma lista de objetos PSApplicationGatewayTrustedRootCertificate, consulte [New-AzApplicationGatewayTrustedRootCertificate](/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate).
   * **privateIpAddress: [String]: opcional**. Um endereço IP privado específico que você deseja associar ao seu novo gateway v2.  Isso deve ser da mesma VNet que você aloca para o novo gateway v2. Se isso não for especificado, o script alocará um endereço IP privado para o seu gateway v2.
   * **publicIpResourceId: [String]: opcional**. O ResourceId do recurso de endereço IP público (SKU padrão) existente na sua assinatura que você deseja alocar para o novo gateway v2. Se isso não for especificado, o script alocará um novo IP público no mesmo grupo de recursos. O nome é o nome do gateway V2 com *-IP* acrescentado.
   * **validateMigration: [opção]: opcional**. Use esse parâmetro se desejar que o script faça algumas validações de comparação de configuração básica após a criação do gateway V2 e a cópia de configuração. Por padrão, nenhuma validação é feita.
   * **enableAutoScale: [opção]: opcional**. Use esse parâmetro se desejar que o script habilite o dimensionamento automático no novo gateway v2 depois que ele for criado. Por padrão, o dimensionamento automático está desabilitado. Você sempre pode habilitá-lo manualmente mais tarde no gateway v2 recém-criado.

1. Execute o script usando os parâmetros apropriados. Pode levar de cinco a sete minutos para ser concluído.

    **Exemplo**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrar o tráfego do cliente

Primeiro, verifique se o script criou com êxito um novo gateway V2 com a configuração exata migrada do seu gateway v1. Você pode verificar isso no portal do Azure.

Além disso, envie uma pequena quantidade de tráfego por meio do gateway v2 como um teste manual.
  
Aqui estão alguns cenários em que o gateway de aplicativo atual (Standard) pode receber o tráfego do cliente e nossas recomendações para cada um deles:

* **Uma zona DNS personalizada (por exemplo, contoso.com) que aponta para o endereço IP de front-end (usando um registro A) associado ao seu gateway Standard v1 ou WAF v1**.

    Você pode atualizar o registro DNS para apontar para o IP de front-end ou o rótulo DNS associado ao gateway de aplicativo Standard_v2. Dependendo do TTL configurado em seu registro DNS, pode levar algum tempo para que todo o tráfego do cliente migre para o novo gateway v2.
* **Uma zona DNS personalizada (por exemplo, contoso.com) que aponta para o rótulo DNS (por exemplo: *myappgw.eastus.cloudapp.Azure.com* usando um registro CNAME) associado ao seu gateway v1**.

   Você tem duas opções:

  * Se você usar endereços IP públicos no seu gateway de aplicativo, poderá fazer uma migração detalhada e granular usando um perfil do Gerenciador de tráfego para rotear incrementalmente o tráfego (método de roteamento de tráfego ponderado) para o novo gateway v2.

    Você pode fazer isso adicionando os rótulos de DNS dos gateways de aplicativo v1 e v2 ao perfil do [Gerenciador de tráfego](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)e CNAME do registro DNS personalizado (por exemplo, `www.contoso.com` ) para o domínio do Traffic Manager (por exemplo, contoso.trafficmanager.net).
  * Ou então, você pode atualizar seu registro DNS de domínio personalizado para apontar para o rótulo DNS do novo gateway de aplicativo v2. Dependendo do TTL configurado em seu registro DNS, pode levar algum tempo para que todo o tráfego do cliente migre para o novo gateway v2.
* **Seus clientes se conectam ao endereço IP de front-end do seu gateway de aplicativo**.

   Atualize seus clientes para usar os endereços IP associados ao gateway de aplicativo v2 criado recentemente. Recomendamos que você não use endereços IP diretamente. Considere o uso do rótulo de nome DNS (por exemplo, yourgateway.eastus.cloudapp.azure.com) associado ao seu gateway de aplicativo que você pode CNAME para sua própria zona DNS personalizada (por exemplo, contoso.com).

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Há alguma limitação com o script Azure PowerShell para migrar a configuração de v1 para v2?

Sim. Consulte [Advertências/limitações](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Este artigo e o script Azure PowerShell aplicáveis para o produto WAF do gateway de aplicativo também? 

Sim.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>O script de Azure PowerShell também alterna o tráfego do meu gateway v1 para o gateway v2 recém-criado?

Não. O script de Azure PowerShell migra apenas a configuração. A migração de tráfego real é sua responsabilidade e no seu controle.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>O novo gateway v2 criado pelo Azure PowerShell script é dimensionado adequadamente para lidar com todo o tráfego servido atualmente pelo meu gateway v1?

O script de Azure PowerShell cria um novo gateway V2 com um tamanho apropriado para lidar com o tráfego em seu gateway v1 existente. O dimensionamento automático é desabilitado por padrão, mas você pode habilitar o dimensionamento automático ao executar o script.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Configurei meu gateway v1 para enviar logs para o armazenamento do Azure. O script replica essa configuração para v2 também?

Não. O script não replica essa configuração para v2. Você deve adicionar a configuração de log separadamente para o gateway v2 migrado.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Este script dá suporte a certificados carregados no Azure keyvault?

Não. Atualmente, o script não oferece suporte a certificados no keyvault. No entanto, isso está sendo considerado para uma versão futura.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Ocorreu alguns problemas com o uso desse script. Como posso obter ajuda?
  
Você pode entrar em contato com o suporte do Azure no tópico "configuração e configuração/migração para o SKU v2". Saiba mais sobre o [suporte do Azure aqui](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o gateway de aplicativo v2](application-gateway-autoscaling-zone-redundant.md)
