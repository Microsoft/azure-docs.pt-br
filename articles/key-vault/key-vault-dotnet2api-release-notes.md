---
title: Notas de versão da API do .NET 2.x para o Cofre de Chaves | Microsoft Docs
description: Os desenvolvedores em .NET usarão essa API para codificar para o Cofre de Chaves do Azure
services: key-vault
author: msmbaldwin
manager: barbkess
editor: bryanla
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: e173ff54020f2d365348ae037793cfbba3f9ed7f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68260438"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>.NET 2.0 para Cofre de Chaves do Azure - Notas de versão e guia de migração
As informações a seguir ajudam a migrar para a versão 2.0 da biblioteca do Azure Key Vault para C# e .NET.  Aplicativos escritos para versões anteriores precisam ser atualizados para dar suporte à versão mais recente.  Essas alterações são necessárias para dar suporte total a recursos novos e aprimorados, como **certificados do Key Vault**.

## <a name="key-vault-certificates"></a>Certificados do Key Vault

Certificados do Key Vault x509 certifica e suporta os seguintes comportamentos:  

* Criar certificados por meio de um processo de criação de Key Vault ou importar certificado existente. Isso inclui certificados autoassinados e gerados por Autoridades de Certificação (CA).
* Armazenar com segurança e gerenciar armazenamento de certificados x509 sem interação usando material da chave privada.  
* Definir políticas que direcionam o Key Vault para gerenciar o ciclo de vida do certificado.  
* Fornecer informações de contato para eventos de ciclo de vida, como avisos de expiração e notificações de renovação.  
* Renovar automaticamente certificados de emissores selecionados (provedores de certificados X509 do parceiro de Key Vault e autoridades de certificação). * Suportar certificado de provedores alternativos (não parceiros) e autoridades de certificação (não suporta renovação automática).  

## <a name="net-support"></a>Suporte ao .NET

* **.NET 4.0** não tem suporte na versão 2.0 da biblioteca .NET do Azure Key Vault
* **.NET Framework 4.5.2** tem suporte na versão 2.0 da biblioteca .NET do Azure Key Vault
* **.NET Standard 1.4** tem suporte na versão 2.0 da biblioteca .NET do Azure Key Vault

## <a name="namespaces"></a>Namespaces

* O namespace para **modelos** é alterado de **Microsoft.Azure.KeyVault** para **Microsoft.Azure.KeyVault.Models**.
* O namespace **Microsoft.Azure.KeyVault.Internal** é descartado.
* Os seguintes namespaces de dependências do SDK do Azure têm 

    - **Hyak.Common** agora é **Microsoft.Rest**.
    - **Hyak.Common.Internals** agora é **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Alterações de tipo

* *Secret* mudou para *SecretBundle*
* *Dictionary* mudou para *IDictionary*
* *List\<t >, String []* alterado para *IList\<T >*
* *NextList* mudou para *NextPageLink*

## <a name="return-types"></a>Tipos de retorno

* **KeyList** e **secretolist** agora retorna *iPage\<T >* em vez de *ListKeysResponseMessage*
* O **BackupKeyAsync** gerado agora retorna *BackupKeyResult*, que contém *Value* (blob de backup). Anteriormente, o método era encapsulado e retornava somente o valor.

## <a name="exceptions"></a>Exceções

* *KeyVaultClientException* mudou para *KeyVaultErrorException*
* O erro de serviço mudou de *exception.Error* para *exception.Body.Error.Message*.
* Informações adicionais removidas da mensagem de erro para **[JsonExtensionData]** .

## <a name="constructors"></a>Construtores

* Em vez de aceitar um *HttpClient* como um argumento do construtor, o construtor aceita apenas *HttpClientHandler* ou *DelegatingHandler[]* .

## <a name="downloaded-packages"></a>Pacotes baixados

Quando um cliente processa uma dependência do Key Vault, os seguintes pacotes são baixados:

### <a name="previous-package-list"></a>Lista de pacotes anterior

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Lista atual de pacotes

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Alterações de classe

* A classe **UnixEpoch** foi removida.
* A classe **Base64UrlConverter** foi renomeada para **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Outras alterações

* Foi adicionado suporte para a configuração da política de repetição de operação KV em falhas transitórias para esta versão da API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Para as operações que retornavam um *cofre*, o tipo de retorno era uma classe que continha uma propriedade **Vault**. Agora, o tipo de retorno é *Vault*.
* *PermissionsToKeys* e *PermissionsToSecrets* agora são *Permissions.Keys* e *Permissions.Secrets*
* Algumas das alterações de tipos de retorno também se aplicam ao plano de controle.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* O pacote é dividido em **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** para as operações de criptografia.

