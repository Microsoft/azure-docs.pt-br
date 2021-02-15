---
title: Usar um certificado TLS/SSL no código
description: Saiba como usar certificados de cliente em seu código. Autentique com recursos remotos com um certificado de cliente ou execute tarefas criptográficas com eles.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b4e184f827875ebebd40ab976ef63e77ee702d49
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126032"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Usar um certificado TLS/SSL no seu código no Serviço de Aplicativo do Azure

No código do aplicativo, você pode acessar os [certificados públicos ou privados adicionados ao serviço de aplicativo](configure-ssl-certificate.md). O código do aplicativo pode atuar como um cliente e acessar um serviço externo que requer autenticação de certificado ou pode precisar executar tarefas criptográficas. Este guia de instruções mostra como usar certificados públicos ou privados no código do aplicativo.

Essa abordagem para usar certificados em seu código usa a funcionalidade TLS no serviço de aplicativo, o que exige que seu aplicativo esteja na camada **básica** ou acima. Se seu aplicativo estiver em uma camada **gratuita** ou **compartilhada** , você poderá [incluir o arquivo de certificado em seu repositório de aplicativos](#load-certificate-from-file).

Quando você permite que o serviço de aplicativo gerencie seus certificados TLS/SSL, você pode manter os certificados e o código do aplicativo separadamente e proteger seus dados confidenciais.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, é necessário ter:

- [Crie um aplicativo do Serviço de Aplicativo](./index.yml)
- [Adicionar um certificado ao seu aplicativo](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Localizar a impressão digital

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **Serviços de Aplicativos** >  **\<app-name>** .

No painel de navegação à esquerda do seu aplicativo, selecione **configurações de TLS/SSL** e, em seguida, selecione **certificados de chave privada (. pfx)** ou **certificados de chave pública (. cer)** .

Localize o certificado que você deseja usar e copie a impressão digital.

![Copiar a impressão digital do certificado](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Tornar o certificado acessível

Para acessar um certificado em seu código do aplicativo, adicione sua impressão digital à `WEBSITE_LOAD_CERTIFICATES` configuração do aplicativo, executando o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Para tornar todos os certificados acessíveis, defina o valor como `*` .

## <a name="load-certificate-in-windows-apps"></a>Carregar o certificado em aplicativos do Windows

A `WEBSITE_LOAD_CERTIFICATES` configuração do aplicativo torna os certificados especificados acessíveis para seu aplicativo hospedado do Windows no repositório de certificados do Windows, no [User\My atual](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

No código C#, você acessa o certificado pela impressão digital do certificado. O código a seguir carrega um certificado com a impressão digital `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

No código Java, você acessa o certificado do repositório "Windows-MY" usando o campo nome comum da entidade (consulte [certificado de chave pública](https://en.wikipedia.org/wiki/Public_key_certificate)). O código a seguir mostra como carregar um certificado de chave privada:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Para idiomas que não dão suporte ou oferecem suporte insuficiente para o repositório de certificados do Windows, consulte [carregar certificado do arquivo](#load-certificate-from-file).

## <a name="load-certificate-from-file"></a>Carregar o certificado do arquivo

Se você precisar carregar um arquivo de certificado que é carregado manualmente, é melhor carregar o certificado usando [FTPS](deploy-ftp.md) em vez de [git](deploy-local-git.md), por exemplo. Você deve manter dados confidenciais como um certificado privado fora do controle do código-fonte.

> [!NOTE]
> ASP.NET e ASP.NET Core no Windows devem acessar o repositório de certificados mesmo se você carregar um certificado de um arquivo. Para carregar um arquivo de certificado em um aplicativo do Windows .NET, carregue o perfil do usuário atual com o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Essa abordagem para usar certificados em seu código usa a funcionalidade TLS no serviço de aplicativo, o que exige que seu aplicativo esteja na camada **básica** ou acima.

O exemplo C# a seguir carrega um certificado público de um caminho relativo em seu aplicativo:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver como carregar um certificado TLS/SSL de um arquivo em Node.js, PHP, Python, Java ou Ruby, consulte a documentação do respectivo idioma ou plataforma da Web.

## <a name="load-certificate-in-linuxwindows-containers"></a>Carregar o certificado em contêineres do Linux/Windows

As `WEBSITE_LOAD_CERTIFICATES` configurações do aplicativo tornam os certificados especificados acessíveis para seus aplicativos de contêiner do Windows ou do Linux (incluindo contêineres do Linux internos) como arquivos. Os arquivos são encontrados nos seguintes diretórios:

| Plataforma de contêiner | Certificados públicos | Certificados privados |
| - | - | - |
| Contêiner do Windows | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Contêiner do Linux | `/var/ssl/certs` | `/var/ssl/private` |

Os nomes de arquivo de certificado são as impressões digitais do certificado. 

> [!NOTE]
> O serviço de aplicativo injeta os caminhos de certificado em contêineres do Windows como as variáveis de ambiente a seguir `WEBSITE_PRIVATE_CERTS_PATH` ,, `WEBSITE_INTERMEDIATE_CERTS_PATH` `WEBSITE_PUBLIC_CERTS_PATH` e `WEBSITE_ROOT_CERTS_PATH` . É melhor fazer referência ao caminho do certificado com as variáveis de ambiente em vez de codificar o caminho do certificado, caso os caminhos do certificado sejam alterados no futuro.
>

Além disso, os [contêineres do Windows Server Core](configure-custom-container.md#supported-parent-images) carregam os certificados no repositório de certificados automaticamente, em **LocalMachine\My** . Para carregar os certificados, siga o mesmo padrão que [carregar certificado em aplicativos do Windows](#load-certificate-in-windows-apps). Para contêineres baseados no Windows nano, use os caminhos de arquivo fornecidos acima para [carregar o certificado diretamente do arquivo](#load-certificate-from-file).

O código C# a seguir mostra como carregar um certificado público em um aplicativo Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver como carregar um certificado TLS/SSL de um arquivo em Node.js, PHP, Python, Java ou Ruby, consulte a documentação do respectivo idioma ou plataforma da Web.

## <a name="more-resources"></a>Mais recursos

* [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md)
* [Impor HTTPS](configure-ssl-bindings.md#enforce-https)
* [Impor o TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Perguntas frequentes: Certificados do Serviço de Aplicativo](./faq-configuration-and-management.md)