---
title: Novidades do Azure Key Vault
description: Atualizações recentes do Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 01/12/2020
ms.author: mbaldwin
ms.openlocfilehash: ef2c42ae76dc5e1511494635904228a8b574d483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132181"
---
# <a name="whats-new-for-azure-key-vault"></a>Novidades do Azure Key Vault

Veja a seguir as novidades do Azure Key Vault. Os novos recursos e os aprimoramentos também foram anunciados no [canal do Key Vault de atualizações do Azure](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="october-2020"></a>Outubro de 2020

> [!WARNING]
> Essas atualizações têm o potencial de afetar as implementações do Azure Key Vault.

Para dar suporte à [exclusão temporária agora ativada por padrão](#soft-delete-on-by-default), foram feitas duas alterações nos cmdlets do PowerShell do Azure Key Vault:

- Os parâmetros DisableSoftDelete e EnableSoftDelete de [Update-AzKeyVault](/powershell/module/az.keyvault/update-azkeyvault) foram preteridos.
- A saída do cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) não tem mais o atributo `SecretValueText`.

## <a name="july-2020"></a>Julho de 2020

> [!WARNING]
> Essas duas atualizações têm o potencial de afetar as implementações do Azure Key Vault.

### <a name="soft-delete-on-by-default"></a>Exclusão reversível ativada por padrão

**A exclusão reversível deve estar habilitada para todos os cofres de chaves**, tanto novos quanto preexistentes. Nos próximos meses, a capacidade de recusar a exclusão reversível será preterida. Para obter todos os detalhes sobre essa possível alteração da falha, bem como as etapas necessárias para encontrar os cofres de chaves afetados e atualizá-los com antecedência, confira o artigo [A exclusão reversível será habilitada em todos os cofres de chaves](soft-delete-change.md).

### <a name="azure-tls-certificate-changes"></a>Alterações no certificado TLS do Azure

A Microsoft está atualizando os serviços do Azure para que eles usem certificados TLS de outro conjunto de ACs (autoridades de certificação) raiz. Essa alteração está sendo feita porque os Certificados de Autoridade de Certificação atuais não estão em conformidade com um dos requisitos de linha de base do fórum do navegador/da AC.  Para ver todos os detalhes, confira [Alterações no certificado TLS do Azure](../../security/fundamentals/tls-certificate-changes.md).

## <a name="june-2020"></a>Junho de 2020

O Azure Monitor para Key Vault já está em versão prévia.  O Azure Monitor fornece monitoramento abrangente de cofres de chaves, fornecendo uma visão unificada das solicitações, do desempenho, das falhas e da latência do Key Vault. Para obter mais informações, confira [Azure Monitor para Key Vault (versão prévia)](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Maio de 2020

O BYOK "bring your own key" do Key Vault já está em disponibilidade geral. Confira a [especificação do BYOK do Azure Key Vault](../keys/byok-specification.md) e saiba como [Importar chaves protegidas por HSM para o Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Março de 2020

Os pontos de extremidade privados já estão disponíveis em versão prévia. O Serviço de Link Privado do Azure permite que você acesse o Azure Key Vault e os serviços de parceiros/clientes hospedados no Azure em um ponto de extremidade privado na sua rede virtual.  Saiba como [Integrar o Key Vault ao Link Privado do Azure](private-link-service.md).

## <a name="2019"></a>2019

- Versão dos SDKs do Azure Key Vault de próxima geração. Para obter exemplos de uso, confira os guias de início rápido de segredos do Azure Key Vault para [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md) e [Node.js](../secrets/quick-create-node.md)
- Novas políticas do Azure para gerenciar certificados do cofre de chaves. Confira as [Definições internas do Azure Policy para o Key Vault](../policy-reference.md).
- A extensão de Máquina Virtual do Azure Key Vault já está em disponibilidade geral.  Confira [Extensão de máquina virtual do Key Vault para Linux](../../virtual-machines/extensions/key-vault-linux.md) e [Extensão de máquina virtual do Key Vault para Windows](../../virtual-machines/extensions/key-vault-windows.md).
- O gerenciamento de segredos controlado por evento para o Azure Key Vault já está disponível na Grade de Eventos do Azure. Para obter mais informações, confira [o esquema da Grade de Eventos para os eventos no Azure Key Vault](../../event-grid/event-schema-key-vault.md] e saiba como [Receber notificações do cofre de chaves e responder a elas com a Grade de Eventos do Azure](event-grid-tutorial.md).

## <a name="2018"></a>2018

Novas integrações e novos recursos lançados neste ano:

- Integração ao Azure Functions. Para obter um exemplo de cenário que utiliza o [Azure Functions](../../azure-functions/index.yml) para operações do cofre de chaves, confira [Automatizar a rotação de um segredo](../secrets/tutorial-rotation.md).
- [Integração ao Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Com isso, o Azure Databricks agora dá suporte a dois tipos de escopos de segredos: Com backup do Azure Key Vault e com backup do Databricks. Para obter mais informações, confira [Criar um escopo de segredo com backup do Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Pontos de extremidade de serviço de rede virtual para o Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Novos recursos lançados neste ano:

- Chaves de conta de armazenamento gerenciadas. O recurso Chaves de Conta de Armazenamento facilitou a integração ao Armazenamento do Azure. Consulte o tópico de visão geral para saber mais, [Visão geral das Chaves de Conta de Armazenamento Gerenciado](../secrets/overview-storage-keys.md).
- Exclusão reversível. O recurso de exclusão reversível aprimora a proteção de dados de cofres de chave e objetos do cofre de chaves. Para saber mais, confira o tópico de visão geral, [Visão geral da exclusão reversível](./soft-delete-overview.md).

## <a name="2015"></a>2015

Novos recursos lançados neste ano:
- Gerenciamento de certificados. Adicionado como um recurso à versão GA 2015-06-01 em 26 de setembro de 2016.

A disponibilidade geral (versão 2015-06-01) foi anunciada em 24 de junho de 2015. As seguintes alterações foram feitas nessa versão:
- Exclusão de uma chave – remoção do campo "usar".
- Obtenção de informações sobre uma chave – remoção do campo "usar".
- Importação de uma chave para um cofre – remoção do campo "usar".
- Restauração de uma chave – remoção do campo "usar".
- Alteração de "RSA_OAEP" para "RSA-OAEP" em Algoritmos RSA. Confira [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).

A segunda versão prévia (versão 2015-02-01-preview) foi anunciada em 20 de abril de 2015. Para saber mais, consulte a postagem no blog [Atualização da API REST](/archive/blogs/kv/rest-api-update). As seguintes tarefas foram atualizadas:

- Listar as chaves em um cofre - adição de suporte a paginação para a operação.
- Listar as versões de uma chave - adição de operação para listar as versões de uma chave.
- Listar os segredos em um cofre - adição de suporte a paginação.
- Listar as versões de um segredo - adição de operação para listar as versões de um segredo.
- Todas as operações - adição de carimbos de data/hora de criação/atualização dos atributos.
- Criar um segredo - adição de Content-Type aos segredos.
- Criar uma chave - adição de marcas como informações opcionais.
- Criar um segredo - adição de marcas como informações opcionais.
- Atualizar uma chave - adição de marcas como informações opcionais.
- Atualizar um segredo - adição de marcas como informações opcionais.
- Alteração do tamanho máximo para segredos de 10 K para 25 K Bytes. Confira [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).

## <a name="2014"></a>2014

A primeira versão prévia (versão 2014-12-08-preview) foi lançada em 8 de janeiro de 2015.

## <a name="next-steps"></a>Próximas etapas

Caso tenha outras dúvidas, entre em contato conosco por meio do [suporte](https://azure.microsoft.com/support/options/).
