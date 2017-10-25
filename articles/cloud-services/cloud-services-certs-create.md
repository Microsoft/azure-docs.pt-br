---
title: "Serviços de nuvem e certificados de gerenciamento | Microsoft Docs"
description: Saiba como criar e usar certificados com o Microsoft Azure
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 689977491e6df37e48536f59234bf4ddba6a1575
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Visão geral sobre certificados para os Serviços de Nuvem do Azure
Os certificados são usados no Azure para os serviços de nuvem ([certificados de serviço](#what-are-service-certificates)) e para a autenticação na API de gerenciamento ([certificados de gerenciamento](#what-are-management-certificates) ao usar o Portal Clássico do Azure e não o Portal não clássico do Azure). Esse tópico fornece uma visão geral de ambos os tipos de certificado, como [criá-los](#create) e [implantá-los](#deploy) no Azure.

Os certificados usados no Azure são certificados x.509 v3, que podem ser assinados por outro certificado confiável ou ser autoassinados. Um certificado autoassinado é assinado por seu próprio criador, portanto não é considerado confiável por padrão. A maioria dos navegadores pode ignorar esse problema. Você só deve usar certificados autoassinados ao desenvolver e testar seus serviços de nuvem. 

Os certificados usados pelo Azure podem conter uma chave particular ou pública. Certificados têm uma impressão digital, que é um meio de identificá-los de maneira inequívoca. Essa impressão digital é usada no [arquivo de configuração](cloud-services-configure-ssl-certificate.md) do Azure para identificar qual certificado um serviço de nuvem deve usar. 

## <a name="what-are-service-certificates"></a>O que são certificados de serviço?
Certificados de serviço são anexados aos serviços de nuvem e possibilitam que a comunicação feita com e por meio deles seja segura. Por exemplo, se você implantou uma função web, convém fornecer um certificado que pode autenticar um ponto de extremidade HTTPS exposto. Certificados de serviço, definidos em sua definição de serviço, são implantados automaticamente na máquina virtual que está executando uma instância de sua função. 

Você pode carregar certificados de serviço no Portal Clássico do Azure usando o Portal Clássico do Azure ou o modelo de implantação clássico. Os certificados de serviço são associados um serviço de nuvem específico. Eles são atribuídos a uma implantação no arquivo de definição de serviço.

Certificados de serviço podem ser gerenciados separadamente de seus serviços e podem ser gerenciados por indivíduos diferentes. Por exemplo, um desenvolvedor pode carregar um pacote de serviço que se refere a um certificado que um gerente de TI carregou anteriormente no Azure. Um gerente de TI pode gerenciar e renovar esse certificado alterando a configuração do serviço sem precisar carregar um novo pacote de serviço. A atualização sem um novo pacote de serviço é possível porque o nome lógico, o nome do repositório e o local do certificado estão no arquivo de definição de serviço, enquanto que a impressão digital do certificado está especificada no arquivo de configuração de serviço. Para atualizar o certificado, é necessário apenas carregar um novo certificado e alterar o valor da impressão digital no arquivo de configuração de serviço.

>[!Note]
>O artigo [Perguntas frequentes sobre Serviços de Nuvem](cloud-services-faq.md) tem algumas informações úteis sobre certificados.

## <a name="what-are-management-certificates"></a>O que são certificados de gerenciamento?
Os certificados de gerenciamento permitem que você autentique com o modelo de implantação clássico. Muitos programas e ferramentas (como o Visual Studio ou o SDK do Azure) usam esses certificados para automatizar a configuração e a implantação de diversos serviços do Azure. Eles serviços não estão, na verdade, relacionados a serviços de nuvem. 

> [!WARNING]
> Portanto, tenha cuidado! Esses tipos de certificados permitem que qualquer pessoa que os utilize para autenticação gerencie a assinatura à qual eles estão associados. 
> 
> 

### <a name="limitations"></a>Limitações
Há um limite de 100 certificados de gerenciamento por assinatura. Também há um limite de 100 certificados de gerenciamento para todas as assinaturas com a ID de usuário de um administrador de serviços específico. Se a ID de usuário do administrador da conta já foi usada para adicionar 100 certificados de gerenciamento e houver necessidade de mais certificados, você pode adicionar um coadministrador para adicionar esses certificados. 

Antes de adicionar mais de 100 certificados, veja se é possível reutilizar um certificado existente. O uso de coadministradores adiciona uma complexidade potencial desnecessária ao processo de gerenciamento de certificados.

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Criar um Novo certificado autoassinado
Você pode usar qualquer ferramenta disponível para criar um certificado autoassinado, desde que ela esteja de acordo com estas configurações:

* Um certificado X.509.
* Contém uma chave privada.
* Criado para troca de chaves (arquivo. pfx).
* O nome de assunto deve corresponder ao domínio usado para acessar o serviço de nuvem.

    > Você não pode adquirir um certificado SSL para o domínio cloudapp.net (ou para qualquer um relacionado ao Azure); o nome de assunto do certificado deve corresponder ao nome do domínio personalizado usado para acessar o aplicativo. Por exemplo, **contoso.net** e não **contoso.cloudapp.net**.

* Mínimo de criptografia de 2048 bits.
* **Apenas Certificado de Serviço**: o certificado do cliente deve residir no repositório de certificados *Pessoal* .

Há duas maneiras fáceis de criar um certificado no Windows, com o utilitário `makecert.exe` ou o IIS.

### <a name="makecertexe"></a>Makecert.exe
Esse utilitário foi preterido e não está documentado aqui. Para obter mais informações, consulte [este artigo do MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
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
[Carregue seu certificado de serviço no Portal Clássico do Azure](cloud-services-configure-ssl-certificate.md) (ou no [Portal do Azure](cloud-services-configure-ssl-certificate-portal.md)).

Carregue um [certificado de API de gerenciamento](../azure-api-management-certs.md) no portal clássico do Azure. O portal do Azure não usa certificados de gerenciamento para autenticação.

