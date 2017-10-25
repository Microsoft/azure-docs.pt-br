---
title: Criar registros DNS personalizados para um aplicativo Web | Microsoft Docs
description: "Como criar registros DNS de domínio personalizado para o aplicativo Web usando o DNS do Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: d4b0aa817c3fd7f3304b5122ac584166d8079d3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Criar registros DNS para um aplicativo Web em um domínio personalizado

Você pode usar o DNS do Azure para hospedar um domínio personalizado para seus aplicativos Web. Por exemplo, você está criando um aplicativo Web do Azure e quer que os usuários o acessem usando contoso.com ou www.contoso.com como FQDN.

Para isso, você precisa criar dois registros:

* Um registro "A" raiz que aponta para contoso.com
* Um registro "CNAME" para o nome www que aponta para o registro A

Tenha em mente que, se você criar um registro A para um aplicativo Web no Azure, o registro A deve ser manualmente atualizado se o endereço IP subjacente para o aplicativo Web for alterado.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, primeiro você deve criar uma zona DNS no DNS do Azure e delegar a zona no registrador para o DNS do Azure.

1. Para criar uma zona DNS, siga as etapas em [Criar uma zona DNS](dns-getstarted-create-dnszone.md).
2. Para delegar o DNS ao DNS do Azure, siga as etapas em [Delegação de domínio DNS](dns-domain-delegation.md).

Após criar uma zona e delegá-la ao DNS do Azure, você pode criar registros para seu domínio personalizado.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Criar um registro A para seu domínio personalizado

Um registro A é usado para mapear um nome para seu endereço IP. No exemplo a seguir, atribuiremos @ como um registro A para um endereço IPv4:

### <a name="step-1"></a>Etapa 1

Crie um registro A e atribuir a uma variável $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Etapa 2

Adicione o valor IPv4 ao conjunto de registros criado anteriormente "@" usando a variável $rs atribuída. O valor IPv4 atribuído será o endereço IP do seu aplicativo Web.

Para localizar o endereço IP de um aplicativo Web, siga as etapas em [Configurar um nome de domínio personalizado no serviço de aplicativo do Azure](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Etapa 3

Confirme as alterações no conjunto de registros. Use `Set-AzureRMDnsRecordSet` para carregar as alterações no conjunto de registros para o DNS do Azure:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Criar um registro CNAME para seu domínio personalizado

Se o domínio já é gerenciado pelo DNS do Azure (confira [Delegação de domínio ao DNS](dns-domain-delegation.md)), você pode usar o exemplo a seguir para criar um registro CNAME para contoso.azurewebsites.net.

### <a name="step-1"></a>Etapa 1

Abra o PowerShell, crie um novo conjunto de registros CNAME e atribua-o a uma variável $rs. Este exemplo cria um tipo de conjunto de registros CNAME com uma "vida útil" de 600 segundos na zona DNS denominada "contoso.com".

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

O exemplo a seguir é a resposta.

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Etapa 2

Depois de criar o conjunto de registros CNAME, você precisa criar um valor de alias que apontará para o aplicativo Web.

Usando a variável "$rs" atribuída anteriormente, você pode usar o comando PowerShell a seguir para criar o alias para o aplicativo Web contoso.azurewebsites.net.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

O exemplo a seguir é a resposta.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Etapa 3

Confirme as alterações usando o cmdlet `Set-AzureRMDnsRecordSet` :

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

Você pode validar se o registro foi criado corretamente consultando "www.contoso.com" usando o nslookup, conforme mostrado abaixo:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>Criar um registro "awverify" para aplicativos Web

Se você decidir usar um registro A do seu aplicativo Web, deverá passar por um processo de verificação para garantir que você tem o domínio personalizado. Essa etapa de verificação é feita criando um registro CNAME especial chamado "awverify". Esta seção aplica-se somente a registros A.

### <a name="step-1"></a>Etapa 1

Crie o registro "awverify". No exemplo a seguir, criaremos o registro "aweverify" para contoso.com verificar a propriedade do domínio personalizado.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

O exemplo a seguir é a resposta.

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Etapa 2

Após criar o conjunto de registros "awverify", atribua o alias do conjunto de registros CNAME. No exemplo a seguir, atribuiremos o alias do conjunto de registros CNAMe a awverify.contoso.azurewebsites.net.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

O exemplo a seguir é a resposta.

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Etapa 3

Confirme as alterações usando o `Set-AzureRMDnsRecordSet cmdlet`, conforme mostrado no comando abaixo.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Próximas etapas

Siga as etapas em [Configurar um nome de domínio personalizado para o serviço de aplicativo](../app-service/app-service-web-tutorial-custom-domain.md) para configurar o aplicativo Web para usar um domínio personalizado.
