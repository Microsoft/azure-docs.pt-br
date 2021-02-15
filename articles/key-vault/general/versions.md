---
title: Versões do Azure Key Vault
description: As diversas versões do Azure Key Vault
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 2396aac67a17352987d9d6e3b49535c19ffe8917
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792371"
---
# <a name="key-vault-versions"></a>Versões do Key Vault

Veja a seguir as novidades do Azure Key Vault. Os novos recursos e os aprimoramentos também foram anunciados no [canal do Key Vault de atualizações do Azure](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

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

- [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md)
- [Chaves](../keys/index.yml)
- [Segredos](../secrets/index.yml)
- [Certificados](../certificates/index.yml)