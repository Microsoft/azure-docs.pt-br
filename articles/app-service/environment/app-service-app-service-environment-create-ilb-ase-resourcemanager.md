---
title: Como Criar um ASE ILB Usando Modelos do Azure Resource Manager | Microsoft Docs
description: Saiba como criar um ASE de balanceador de carga interno usando modelos do Azure Resource Manager.
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.openlocfilehash: ea9407208f1bf555cf1a6d166825896dec89ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Como criar um ASE ILB usando modelos do Azure Resource Manager

> [!NOTE] 
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1. Há uma versão mais recente do ambiente de serviço de aplicativo que é mais fácil de usar e é executado na infraestrutura mais avançada. Para saber mais sobre o novo início de versão com o [Introdução ao Ambiente do Serviço de Aplicativo](intro.md).
>

## <a name="overview"></a>Visão geral
Ambientes de Serviço de Aplicativo podem ser criados com um endereço interno de rede virtual em vez de um VIP público.  Esse endereço interno é fornecido por um componente do Azure chamado ILB (balanceador de carga interno).  Um ASE ILB pode ser criado usando o portal do Azure.  Ele também pode ser criado usando a automação por meio de modelos do Azure Resource Manager.  Este artigo explica as etapas e a sintaxe necessária para criar um ASE ILB com modelos do Azure Resource Manager.

Há três etapas envolvidas na automatização da criação de um ASE ILB:

1. Primeiro o ASE base é criado em uma rede virtual usando um endereço de balanceador de carga interno em vez de um VIP público.  Como parte dessa etapa, um nome de domínio raiz é atribuído ao ASE ILB.
2. Depois que o ILB ASE é criado, um certificado SSL é carregado.  
3. O certificado SSL carregado foi atribuído explicitamente à ASE ILB como o certificado SSL "padrão".  Esse certificado SSL será usado para o tráfego SSL dos aplicativos no ASE ILB quando eles são endereçados utilizando o domínio-raiz comum atribuído ao ASE (por exemplo, https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Criando o ASE ILB base
Um modelo do Azure Resource Manager de exemplo e seu arquivo de parâmetros associado estão disponíveis no GitHub [aqui][quickstartilbasecreate].

A maioria dos parâmetros no arquivo *azuredeploy.parameters.json* são comuns para criar os ASEs ILB, bem como ASEs associados a um VIP público.  A lista abaixo, ao criar um ASE ILB, chama parâmetros de anotação especial ou exclusivos:

* *interalLoadBalancingMode*: na maioria dos casos, define para 3, o que significa que o tráfego HTTP/HTTPS nas portas 80/443 e as portas do canal de dados/controle atendidas pelo serviço FTP no ASE serão associados a um endereço interno da rede virtual alocada do ILB.  Se essa propriedade for definida como 2, somente as portas relacionadas ao serviço FTP (canais de controle e de dados) serão associadas a um endereço ILB, ao passo que o tráfego HTTP/HTTPS permanecerá no VIP público.
* *dnsSuffix*: esse parâmetro define o domínio-raiz padrão que será atribuído ao ASE.  A variação pública do Serviço de Aplicativo do Azure, o domínio de raiz padrão para todos os aplicativos Web, é *azurewebsites.net*.  No entanto, como um ASE ILB é interno na rede virtual do cliente, não faz sentido usar o domínio de raiz padrão do serviço público.  Em vez disso, um ASE ILB deve ter um domínio de raiz padrão que faça sentido para uso dentro da rede virtual interna da empresa.  Por exemplo, uma empresa hipotética Contoso pode usar um domínio raiz padrão *internal-contoso.com* para aplicativos que se destinam apenas a serem resolvidos e acessados na rede virtual da Contoso. 
* *ipSslAddressCount*: esse parâmetro é automaticamente padronizado para um valor 0 no arquivo *azuredeploy.json* porque os ASEs ILB têm apenas um único endereço ILB.  Não há nenhum endereço IP SSL explícito para um ASE ILB e, portanto, o pool de endereços IP SSL para um ASE ILB deve ser definido como zero. Caso contrário, ocorrerá um erro de provisionamento. 

Quando o arquivo *azuredeploy.parameters.json* for preenchido para um ASE ILB, o ASE ILB pode ser criado usando o trecho de código do Powershell a seguir.  Altere os CAMINHOS do arquivo para corresponder ao local em que os arquivos do modelo do Azure Resource Manager estão localizados no seu computador.  Além disso, lembre-se de fornecer seus próprios valores para o nome de implantação do Azure Resource Manager e para o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois que o modelo do Azure Resource Manager for enviado, levará algumas horas para o ASE ILB ser criado.  Uma vez concluída a criação, o ASE ILB aparecerá no portal de experiência do usuário na lista de Ambientes de Serviço de Aplicativo para a assinatura que disparou a implantação.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Carregando e configurando o certificado SSL "Padrão"
Quando o ILB ASE é criado, um certificado SSL deve ser associado ao ASE como o certificado SSL “padrão” usado para estabelecer conexões SSL com aplicativos.  Continuando com o exemplo hipotético da Contoso Corporation, se o sufixo DNS padrão do ASE for *internal-contoso.com*, então, uma conexão com *https://some-random-app.internal-contoso.com* irá requerer um certificado SSL válido para **.internal-contoso.com*. 

Há várias maneiras de obter um certificado SSL válido, incluindo CAs internos, adquirir certificado de um emissor externo e usar um certificado autoassinado.  Independentemente da origem do certificado SSL, os seguintes atributos de certificado devem ser configurados corretamente:

* *Assunto*: esse atributo deve ser definido para **.domínio-raiz-aqui.com*
* *Nome Alternativo da Entidade*: esse atributo deve incluir **.your-root-domain-here.com* e **.scm.your-root-domain-here.com*.  O motivo para a segunda entrada é que as conexões SSL para o site SCM/Kudu associado a cada aplicativo serão feitas usando um endereço do formulário *nome-do-aplicativo.scm.seu-domínio-raiz-aqui.com*.

Com um certificado SSL válido em mãos, são necessárias mais duas etapas preparatórias.  O certificado SSL deve ser convertido/salvo como um arquivo.pfx.  Lembre-se de que o arquivo .pfx deve incluir todos os certificados intermediários e raiz e também precisa ser protegido com uma senha.

Em seguida, o arquivo .pfx resultante deve ser convertido em uma cadeia de caracteres de base64 porque o certificado SSL será carregado usando um modelo do Azure Resource Manager.  Já que os modelos do Azure Resource Manager são arquivos de texto, o arquivo .pfx deve ser convertido em uma cadeia de caracteres de base64 para poder ser incluída como um parâmetro do modelo.

O trecho de código do Powershell abaixo mostra um exemplo de como gerar um certificado autoassinado, exportar o certificado como um arquivo .pfx, converter o arquivo .pfx em uma cadeia de caracteres codificada em base64 e salvar essa cadeia de caracteres em um arquivo separado.  O código do PowerShell para a codificação de base64 foi adaptado do [Blog de Scripts do PowerShell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Depois que o certificado SSL for gerado com êxito e convertido em uma cadeia de caracteres codificada em base64, o modelo do Azure Resource Manager de exemplo no GitHub para [configurar o certificado SSL padrão][configuringDefaultSSLCertificate] poderá ser usado.

Os parâmetros no arquivo *azuredeploy.parameters.json* estão listados abaixo:

* *appServiceEnvironmentName*: o nome do ASE ILB sendo configurado.
* *existingAseLocation*: a cadeia de caracteres de texto que contém a região do Azure onde o ASE ILB foi implantado.  Por exemplo: "Centro-Sul dos EUA".
* *pfxBlobString*: a representação de cadeia de caracteres codificada em base 64 do arquivo .pfx.  Usando o trecho de código mostrado anteriormente, copie a cadeia de caracteres contida no "exportedcert.pfx.b64" e cole-a como o valor do atributo *pfxBlobString* .
* *password*: a senha usada para proteger o arquivo .pfx.
* *certificateThumbprint*: a impressão digital do certificado.  Se você recuperar esse valor do Powershell (por exemplo, *$certificate.Thumbprint* do trecho de código anterior), poderá usar o valor como estiver.  No entanto se você copiar o valor da caixa de diálogo Certificado Windows, lembre-se de retirar os espaços estranhos.  O *certificateThumbprint* deve ser semelhante a: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: um identificador da cadeia de caracteres amigável de sua escolha usado para identificar o certificado.  O nome é usado como parte do identificador exclusivo do Azure Resource Manager para a entidade *Microsoft.Web/certificates* que representa o certificado SSL.  O nome **deve** terminar com o seguinte sufixo:  \_seuNomeASEAqui_InternalLoadBalancingASE.  Esse sufixo é usado pelo portal como um indicador de que o certificado é usado para proteger um ASE habilitado por ILB.

Um exemplo abreviado de *azuredeploy.parameters.json* é mostrado abaixo:

    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
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

Quando o arquivo *azuredeploy.parameters.json* tiver sido preenchido, o certificado SSL padrão poderá ser configurado usando o trecho de código do Powershell a seguir.  Altere os CAMINHOS do arquivo para corresponder ao local em que os arquivos do modelo do Azure Resource Manager estão localizados no seu computador.  Além disso, lembre-se de fornecer seus próprios valores para o nome de implantação do Azure Resource Manager e para o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois que o modelo do Azure Resource Manager for enviado, levará aproximadamente quarenta minutos por front-end do ASE para aplicar a alteração.  Por exemplo, com um ASE padrão dimensionado usando dois front-ends, o modelo levará aproximadamente uma hora e vinte minutos para concluir.  Enquanto o modelo estiver em execução, o ASE não poderá ser dimensionado.  

Quando o modelo for concluído, os aplicativos no ASE ILB poderão ser acessados via HTTPS e as conexões serão protegidas usando o certificado SSL padrão.  O certificado SSL padrão será usado quando aplicativos no ASE ILB forem endereçados utilizando uma combinação de nome do aplicativo com o nome de host padrão.  Por exemplo *https://mycustomapp.internal-contoso.com* usaria o certificado SSL padrão para **.internal-contoso.com*.

No entanto, assim como os aplicativos em execução no serviço público multilocatário, os desenvolvedores podem também configurar nomes de host personalizados para aplicativos individuais e configurar associações de certificado SSL SNI exclusivas para aplicativos individuais.  

## <a name="getting-started"></a>Introdução
Para se familiarizar com os ambientes de serviço de aplicativo, consulte [Introdução ao ambiente do serviço de aplicativo](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

