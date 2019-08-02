---
title: Certificados e o Ambiente do Serviço de Aplicativo - Azure
description: Explica diversos tópicos relacionados a certificados em um ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ba34638bbdb838adc6f1e61b1f8b07a6915815c0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540773"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificados e o Ambiente do Serviço de Aplicativo 

O ASE (Ambiente do Serviço de Aplicativo) é uma implantação do Serviço de Aplicativo do Azure que é executada na sua VNet (Rede Virtual) do Azure. Ele pode ser implantado com um ponto de extremidade do aplicativo acessível pela internet ou um ponto de extremidade do aplicativo que esteja em sua rede virtual. Se você implantar o ASE com um ponto de extremidade acessível pela Internet, a implantação será chamada de ASE externo. Se você implantar o ASE com um ponto de extremidade em sua rede virtual, a implantação será chamada de ILB ASE. Você pode aprender mais sobre o ILB ASE no documento [Criar e usar um ILB ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

O ASE é um sistema de locatário único. Como ele é de locatário único, há alguns recursos disponíveis apenas para ASE que não esteja disponível no Serviço de Aplicativo multilocatário. 

## <a name="ilb-ase-certificates"></a>Certificados de ILB ASE 

Se você estiver usando um ASE externo, seus aplicativos serão acessados em [appname].[asename].p.azurewebsites.net. Por padrão, todos os ASEs, até mesmo os ASEs ILB, são criados com certificados que seguem esse formato. Quando você tem um ILB ASE, os aplicativos são acessados com base no nome de domínio que você especifica ao criar o ILB ASE. Para que os aplicativos sejam compatíveis com SSL, você precisa carregar certificados. Obtenha um certificado SSL válido, usando autoridades de certificação internas, adquirindo um certificado de um emissor externo ou usando um certificado autoassinado. 

Há duas opções para configurar certificados com o ILB ASE.  Você pode definir um certificado padrão curinga para o ILB ASE ou definir certificados nos aplicativos Web individuais no ASE.  Independentemente da sua escolha, os seguintes atributos de certificado devem ser configurados corretamente:

- **Entidade:** Esse atributo deve ser definido como *.[seu-domínio-raiz-aqui] para um certificado de ILB ASE curinga. Se estiver criando o certificado para seu aplicativo, então ele deverá ser [appname].[seu-domínio-raiz-aqui]
- **Nome Alternativo da Entidade**: Esse atributo deve incluir *.[seu-domínio-raiz-aqui] e *.scm.[seu-domínio-raiz-aqui] para o certificado de ILB ASE curinga. Se estiver criando o certificado para seu aplicativo, então ele deverá ser [appname].[seu-domínio-raiz-aqui] e [appname].scm.[seu-domínio-raiz-aqui].

Como uma terceira variante, você pode criar um certificado de ILB ASE que inclua todos os nomes de aplicativo individuais na SAN do certificado em vez de usar uma referência de curinga. O problema com esse método é que você precisará saber com antecedência os nomes dos aplicativos que estiver colocando no ASE ou precisará ficar atualizando o certificado ILB ASE.

### <a name="upload-certificate-to-ilb-ase"></a>Carregar certificado ao ILB ASE 

Depois que um ILB ASE é criado no portal, o certificado deve ser definido para o ILB ASE. Até que o certificado seja definido, o ASE mostrará uma faixa indicando que o certificado não foi definido.  

O certificado a ser carregado deverá ser um arquivo .pfx. Depois que o certificado for carregado, o ASE executará uma operação de escala para definir o certificado. 

Você não pode criar o ASE e carregar o certificado como uma única ação no portal ou até mesmo em um único modelo. Como uma ação separada, você pode carregar o certificado usando um modelo, conforme descrito no documento [Criar um ASE usando um modelo](./create-from-template.md).  

Se desejar criar um certificado autoassinado rapidamente para testar, você poderá usar o seguinte trecho de PowerShell:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Quando criar um self assinado cert, você precisará garantir que o nome da entidade tem o formato de CN = {ASE_NAME_HERE} _InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certificados de aplicativo 

Aplicativos que são hospedados em um ASE podem usar os recursos de certificado centrado em aplicativos que estão disponíveis no Serviço de Aplicativo multilocatário. Esses recursos incluem:  

- Certificados SNI 
- SSL com base em IP, que só é compatível com um ASE externo.  Um ILB ASE não é compatível com SSL com base em IP.
- Certificados hospedados no Key Vault 

As instruções para carregar e gerenciar esses certificados estão disponíveis no tutorial SSL https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl do Serviço de Aplicativo.  Se você estiver simplesmente configurando certificados de acordo com um nome de domínio personalizado que você atribuiu ao seu aplicativo Web, essas instruções serão suficientes. Se você estiver carregando o certificado para um aplicativo Web de ILB ASE com o nome de domínio padrão, especifique o site SCM na SAN do certificado, conforme observado anteriormente. 

## <a name="tls-settings"></a>Configurações de protocolo TLS 

Você pode definir a configuração de TLS em um nível de aplicativo.  

## <a name="private-client-certificate"></a>Certificado de cliente privado 

Um caso de uso comum é configurar o aplicativo como um cliente em um modelo cliente-servidor. Se você proteger seu servidor com um Certificado de Autoridade de Certificação privado, será necessário carregar o certificado de cliente em seu aplicativo.  As instruções a seguir carregarão certificados no repositório confiável das funções de trabalho em que seu aplicativo está sendo executado. Se você carregar o certificado em um aplicativo, poderá usá-lo com seus outros aplicativos no mesmo Plano do Serviço de Aplicativo sem ter que carregar o certificado novamente.

Para carregar o certificado em seu aplicativo no ASE:

1. Gere um arquivo *.cer* para seu certificado. 
2. Acesse o aplicativo que precisa do certificado no portal do Azure
3. Acesse as configurações de SSL no aplicativo. Clique em Carregar Certificado. Selecione Público. Selecione Computador Local. Forneça um nome. Procure e selecione seu arquivo *.cer*. Selecionar carregar. 
4. Copie a impressão digital.
5. Acesse Configurações do Aplicativo. Crie uma Configuração de Aplicativo WEBSITE_LOAD_ROOT_CERTIFICATES com a impressão digital como o valor. Se você tiver vários certificados, poderá colocá-los na mesma configuração separada por vírgulas e sem nenhum espaço em branco, como 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

O certificado ficará disponível para todos os aplicativos no mesmo Plano do Serviço de Aplicativo que definiu essa configuração. Se você precisar que ele fique disponível para aplicativos de outro Plano do Serviço de Aplicativo, será necessário repetir a operação de Configuração de Aplicativo em um aplicativo desse Plano do Serviço de Aplicativo. Para verificar se o certificado está definido, vá para o console do Kudu e emita o seguinte comando no console de depuração do PowerShell:

    dir cert:\localmachine\root

Para executar testes, você pode criar um certificado autoassinado e gerar um arquivo *.cer* com o PowerShell a seguir: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

