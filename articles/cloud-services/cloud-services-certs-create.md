---
title: Serviços de nuvem e certificados de gerenciamento | Microsoft Docs
description: Saiba como criar e usar certificados com o Microsoft Azure
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: 3c84c6832856986a45be7d275fb94a6c5fc066f0
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359198"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Visão geral sobre certificados para os Serviços de Nuvem do Azure
Os certificados são usados no Azure para serviços de nuvem ([certificados de serviço](#what-are-service-certificates)) e para a autenticação com o gerenciamento de API ([certificados de gerenciamento](#what-are-management-certificates)). Esse tópico fornece uma visão geral de ambos os tipos de certificado, como [criá-los](#create) e implantá-los no Azure.

Os certificados usados no Azure são certificados x.509 v3, que podem ser assinados por outro certificado confiável ou ser autoassinados. Um certificado autoassinado é assinado por seu próprio criador, portanto não é considerado confiável por padrão. A maioria dos navegadores pode ignorar esse problema. Você só deve usar certificados autoassinados ao desenvolver e testar seus serviços de nuvem. 

Os certificados usados pelo Azure podem conter uma chave particular ou pública. Certificados têm uma impressão digital, que é um meio de identificá-los de maneira inequívoca. Essa impressão digital é usada no [arquivo de configuração](cloud-services-configure-ssl-certificate-portal.md) do Azure para identificar qual certificado um serviço de nuvem deve usar. 

>[!Note]
>O Azure Cloud Services não aceita o certificado criptografado por AES256-SHA256.

## <a name="what-are-service-certificates"></a>O que são certificados de serviço?
Certificados de serviço são anexados aos serviços de nuvem e possibilitam que a comunicação feita com e por meio deles seja segura. Por exemplo, se você implantou uma função web, convém fornecer um certificado que pode autenticar um ponto de extremidade HTTPS exposto. Certificados de serviço, definidos em sua definição de serviço, são implantados automaticamente na máquina virtual que está executando uma instância de sua função. 

Você pode carregar certificados de serviço para o Azure acessando o portal do Azure ou utilizando o modelo de implantação clássico. Os certificados de serviço são associados um serviço de nuvem específico. Eles são atribuídos a uma implantação no arquivo de definição de serviço.

Certificados de serviço podem ser gerenciados separadamente de seus serviços e podem ser gerenciados por indivíduos diferentes. Por exemplo, um desenvolvedor pode carregar um pacote de serviço que se refere a um certificado que um gerente de TI carregou anteriormente no Azure. Um gerente de TI pode gerenciar e renovar esse certificado alterando a configuração do serviço sem precisar carregar um novo pacote de serviço. A atualização sem um novo pacote de serviço é possível porque o nome lógico, o nome do repositório e o local do certificado estão no arquivo de definição de serviço, enquanto que a impressão digital do certificado está especificada no arquivo de configuração de serviço. Para atualizar o certificado, é necessário apenas carregar um novo certificado e alterar o valor da impressão digital no arquivo de configuração de serviço.

>[!Note]
>O artigo [perguntas frequentes sobre serviços de nuvem – configuração e gerenciamento](cloud-services-configuration-and-management-faq.md) tem algumas informações úteis sobre certificados.

## <a name="what-are-management-certificates"></a>O que são certificados de gerenciamento?
Os certificados de gerenciamento permitem que você autentique com o modelo de implantação clássico. Muitos programas e ferramentas (como o Visual Studio ou o SDK do Azure) usam esses certificados para automatizar a configuração e a implantação de diversos serviços do Azure. Eles serviços não estão, na verdade, relacionados a serviços de nuvem. 

> [!WARNING]
> Portanto, tenha cuidado! Esses tipos de certificados permitem que qualquer pessoa que os utilize para autenticação gerencie a assinatura à qual eles estão associados. 
> 
> 

### <a name="limitations"></a>Limitações
Há um limite de 100 certificados de gerenciamento por assinatura. Também há um limite de 100 certificados de gerenciamento para todas as assinaturas com a ID de usuário de um administrador de serviços específico. Se a ID de usuário do administrador da conta já foi usada para adicionar 100 certificados de gerenciamento e houver necessidade de mais certificados, você pode adicionar um coadministrador para adicionar esses certificados. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Criar um Novo certificado autoassinado
Você pode usar qualquer ferramenta disponível para criar um certificado autoassinado, desde que ela esteja de acordo com estas configurações:

* Um certificado X.509.
* Contém uma chave privada.
* Criado para troca de chaves (arquivo. pfx).
* O nome de assunto deve corresponder ao domínio usado para acessar o serviço de nuvem.

    > Você não pode adquirir um certificado SSL para o domínio cloudapp.net (ou para qualquer um relacionado ao Azure); o nome de assunto do certificado deve corresponder ao nome do domínio personalizado usado para acessar o aplicativo. Por exemplo, **contoso.net** e não **contoso.cloudapp.net**.

* Mínimo de criptografia de 2048 bits.
* **Apenas Certificado de Serviço**: o certificado do cliente deve residir no repositório de certificados *Pessoal*.

Há duas maneiras fáceis de criar um certificado no Windows, com o utilitário `makecert.exe` ou o IIS.

### <a name="makecertexe"></a>Makecert.exe
Esse utilitário foi preterido e não está documentado aqui. Para obter mais informações, consulte [este artigo do MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Se você quiser usar o certificado com um endereço IP em vez de um domínio, use o endereço IP no parâmetro - DnsName.


Se você quiser usar este [certificado com o portal de gerenciamento](../azure-api-management-certs.md), exporte-o para um arquivo **.cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>IIS (Serviços de Informações da Internet)
Há muitas páginas na Internet que tratam de como fazer isso com o IIS. [Este](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) é um ótimo site que descobri e que acho que explica bem. 

### <a name="linux"></a>Linux
[Este](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigo descreve como criar certificados com SSH.

## <a name="next-steps"></a>Próximas etapas
[Carregar seu certificado de serviço no portal do Azure](cloud-services-configure-ssl-certificate-portal.md).

Carregar um [certificado de gerenciamento de API](../azure-api-management-certs.md) no portal do Azure.

