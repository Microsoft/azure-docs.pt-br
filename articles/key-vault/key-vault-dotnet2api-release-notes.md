---
title: "Notas de versão da API do .NET 2.x para o Cofre de Chaves | Microsoft Docs"
description: "Os desenvolvedores em .NET usarão essa API para codificar para o Cofre de Chaves do Azure"
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bruceper
ms.openlocfilehash: c5b5fd7f16faf17d16ecc82269fb1264adf4dd06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>.NET 2.0 para Cofre de Chaves do Azure - Notas de versão e guia de migração
As anotações e as diretrizes a seguir são para desenvolvedores que trabalham com a biblioteca .NET/C# do Azure Key Vault. Na transição da versão 1.0 para a versão 2.0, diversas atualizações feitas exigirão o trabalho de migração em seu código para que você se beneficie de aprimoramentos funcionais e adições de recurso, como suporte a **certificados do Key Vault**.

## <a name="key-vault-certificates"></a>Certificados do Key Vault

Os certificados do Cofre de Chaves oferecem suporte ao gerenciamento de seus certificados x509 e dos comportamentos a seguir:  

* Permite que um proprietário de certificado crie um certificado por meio de um processo de criação de Cofre de Chaves ou pela importação de um certificado existente. Isso inclui certificados autoassinados e gerados por Autoridades de Certificação.
* Permite que um proprietário de certificado do Cofre de Chaves implemente o armazenamento seguro e o gerenciamento de certificados X509 sem interagir com o material da chave privada.  
* Permite que um proprietário de certificado crie uma política que direciona o Cofre de Chaves para gerenciar o ciclo de vida de um certificado.  
* Permite que os proprietários de certificado forneçam informações de contato para a notificação sobre eventos de ciclo de vida de validade e renovação de certificados.  
* Oferece suporte à renovação automática com emissores selecionados - provedores de certificado X509/autoridades de certificação parceiros do Cofre de Chaves.
  
  * OBSERVAÇÃO - Provedores/autoridades que não têm parceria também têm permissão, mas, não oferecerão suporte ao recurso de renovação automática.

## <a name="net-support"></a>Suporte ao .NET

* **.NET 4.0** não tem suporte na versão 2.0 da biblioteca .NET/C# do Cofre de Chaves do Azure
* **.NET Core** tem suporte na versão 2.0 da biblioteca .NET/C# do Cofre de Chaves do Azure

## <a name="namespaces"></a>Namespaces

* O namespace para **modelos** é alterado de **Microsoft.Azure.KeyVault** para **Microsoft.Azure.KeyVault.Models**.
* O namespace **Microsoft.Azure.KeyVault.Internal** é descartado.
* O namespace de dependências do SDK do Azure é alterado de **Hyak.Common** e **Hyak.Common.Internals** para **Microsoft.Rest** e **Microsoft.Rest.Serialization**

## <a name="type-changes"></a>Alterações de tipo

* *Secret* mudou para *SecretBundle*
* *Dictionary* mudou para *IDictionary*
* *List<T>, cadeia de caracteres []* mudou para *IList<T>*
* *NextList* mudou para *NextPageLink*

## <a name="return-types"></a>Tipos de retorno

* **KeyList** e **SecretList** retornarão *IPage<T>* em vez de *ListKeysResponseMessage*
* O **BackupKeyAsync** gerado retornará *BackupKeyResult*, que contém *Value* (blob de backup). Antes do método ser encapsulado e retornar somente o valor.

## <a name="exceptions"></a>Exceções

* *KeyVaultClientException* mudou para *KeyVaultErrorException*
* O erro de serviço mudou de *exception.Error* para *exception.Body.Error.Message*.
* Informações adicionais removidas da mensagem de erro para **[JsonExtensionData]**.

## <a name="constructors"></a>Construtores

* Em vez de aceitar um *HttpClient* como um argumento do construtor, o construtor aceita apenas *HttpClientHandler* ou *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Pacotes baixados

Quando um cliente está processando uma dependência no Cofre de Chaves, os itens a seguir são baixados

### <a name="previous-package-list"></a>Lista de pacotes anterior

* id do pacote="Hyak.Common" versão="1.0.2" targetFramework="net45"
* id do pacote="Microsoft.Azure.Common" versão="2.0.4" targetFramework="net45"
* id do pacote="Microsoft.Azure.Common.Dependencies" versão="1.0.0" targetFramework="net45"
* id do pacote="Microsoft.Azure.KeyVault" versão="1.0.0" targetFramework="net45"
* id do pacote="Microsoft.Bcl" versão="1.1.9" targetFramework="net45"
* id do pacote="Microsoft.Bcl.Async" versão="1.0.168" targetFramework="net45"
* id do pacote="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"
* id do pacote="Microsoft.Net.Http" versão="2.2.22" targetFramework="net45"

### <a name="current-package-list"></a>Lista atual de pacotes

* id do pacote="Microsoft.Azure.KeyVault" versão="2.0.0-preview" targetFramework="net45"
* id do pacote="Microsoft.Rest.ClientRuntime" versão="2.2.0" targetFramework="net45"
* id do pacote="Microsoft.Rest.ClientRuntime.Azure" versão="3.2.0" targetFramework="net45"

## <a name="class-changes"></a>Alterações de classe

* A classe **UnixEpoch** foi removida
* A classe **Base64UrlConverter** foi renomeada para **Base64UrlJsonConverter**

## <a name="other-changes"></a>Outras alterações

* Foi adicionado suporte para a configuração da política de repetição de operação KV em falhas transitórias para esta versão da API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Para as operações que retornavam um *cofre*, o tipo de retorno era uma classe que continha uma propriedade Vault. Agora, o tipo de retorno é *Vault*.
* *PermissionsToKeys* e *PermissionsToSecrets* agora são *Permissions.Keys* e *Permissions.Secrets*
* Algumas das alterações de tipos de retorno também se aplicam ao plano de controle.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* O pacote é dividido em **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** para as operações de criptografia.

