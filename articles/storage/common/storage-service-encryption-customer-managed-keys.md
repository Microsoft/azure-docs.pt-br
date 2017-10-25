---
title: "Criptografia do Serviço de Armazenamento do Azure usando chaves gerenciadas pelo cliente no Azure Key Vault | Microsoft Docs"
description: "Use o recurso de Criptografia do Serviço de Armazenamento do Azure para criptografar o Armazenamento de Blobs do Azure no lado do serviço ao armazenar os dados e descriptografá-lo ao recuperar os dados usando chaves gerenciadas pelo cliente."
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.openlocfilehash: 6d1e6752fb631114f5be06cb27a63e40547bf6ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault

O Microsoft Azure está empenhado em ajudá-lo a proteger seus dados para atender aos compromissos de conformidade e segurança de sua organização.  Uma maneira de proteger seus dados em repouso é usar a SSE (Criptografia do Serviço de Armazenamento), que criptografa os dados automaticamente ao gravá-los no armazenamento e os descriptografa ao recuperá-los. A criptografia e descriptografia são automáticas, completamente transparentes e usam a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis.

Use chaves de criptografia gerenciadas pela Microsoft com a SSE ou suas próprias chaves de criptografia. Este artigo discute os últimos tópicos. Para obter mais informações sobre como usar chaves gerenciadas pela Microsoft ou sobre a SSE em geral, consulte [Criptografia do Serviço de Armazenamento para dados em repouso](../storage-service-encryption.md).

Para fornecer a capacidade de usar suas próprias chaves de criptografia, a SSE do Armazenamento de blobs é integrada ao AKV (Azure Key Vault). Você pode criar suas próprias chaves de criptografia e armazená-las no AKV ou usar as APIs do AKV para gerar chaves de criptografia. Não só o AKV permite a você gerenciar e controlar as chaves, mas também permite auditar o uso delas. 

Por que você desejará criar suas próprias chaves? Isso proporciona mais flexibilidade, permitindo que você crie, gire, desabilite e defina os controles de acesso. Também permite que você realize auditoria das chaves de criptografia usadas para proteger seus dados.

## <a name="sse-with-customer-managed-keys-preview"></a>Versão prévia da SSE com chaves gerenciadas pelo cliente

Esse recurso está atualmente na visualização. Para usar esse recurso, você precisa criar uma nova conta de armazenamento. Crie um novo cofre de chaves e uma nova chave ou use um cofre de chaves e uma chave existentes. A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes.

Para participar da versão prévia, contate [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com). Forneceremos um link especial para participar da versão prévia.

Para saber mais, confira as [Perguntas frequentes](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest).

> [!IMPORTANT]
> É necessário se inscrever na versão prévia antes de seguir as etapas deste artigo. Sem o acesso à versão prévia, você não poderá habilitar esse recurso no portal.

## <a name="getting-started"></a>Introdução
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Etapa 1: [Criar uma nova conta de armazenamento](../storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>Etapa 2: Habilitar a criptografia
Habilite a SSE na conta de armazenamento usando o [portal do Azure](https://portal.azure.com). Na folha Configurações da conta de armazenamento, procure a seção Serviço Blob, conforme mostrado na figura abaixo, e clique em Criptografia.

![Captura de tela do Portal mostrando a opção Criptografia](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)
<br/>*Habilitar a SSE no Serviço Blob*

Se você quiser habilitar ou desabilitar programaticamente a Criptografia do Serviço de Armazenamento em uma conta de armazenamento, use a [API REST do Provedor de Recursos de Armazenamento do Azure](https://docs.microsoft.com/en-us/rest/api/storagerp/?redirectedfrom=MSDN), a [Biblioteca de Cliente do Provedor de Recursos de Armazenamento do .NET](https://docs.microsoft.com/en-us/dotnet/api/?redirectedfrom=MSDN), o [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.0.0) ou a [CLI do Azure](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli).

Nesta tela, se você não conseguir ver a caixa de seleção “usar sua própria chave”, isso significa que você não foi aprovado para a versão prévia. Envie um email para [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) e solicite a aprovação.

![Captura de tela do portal mostrando a Versão Prévia da Criptografia](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

Por padrão, a SSE usa as chaves gerenciadas pela Microsoft. Para usar suas próprias chaves, marque a caixa. Em seguida, você pode especificar o URI da chave ou selecionar uma chave e o Key Vault no seletor.

## <a name="step-3-select-your-key"></a>Etapa 3: Selecionar a chave

![Captura de tela do portal mostrando a opção Criptografias usam sua própria chave](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![Captura de tela do portal mostrando a opção Inserir URI da chave da Criptografia](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Se a conta de armazenamento não tiver acesso ao Key Vault, execute o comando a seguir usando o Azure PowerShell para conceder acesso às contas de armazenamento ao cofre de chaves solicitado.

![Captura de tela do portal mostrando o acesso negado ao cofre de chaves](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Você também pode conceder acesso por meio do portal do Azure acessando o Azure Key Vault no portal do Azure e concedendo acesso à conta de armazenamento.

## <a name="step-4-copy-data-to-storage-account"></a>Etapa 4: Copiar dados para a conta de armazenamento
Se desejar transferir dados para sua nova conta de armazenamento, de forma que ela seja criptografada, consulte a [Etapa 3 de Introdução à Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account).

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>Etapa 5: Consultar o status dos dados criptografados
Para consultar o status dos dados criptografados, consulte a [Etapa 4 de Introdução à Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data).

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Perguntas frequentes sobre a Criptografia do Serviço de Armazenamento de dados em repouso
**P: Estou usando o armazenamento Premium. Posso usar a SSE com chaves gerenciadas pelo cliente?**

R: Sim, há suporte para a SSE com chaves gerenciadas pela Microsoft e pelo cliente tanto no armazenamento Standard quanto no Premium. 

**P: Posso criar novas contas de armazenamento com a SSE com chaves gerenciadas pelo cliente habilitada usando o Azure PowerShell e a CLI do Azure?**

R: Sim.

**P: Qual é o custo adicional do Armazenamento do Azure se a SSE com chaves gerenciadas pelo cliente é habilitada?**

R: Há um custo associado para o uso do Azure Key Vault. Para obter mais detalhes, visite [Preços do Key Vault](https://azure.microsoft.com/en-us/pricing/details/key-vault/). Não há nenhum custo adicional para o uso da SSE.

**P: Posso revogar o acesso às chaves de criptografia?**

R: Sim, você pode revogar o acesso a qualquer momento. Há várias maneiras para revogar o acesso às chaves. Consulte [PowerShell do Azure Key Vault](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) e [CLI do Azure Key Vault](https://docs.microsoft.com/en-us/cli/azure/keyvault) para obter mais detalhes. Revogar o acesso efetivamente bloqueará o acesso a todos os blobs na conta de armazenamento, pois a Chave de Criptografia da Conta não é acessível pelo Armazenamento do Azure.

**P: Posso criar uma conta de armazenamento e uma chave em uma região diferente?**

R: Não, a conta de armazenamento e o cofre de chaves ou a chave precisam estar na mesma região. 

**P: Posso habilitar a SSE com chaves gerenciadas pelo cliente durante a criação da conta de armazenamento?**

R: não. Ao habilitar a SSE durante a criação a conta de armazenamento, você pode usar somente chaves gerenciadas pela Microsoft. Se você desejar usar chaves gerenciadas pelo cliente, precisará atualizar as propriedades da conta de armazenamento. Use a REST ou uma das bibliotecas de clientes do armazenamento para atualizar de forma programática a conta de armazenamento ou atualizar as propriedades da conta de armazenamento usando o Portal do Azure após a criação da conta.

**P: Posso desabilitar a criptografia durante o uso da SSE com chaves gerenciadas pelo cliente?**

R: Não. Não é possível desabilitar a criptografia durante o uso da SSE com chaves gerenciadas pelo cliente. Para desabilitar a criptografia, você precisa alternar para o uso de chaves gerenciadas pela Microsoft. Faça isso usando o portal do Azure ou o PowerShell.

**P: O SSE é habilitado por padrão quando eu crio uma nova conta de armazenamento?**

R: O SSE não está habilitado por padrão. Você pode usar o portal do Azure para habilitá-lo. Você também pode habilitar esse recurso por meio de programação usando a API REST do Provedor de Recursos de Armazenamento. 

**P: Não consigo habilitar a criptografia em minha conta de armazenamento.**

R: A conta é uma conta de armazenamento do Resource Manager? Não há suporte para contas de armazenamento clássicas. A SSE com chaves gerenciadas pelo cliente também pode ser habilitada apenas em contas de armazenamento do Resource Manager recém-criadas.

**P: A SSE com chaves gerenciadas pelo cliente é permitida somente em regiões específicas?**

R: A SSE está disponível apenas em determinadas regiões para o Armazenamento de blobs nesta versão prévia. Envie um email para [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) para verificar a disponibilidade e os detalhes sobre a versão prévia. 

**P: Como devo contatar alguém se eu tiver problemas ou quiser enviar comentários?**

R: Entre em contato com [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) para falar sobre problemas relacionados à Criptografia do Serviço de Armazenamento. 

## <a name="next-steps"></a>Próximas etapas

*   Para saber mais sobre o conjunto abrangente de funcionalidades de segurança que ajuda os desenvolvedores a criar aplicativos seguros, consulte o [Guia de segurança de armazenamento](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide).
*   Para obter informações gerais sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)
*   Para começar a usar o Azure Key Vault, consulte [Introdução ao Azure Key Vault](../../key-vault/key-vault-get-started.md).
