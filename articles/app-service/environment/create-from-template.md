---
title: Criar um ASE com ARM
description: Saiba como criar um ambiente de serviço de aplicativo ILB ou externo usando um modelo de Azure Resource Manager.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 15cd0979fdc2468ab50451042cd99a8442470139
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148178"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Criar um ASE usando um modelo do Azure Resource Manager

## <a name="overview"></a>Visão geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ambientes do Serviço de Aplicativo do Azure (ASEs) podem ser criados com um ponto de extremidade acessível pela Internet ou um ponto de extremidade em um endereço interno na rede virtual (VNet) do Azure. Quando criado com um ponto de extremidade interno, esse ponto de extremidade é fornecido por um componente do Azure chamado ILB (balanceador de carga interno). O ASE em um endereço IP interno é chamado de um ASE ILB. O ASE com um ponto de extremidade público é chamado de um ASE externo. 

Um ASE pode ser criado usando o portal do Azure ou um modelo do Azure Resource Manager. Este artigo explica as etapas e a sintaxe que você precisa para criar um ASE Externo ou um ASE ILB com modelos do Resource Manager. Para saber como criar uma ASE no portal do Azure, consulte [Fazer um ASE externo][MakeExternalASE] ou [Fazer um ASE ILB][MakeILBASE].

Ao criar um ASE no portal do Azure, você pode criar sua VNet ao mesmo tempo ou escolher uma VNet pré-existente na qual implantar. Ao criar um ASE com base em um modelo, você deve começar com: 

* Uma VNet do Resource Manager.
* Uma sub-rede nessa VNet. Recomendamos um tamanho de sub-rede ASE de `/24` com 256 endereços para acomodar necessidades futuras de crescimento e dimensionamento. Depois que o ASE é criado, você não pode alterar o tamanho.
* A ID do recurso da sua VNet. Você pode obter mais informações no portal do Azure nas propriedades da rede virtual.
* A assinatura na qual você deseja implantar.
* O local no qual você deseja implantar.

Para automatizar a criação do ASE:

1. Crie o ASE com base em um modelo. Se você criar um ASE externo, terá concluído após essa etapa. Se estiver criando um ASE ILB, haverá algumas coisas extras a serem feitas.

2. Depois que o ASE ILB for criado, um certificado TLS/SSL que corresponda ao domínio do ASE ILB será carregado.

3. O certificado TLS/SSL carregado é atribuído ao ASE ILB como seu certificado TLS/SSL "padrão".  Esse certificado é usado para tráfego TLS/SSL para aplicativos no ASE ILB quando eles usam o domínio raiz comum que é atribuído ao ASE (por exemplo, `https://someapp.mycustomrootdomain.com` ).


## <a name="create-the-ase"></a>Criar o ASE
Um modelo do Resource Manager que cria um ASE e seu arquivo de parâmetros associado está disponível [no exemplo][quickstartasev2create] no GitHub.

Se você quiser fazer uma ASE ILB, use esses [exemplos][quickstartilbasecreate] do modelo do Resource Manager. Eles atendem a esse caso de uso. A maioria dos parâmetros no arquivo *azuredeploy.parameters.json* é comum à criação de ASEs ILB e ASEs Externos. A lista a seguir chama parâmetros de anotação especial ou que são exclusivos, quando você cria um ASE ILB:

* *internalLoadBalancingMode*: na maioria dos casos, define como 3, o que significa que o tráfego HTTP/HTTPS nas portas 80/443 e as portas do canal de dados/controle atendidas pelo serviço FTP no ASE serão associados a um endereço interno da rede virtual alocada do ILB. Se essa propriedade é definida como 2, somente as portas relacionadas de serviço FTP (canais de controle e de dados) estão associadas a um endereço ILB. O tráfego HTTP/HTTPS permanece no VIP público.
* *dnsSuffix*: Esse parâmetro define o domínio-raiz padrão que é atribuído ao ASE. A variação pública do Serviço de Aplicativo do Azure, o domínio de raiz padrão para todos os aplicativos Web, é *azurewebsites.net*. Como um ASE ILB é interno na rede virtual do cliente, não faz sentido usar o domínio-raiz padrão do serviço público. Em vez disso, um ASE ILB deve ter um domínio de raiz padrão que faça sentido para uso dentro da rede virtual interna da empresa. Por exemplo, a Contoso Corporation pode usar um domínio raiz padrão *internal-contoso.com* para aplicativos que se destinam a serem resolvidos e acessados apenas na rede virtual da Contoso. 
* *ipSslAddressCount*: Esse parâmetro padroniza automaticamente para um valor 0 no arquivo *azuredeploy.json* porque os ASEs ILB têm apenas um único endereço ILB. Não há nenhum endereço IP SSL explícito para um ASE ILB. Portanto, o pool de endereços IP SSL para um ASE ILB deve ser definido como zero. Caso contrário, ocorrerá um erro de provisionamento. 

Após o arquivo *azuredeploy.parameters.json* ser preenchido, crie o ASE usando o snippet de código do PowerShell. Altere os caminhos do arquivo para corresponder aos locais do modelo Resource Manager no seu computador. Lembre-se de fornecer seus próprios valores para o nome de implantação do Resource Manager e para o nome do grupo de recursos:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Leva aproximadamente uma hora para o ASE ser criado. Em seguida, o ASE aparece no portal na lista de ASEs para a assinatura que disparou a implantação.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Carregar e configurar o certificado TLS/SSL "padrão"
Um certificado TLS/SSL deve ser associado ao ASE como o certificado TLS/SSL "padrão" que é usado para estabelecer conexões TLS com aplicativos. Se o sufixo DNS padrão do ASE for *Internal-contoso.com*, uma conexão com o `https://some-random-app.internal-contoso.com` exigirá um certificado TLS/SSL válido para **. Internal-contoso.com*. 

Obtenha um certificado TLS/SSL válido usando autoridades de certificação internas, comprando um certificado de um emissor externo ou usando um certificado autoassinado. Independentemente da origem do certificado TLS/SSL, os seguintes atributos de certificado devem ser configurados corretamente:

* **Assunto**: esse atributo deve ser definido como **. your-root-Domain-here.com*.
* **Nome alternativo da entidade**: esse atributo deve incluir **. your-root-Domain-here.com* e **. SCM.your-root-Domain-here.com*. As conexões TLS com o site SCM/kudu associado a cada aplicativo usam um endereço no formato *Your-app-Name.SCM.your-root-Domain-here.com*.

Com um certificado TLS/SSL válido em mãos, são necessárias duas etapas preparatória adicionais. Converta/salve o certificado TLS/SSL como um arquivo. pfx. Lembre-se que o arquivo .pfx deve incluir todos os certificados intermediários e raiz. Proteja-o com uma senha.

O arquivo. pfx precisa ser convertido em uma cadeia de caracteres Base64 porque o certificado TLS/SSL é carregado usando um modelo do Resource Manager. Como os modelos do Resource Manager são arquivos de texto, o arquivo. pfx deve ser convertido em uma cadeia de caracteres base64. Dessa forma pode ser incluído como um parâmetro do modelo.

Use o seguinte snippet de código do PowerShell para:

* Criar um certificado autoassinado.
* Exportar o certificado como um arquivo. pfx.
* Converter o arquivo. pfx em uma cadeia de caracteres codificada em base64.
* Salvar a cadeia de caracteres codificada em base64 em um arquivo separado. 

Este código do PowerShell para a codificação de base64 foi adaptado do [Blog de Scripts do PowerShell][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Depois que o certificado TLS/SSL for gerado e convertido com êxito em uma cadeia de caracteres codificada em base64, use o modelo do Resource Manager de exemplo [Configurar o certificado SSL padrão][quickstartconfiguressl] no github. 

Os parâmetros no arquivo *azuredeploy.parameters.json* estão listados abaixo:

* *appServiceEnvironmentName*: o nome do ASE ILB que está sendo configurado.
* *existingAseLocation*: cadeia de caracteres de texto que contém a região do Azure onde o ASE ILB foi implantado.  Por exemplo, "Centro-Sul dos EUA".
* *pfxBlobString*: A representação de cadeia de caracteres codificada em base 64 do arquivo .pfx. Use o snippet de código mostrado anteriormente e copie a cadeia de caracteres contida em "exportedcert.pfx.b64". Cole-o como o valor de atributo *pfxBlobString*.
* *senha*: a senha usada para proteger o arquivo. pfx.
* *certificateThumbprint*: impressão digital do certificado. Se você recuperar esse valor do PowerShell (por exemplo, *$certificate.Thumbprint* do snippet de código anterior), poderá usar o valor como estiver. Se você copiar o valor da caixa de diálogo Certificado Windows, lembre-se de retirar os espaços estranhos. A *certificateThumbprint* deve ser semelhante a AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: um identificador de cadeia de caracteres amigável de sua escolha usado para identificar o certificado. O nome é usado como parte do identificador exclusivo do Gerenciador de recursos para a entidade *Microsoft. Web/Certificates* que representa o certificado TLS/SSL. O nome *deve* terminar com o seguinte sufixo: \_seuNomeASEAqui_InternalLoadBalancingASE. O portal do Azure usa esse sufixo como um indicador de que o certificado é usado para proteger um ASE habilitado por ILB.

Um exemplo abreviado de *azuredeploy.parameters.json* é mostrado aqui:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

Depois que o *azuredeploy.parameters.jsno* arquivo for preenchido, configure o certificado TLS/SSL padrão usando o trecho de código do PowerShell. Altere os caminhos do arquivo para corresponder ao local em que os arquivos do modelo do Resource Manager estão localizados no seu computador. Lembre-se de fornecer seus próprios valores para o nome de implantação do Resource Manager e para o nome do grupo de recursos:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Leva cerca de 40 minutos por front-end ASE para aplicar a alteração. Por exemplo, para um ASE padrão dimensionado que usa dois front-ends, o modelo leva aproximadamente uma hora e 20 minutos para concluir. Enquanto o modelo estiver em execução, o ASE não pode ser dimensionado.  

Depois que o modelo for concluído, aplicativos em ASE ILB podem ser acessados via HTTPS. As conexões são protegidas usando o certificado TLS/SSL padrão. O certificado TLS/SSL padrão é usado quando os aplicativos no ASE ILB são resolvidos usando uma combinação do nome do aplicativo mais o nome de host padrão. Por exemplo, `https://mycustomapp.internal-contoso.com` usa o certificado TLS/SSL padrão para **. Internal-contoso.com*.

No entanto, assim como os aplicativos que são executados no serviço público multilocatário, os desenvolvedores podem configurar nomes de host personalizados para aplicativos individuais. Eles também podem configurar associações de certificado TLS/SSL SNI exclusivas para aplicativos individuais.

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicativo v1 ##
O Ambiente do Serviço de Aplicativo tem duas versões: ASEv1 e ASEv2. As informações anteriores foram baseadas no ASEv2. Esta seção mostra as diferenças entre o ASEv1 e o ASEv2.

No ASEv1, você gerencia todos os recursos manualmente. Isso inclui os front-ends, as funções de trabalho e os endereços IP usados para o SSL baseado em IP. Antes de poder escalar horizontalmente o plano do Serviço de Aplicativo, primeiro você deve escalar horizontalmente o pool de trabalho que você deseja hospedar.

O ASEv1 usa um modelo de preço diferente do ASEv2. No ASEv1, você paga por cada vCPU alocado. Isso inclui os vCPUs que são usados para front-ends ou funções de trabalho que não hospedam nenhuma carga de trabalho. No ASEv1, o tamanho máximo de escala padrão de um ASE é de 55 hosts no total. Isso inclui funções de trabalho e front-ends. Uma vantagem do ASEv1 é que ele pode ser implantado em uma rede virtual clássica, bem como em uma rede virtual do Resource Manager. Para saber mais sobre o ASEv1, confira [Introdução ao Ambiente do Serviço de Aplicativo v1][ASEv1Intro].

Para criar um ASEv1 usando um modelo do Resource Manager, consulte [Criar um ASE v1 ILB com um modelo do Resource Manager][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md