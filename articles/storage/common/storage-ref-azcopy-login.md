---
title: logon do azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando de logon azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4740870dd2d9748aad55150ce1946e3eb666619
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878351"
---
# <a name="azcopy-login"></a>azcopy login

Faz logon no Azure Active Directory para acessar os recursos de armazenamento do Azure.

## <a name="synopsis"></a>Sinopse

Faça logon no Azure Active Directory para acessar os recursos de armazenamento do Azure.

Para ser autorizado à sua conta de armazenamento do Azure, você deve atribuir a função de **colaborador de dados do blob de armazenamento** à sua conta de usuário no contexto da conta de armazenamento, do grupo de recursos pai ou da assinatura pai.

Esse comando armazenará em cache as informações de logon criptografadas para o usuário atual usando os mecanismos internos do sistema operacional.

> [!IMPORTANT]
> Se você definir uma variável de ambiente usando a linha de comando, essa variável será legível no seu histórico de linha de comando. Considere limpar as variáveis que contêm credenciais do seu histórico de linha de comando. Para impedir que as variáveis apareçam no seu histórico, você pode usar um script para solicitar ao usuário suas credenciais e definir a variável de ambiente.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](./storage-use-azcopy-v10.md#transfer-data)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

Faça logon interativamente com a ID de locatário do AAD padrão definida como comum:

```azcopy
azcopy login
```

Fazer logon interativamente com uma ID de locatário especificada:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Faça logon usando a identidade atribuída pelo sistema de uma VM (máquina virtual):

```azcopy
azcopy login --identity
```

Faça logon usando a identidade atribuída pelo usuário de uma VM e uma ID de cliente da identidade do serviço:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Faça logon usando a identidade atribuída pelo usuário de uma VM e uma ID de objeto da identidade do serviço:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Faça logon usando a identidade atribuída pelo usuário de uma VM e uma ID de recurso da identidade do serviço:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Faça logon como uma entidade de serviço usando um segredo do cliente: defina a variável de ambiente AZCOPY_SPA_CLIENT_SECRET como o segredo do cliente para autenticação de entidade de serviço baseada em segredo.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Faça logon como uma entidade de serviço usando um certificado e sua senha:

Defina a variável de ambiente AZCOPY_SPA_CERT_PASSWORD como a senha do certificado para autenticação de entidade de serviço baseada em certificado:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Tratar `/path/to/my/cert` como um caminho para um arquivo PEM ou PKCS12. AzCopy não chega ao repositório de certificados do sistema para obter seu certificado.

`--certificate-path` é obrigatório ao fazer autenticação de entidade de serviço baseada em certificado.

## <a name="options"></a>Opções

**--AAD-** cadeia de ponto de extremidade o Azure Active Directory ponto de extremidade a ser usado. O padrão ( https://login.microsoftonline.com) está correto para a nuvem global do Azure. Defina esse parâmetro ao autenticar em uma nuvem nacional. Não é necessário para Identidade de Serviço Gerenciada.

**--** ID do aplicativo cadeia de caracteres ID do aplicativo da identidade atribuída pelo usuário. Necessário para autenticação de entidade de serviço.

**--** caminho da cadeia de caracteres do caminho do certificado para o certificado para autenticação SPN. Necessário para autenticação de entidade de serviço baseada em certificado.

**--**   ajuda ajuda para o `azcopy login` comando.

**--identidade**   Faça logon usando a identidade da máquina virtual, também conhecida como MSI (identidade de serviço gerenciada).

**--ID-Client-ID** da cadeia de caracteres do cliente de identidade atribuída pelo usuário.

**--Identity-** ID do objeto da cadeia de caracteres ID da identidade atribuída pelo usuário.

**--Identity-Resource-ID** ID do recurso da identidade atribuída pelo usuário.

**--entidade de serviço**   Faça logon por meio do SPN (nome da entidade de serviço) usando um certificado ou um segredo. O segredo do cliente ou a senha do certificado devem ser colocados na variável de ambiente apropriada. Digite AzCopy env para ver os nomes e descrições das variáveis de ambiente.

**--Tenant-ID** cadeia de caracteres Azure Active Directory ID do locatário a ser usada para o logon interativo do dispositivo OAuth.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps float|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|
|--Cadeia de caracteres de sufixos confiáveis da Microsoft   |Especifica sufixos de domínio adicionais onde Azure Active Directory tokens de logon podem ser enviados.  O padrão é '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Todos listados aqui são adicionados ao padrão. Por segurança, você só deve colocar Microsoft Azure domínios aqui. Separe várias entradas com ponto e vírgula.|

## <a name="see-also"></a>Confira também

- [azcopy](storage-ref-azcopy.md)
