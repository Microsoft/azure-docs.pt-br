---
title: Visão geral das chaves, dos segredos e dos certificados do Azure Key Vault
description: Visão geral dos detalhes de interface e desenvolvedor de REST do Azure Key Vault para chaves, segredos e certificados.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: e3eb77d15c288c93298da6dd79a76565e5d67f96
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749934"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Visão geral das chaves, dos segredos e dos certificados do Azure Key Vault

O Azure Key Vault permite que aplicativos do Microsoft Azure e usuários armazenem e usem vários tipos de dados de chave/segredo. O provedor de recursos do Key Vault dá suporte a dois tipos de recursos: cofres e HSMs gerenciados.

## <a name="dns-suffixes-for-base-url"></a>Sufixos DNS para URL base
 A tabela a seguir mostra o sufixo DNS da URL base usado pelo ponto de extremidade do plano de dados para cofres e pools do HSM gerenciado em vários ambientes de nuvem.

Ambiente de nuvem | Sufixo DNS para cofres | Sufixo DNS para HSMs gerenciados
---|---|---
Nuvem do Azure | .vault.azure.net | .managedhsm.azure.net
Nuvem do Azure China | .vault.azure.cn | Sem suporte
Azure US Government | .vault.usgovcloudapi.net | Sem suporte
Nuvem Alemã do Azure | .vault.microsoftazure.de | Sem suporte
|||


## <a name="object-types"></a>Tipos de objeto
 A tabela a seguir mostra os tipos de objeto e os sufixos na URL base.

Tipo de objeto|Sufixo de URL|Cofres|Pools HSM gerenciados
--|--|--|--
**Chaves criptográficas**||
Chaves protegidas por HSM|/keys|Com suporte|Com suporte
Chaves protegidas por software|/keys|Com suporte|Sem suporte
**Outros tipos de objetos**||
Segredos|/secrets|Com suporte|Sem suporte
Certificados|/certificates|Com suporte|Sem suporte
Chaves de conta de armazenamento|/storage|Com suporte|Sem suporte
|||
- **Chaves de criptografia**: Dá suporte a vários tipos de chave e algoritmos e habilita o uso de chaves protegidas por HSM e por software. Para obter mais informações, confira [Sobre chaves](../keys/about-keys.md).
- **Segredos**: Fornece armazenamento seguro de segredos, como senhas e cadeias de conexão de banco de dados. Para obter mais informações, confira [Sobre segredos](../secrets/about-secrets.md).
- **Certificados**: Oferece suporte a certificados, que são criados sobre chaves e segredos e adicionam um recurso de renovação automática. Para obter mais informações, confira [Sobre certificados](../certificates/about-certificates.md).
- **Chaves de conta de Armazenamento do Azure**: Pode gerenciar chaves de uma conta de Armazenamento do Microsoft Azure para você. Internamente, o Key Vault pode listar (sincronizar) chaves com uma conta de Armazenamento do Azure e gerar novamente (gira) as chaves periodicamente. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento com o Key Vault](../secrets/overview-storage-keys.md).

Para obter mais informações gerais sobre o Key Vault, confira [Sobre o Azure Key Vault](overview.md). Para obter mais informações sobre os pools do HSM Gerenciado, confira [O que é o HSM Gerenciado do Azure Key Vault?](../managed-hsm/overview.md)


## <a name="data-types"></a>Tipos de dados

Consulte as especificações JOSE para tipos de dados relevantes para as chaves, criptografia e assinatura.  

-   **algoritmo** - um algoritmo com suporte para uma operação de chave, por exemplo, RSA1_5  
-   **valor de texto cifrado** - octetos de texto cifrado, codificado usando Base64URL  
-   **valor de Digest** - a saída de um algoritmo de hash, codificado usando Base64URL  
-   **tipo de chave** - um dos tipos de chave com suporte, por exemplo, RSA (Rivest-Shamir-Adleman).  
-   **valor de texto sem formatação** - octetos de texto sem formatação, codificado usando Base64URL  
-   **valor de assinatura** - a saída de um algoritmo de assinatura, codificado usando Base64URL  
-   **base64URL** - um Base64URL [RFC4648] codificado valor binário  
-   **boolean** - seja true ou false  
-   **Identity** - uma identidade do Azure Active Directory (AAD).  
-   **IntDate** - um valor decimal de JSON que representa o número de segundos desde 1970-01-01T0:0:0Z UTC até a data/hora de UTC especificada. Consulte RFC3339 para obter detalhes sobre data/hora em geral e o UTC em particular.  

## <a name="objects-identifiers-and-versioning"></a>Objetos, identificadores e controle de versão

Objetos armazenados no Key Vault são submetidos ao controle de versão sempre que uma nova instância de um objeto é criada. Cada versão é atribuída a um identificador exclusivo e a uma URL. Quando um objeto é criado pela primeira vez, ele recebe um identificador de versão exclusivo e é marcado como a versão atual do objeto. A criação de uma nova instância com o mesmo nome de objeto fornece ao novo objeto um identificador de versão exclusivo, fazendo com que ele se torne a versão atual.  

Os objetos no Key Vault podem ser abordados especificando uma versão ou omitindo a versão para operações na versão atual do objeto. Por exemplo, dada uma Chave com o nome `MasterKey`, executar operações sem especificar uma versão faz o sistema usar a versão mais recente disponível. Executar operações com o identificador de versão específico faz com que o sistema use essa versão específica do objeto.  

### <a name="vault-name-and-object-name"></a>Nome do cofre e nome do objeto
Os objetos são identificados de forma exclusiva no Key Vault usando uma URL. Não há dois objetos no sistema com a mesma URL, independentemente da localização geográfica. A URL completa para um objeto é chamada de Identificador do Objeto. A URL consiste em um prefixo que identifica o Key Vault, o tipo de objeto, o Nome do Objeto fornecido pelo usuário e uma Versão do Objeto. O nome do objeto é imutável e diferencia maiusculas de minúsculas. Identificadores que não incluem a versão do objeto são chamados de Identificadores de Base.  

Para mais informações, consulte [Autenticação, solicitações e respostas](authentication-requests-and-responses.md)

Um identificador de objeto tem o seguinte formato geral (dependendo do tipo de contêiner):  

- **Para Cofres**: `https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **Para pools do HSM Gerenciado**: `https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Confira [Suporte de tipo de objeto](#object-types) para tipos de objetos com suporte em cada tipo de contêiner.

Em que:  

| Elemento | Descrição |  
|-|-|  
|`vault-name` ou `hsm-name`|O nome de um cofre ou de um pool do HSM Gerenciado no serviço do Microsoft Azure Key Vault.<br /><br />Os nomes do cofre e do pool do HSM Gerenciado são selecionados pelo usuário e são globalmente exclusivos.<br /><br />O nome do cofre e do pool do HSM Gerenciado precisa ser uma sequência de 3 a 24 caracteres que contenha somente 0 a 9, a a z, A a Z, e -.|  
|`object-type`|O tipo do objeto, "chaves", "segredos" ou "certificados".|  
|`object-name`|Um `object-name` é um nome fornecido por usuário e deve ser exclusivo em um Cofre de Chaves. O nome deve ser uma sequência de 1 a 127 caracteres, que começam com uma letra e contêm somente 0 – 9, a – z, A – Z e -.|  
|`object-version`|Um `object-version` é um identificador de cadeia de caracteres de 32 caracteres gerado pelo sistema, que é opcionalmente usado para direcionar uma versão exclusiva de um objeto.|  

## <a name="next-steps"></a>Próximas etapas

- [Sobre as chaves](../keys/about-keys.md)
- [Sobre segredos](../secrets/about-secrets.md)
- [Sobre certificados](../certificates/about-certificates.md)
- [Autenticação, solicitações e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)
