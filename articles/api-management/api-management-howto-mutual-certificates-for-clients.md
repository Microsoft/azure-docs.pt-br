---
title: Proteger APIs usando a autenticação de certificado do cliente no gerenciamento de API
titleSuffix: Azure API Management
description: Saiba como proteger o acesso às APIs usando certificados de cliente. Você pode usar expressões de política para validar certificados de entrada.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 553b4527796db3e5d0f430afd6c5e614626187e5
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988885"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como proteger APIs usando a autenticação de certificado do cliente no Gerenciamento de API

O Gerenciamento de API fornece a capacidade de proteger o acesso às APIs (isto é, cliente para Gerenciamento de API) usando certificados do cliente. Você pode validar o certificado de entrada e verificar as propriedades do certificado em relação aos valores desejados usando expressões de política.

Para obter informações sobre como proteger o acesso ao serviço de back-end de uma API usando certificados de cliente (ou seja, gerenciamento de API para back-end), consulte [como proteger serviços de back-end usando autenticação de certificado de cliente](./api-management-howto-mutual-certificates.md)

> [!IMPORTANT]
> Para receber e verificar certificados de cliente por HTTP/2 nas camadas Developer, Basic, Standard ou Premium, você deve ativar a configuração "negociar certificado de cliente" na folha "domínios personalizados", conforme mostrado abaixo.

![Negociar certificado do cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Para receber e verificar os certificados do cliente na camada de consumo, você deve ativar a configuração "solicitar certificado do cliente" na folha "domínios personalizados", conforme mostrado abaixo.

![Solicitar certificado do cliente](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Verificando o emissor e a entidade

As políticas abaixo podem ser configuradas para verificar o emissor e a entidade de um certificado do cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para desabilitar a verificação do uso da lista de certificados revogados `context.Request.Certificate.VerifyNoRevocation()` em vez de `context.Request.Certificate.Verify()` .
> Se o certificado do cliente for autoassinado, os certificados de autoridade de certificação raiz (ou intermediário) deverão ser [carregados](api-management-howto-ca-certificates.md) no gerenciamento de API para o `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` para funcionar.

## <a name="checking-the-thumbprint"></a>Verificando a impressão digital

Veja abaixo que as políticas podem ser configuradas para verificar a impressão digital de um certificado do cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para desabilitar a verificação do uso da lista de certificados revogados `context.Request.Certificate.VerifyNoRevocation()` em vez de `context.Request.Certificate.Verify()` .
> Se o certificado do cliente for autoassinado, os certificados de autoridade de certificação raiz (ou intermediário) deverão ser [carregados](api-management-howto-ca-certificates.md) no gerenciamento de API para o `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` para funcionar.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Verificação de uma impressão digital em relação a certificados carregados no Gerenciamento de API

O exemplo a seguir mostra como verificar a impressão digital de um certificado do cliente em relação a certificados carregados no Gerenciamento de API:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Para desabilitar a verificação do uso da lista de certificados revogados `context.Request.Certificate.VerifyNoRevocation()` em vez de `context.Request.Certificate.Verify()` .
> Se o certificado do cliente for autoassinado, os certificados de autoridade de certificação raiz (ou intermediário) deverão ser [carregados](api-management-howto-ca-certificates.md) no gerenciamento de API para o `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` para funcionar.

> [!TIP]
> O problema de deadlock de certificado de cliente descrito neste [artigo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) pode se manifestar de várias maneiras, por exemplo, as solicitações Freeze, as solicitações resultam em `403 Forbidden` código de status após o tempo limite `context.Request.Certificate` é `null` . Esse problema geralmente afeta `POST` e `PUT` solicita o tamanho do conteúdo de aproximadamente 60KB ou maior.
> Para evitar que esse problema ocorra, ative a configuração "negociar certificado de cliente" para os nomes de host desejados na folha "domínios personalizados", conforme mostrado na primeira imagem deste documento. Este recurso não está disponível na camada de consumo.

## <a name="certificate-validation-in-self-hosted-gateway"></a>Validação de certificado no gateway de hospedagem interna

A imagem de [Gateway autohospedado](self-hosted-gateway-overview.md) do gerenciamento de API padrão não dá suporte à validação de certificados de cliente e servidor usando [certificados raiz de CA](api-management-howto-ca-certificates.md) carregados para uma instância de gerenciamento de API. Os clientes que apresentam um certificado personalizado para o gateway auto-hospedado podem experimentar respostas lentas, pois a validação da CRL (lista de certificados revogados) pode levar muito tempo para expirar no gateway. 

Como solução alternativa ao executar o gateway, você pode configurar o endereço IP PKI para apontar para o endereço localhost (127.0.0.1) em vez da instância de gerenciamento de API. Isso faz com que a validação da CRL falhe rapidamente quando o gateway tenta validar o certificado do cliente. Para configurar o gateway, adicione uma entrada DNS para a instância de gerenciamento de API a ser resolvida para o localhost no `/etc/hosts` arquivo no contêiner. Você pode adicionar essa entrada durante a implantação do gateway:
 
* Para a implantação do Docker – adicione o `--add-host <hostname>:127.0.0.1` parâmetro ao `docker run` comando. Para obter mais informações, consulte [Adicionar entradas ao contêiner hosts de arquivo](https://docs.docker.com/engine/reference/commandline/run/#add-entries-to-container-hosts-file---add-host)
 
* Para a implantação do kubernetes – adicione uma `hostAliases` especificação ao `myGateway.yaml` arquivo de configuração. Para obter mais informações, consulte [adicionando entradas para o Pod/etc/hosts com aliases de host](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/).




## <a name="next-steps"></a>Próximas etapas

-   [Como garantir serviços de back-end usando autenticação de certificado do cliente](./api-management-howto-mutual-certificates.md)
-   [Como carregar certificados](./api-management-howto-mutual-certificates.md)
