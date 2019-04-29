---
title: Usar o certificado SSL de cliente no código do aplicativo – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como usar certificados de cliente para se conectar a recursos remotos que necessitam deles.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 763aadc50a8760b4265dbfc21e9278f909b68433
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851080"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Usar um certificado SSL no código do aplicativo no Serviço de Aplicativo do Azure

Este guia de instruções mostra como usar um dos certificados SSL carregado ou importado no aplicativo do Serviço de Aplicativo no código do aplicativo. Um exemplo do caso de uso é que seu aplicativo acessa um serviço externo que exige autenticação de certificado. 

Essa abordagem de uso dos certificados SSL no código utiliza a funcionalidade do SSL no Serviço de Aplicativo, sendo necessário que seu aplicativo esteja na camada **Básica** ou superior. Uma alternativa é incluir o arquivo de certificado no diretório do aplicativo e carregá-lo diretamente (consulte [Alternative: load certificate as a file](#file) [Alternativa: carregar o certificado como um arquivo]). No entanto, essa alternativa não permite ocultar a chave privada no certificado do código do aplicativo ou o desenvolvedor. Além disso, se o código do seu aplicativo estiver em um repositório de software livre, manter um certificado com uma chave privada no repositório não será uma opção.

Ao permitir que o Serviço de Aplicativo gerencie os certificados SSL, é possível manter os certificados e o código do aplicativo separados e proteger seus dados confidenciais.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções:

- [Crie um aplicativo do Serviço de Aplicativo](/azure/app-service/)
- [Mapear um nome DNS personalizado para o aplicativo Web](app-service-web-tutorial-custom-domain.md)
- [Carregar o certificado SSL](app-service-web-tutorial-custom-ssl.md) ou [importar um Certificado do Serviço de Aplicativo](web-sites-purchase-ssl-web-site.md) para o aplicativo Web


## <a name="load-your-certificates"></a>Carregar seus certificados

Para usar um certificado carregado ou importado para o Serviço de Aplicativo, primeiro, disponibilize-o para o código do aplicativo. Isso é feito com a configuração de aplicativo `WEBSITE_LOAD_CERTIFICATES`.

No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, abra a página do aplicativo Web.

No painel de navegação esquerdo, clique em **Certificados SSL**.

![Certificado carregado](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Todos os certificados SSL carregados e importados para esse aplicativo Web são mostrados aqui com suas impressões digitais. Copie a impressão digital do certificado que deseja utilizar.

No painel de navegação esquerdo, clique em **Configurações do aplicativo**.

Adicione uma configuração de aplicativo chamada `WEBSITE_LOAD_CERTIFICATES` e defina seu valor para a impressão digital do certificado. Para disponibilizar vários certificados, use valores de impressão digital separados por vírgulas. Para disponibilizar todos os certificados, defina o valor como `*`. Observe que isso colocará o certificado no repositório `CurrentUser\My`.

![Definir a configuração do aplicativo](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Ao terminar, clique em **Salvar**.

Agora, o certificado configurado está pronto para ser usado pelo código.

## <a name="use-certificate-in-c-code"></a>Usar o certificado no código C#

Quando o certificado estiver disponível, será possível acessá-lo no código C# por meio da impressão digital do certificado. O código a seguir carrega um certificado com a impressão digital `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="alternative-load-certificate-as-a-file"></a>Alternativa: carregar o certificado como um arquivo

Esta seção mostra como carregar um arquivo de certificado que está no diretório de aplicativos. 

O exemplo de C# a seguir carrega um certificado chamado `mycert.pfx` do diretório `certs` do repositório do aplicativo.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

