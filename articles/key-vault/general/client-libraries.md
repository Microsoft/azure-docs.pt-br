---
title: Bibliotecas de clientes para o Azure Key Vault | Microsoft Docs
description: Bibliotecas de clientes para o Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: e95c6d0b18341dd46189f6b16c0e7da9703305f8
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400191"
---
# <a name="client-libraries-for-azure-key-vault"></a>Bibliotecas de clientes para o Azure Key Vault

As bibliotecas de clientes para o Azure Key Vault permitem acesso programático à funcionalidade do Key Vault em uma variedade de linguagens, incluindo .NET, Python, Java e JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Bibliotecas de clientes por linguagem e objeto

Cada SDK tem bibliotecas de clientes separadas para cofres de chaves, segredos, chaves e certificados, de acordo com a tabela abaixo.

| Idioma | Segredos | simétricas | Certificados | Key Vault (plano de gerenciamento) |
|--|--|--|--|--|
| .NET | - [Referência de API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Guia de Início Rápido](../secrets/quick-create-net.md) | - [Referência de API](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [Referência de API](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) | - [Referência de API](/dotnet/api/microsoft.azure.management.keyvault?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [Referência de API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Pacote PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Guia de Início Rápido](../secrets/quick-create-python.md) |- [Referência de API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Pacote PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Guia de Início Rápido](../keys/quick-create-python.md) | - [Referência de API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Pacote PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Guia de Início Rápido](../certificates/quick-create-python.md) | - [Referência de API](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault?view=azure-python)<br> - [Pacote PyPi](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [Referência de API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Guia de Início Rápido](../secrets/quick-create-java.md) |- [Referência de API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [Referência de API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |- [Referência de API](/java/api/com.microsoft.azure.management.keyvault?view=azure-java-stable)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [Referência de API](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [Pacote npm](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Guia de Início Rápido](../secrets/quick-create-node.md) |- [Referência de API](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [Pacote npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [Referência de API](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [Pacote npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |  - [Referência de API](/javascript/api/@azure/arm-keyvault/?view=azure-node-latest)<br>- [Pacote npm](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Próximas etapas

- Confira o [Guia do desenvolvedor do Azure Key Vault](developers-guide.md)
- Leia mais sobre como [Autenticar-se no Key Vault](authentication.md)
- Leia mais sobre [Como proteger o acesso a um Key Vault](secure-your-key-vault.md)
