---
title: Proteger seu cluster no Azure Data Explorer
description: Este artigo descreve como proteger seu cluster no Azure Data Explorer no portal do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: fbc5b18de093b2c91b17fa310c08a5b02b113a22
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406516"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Proteger seu cluster no Azure Data Explorer

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) ajuda a proteger e proteger seus dados para atender aos compromissos de segurança e conformidade da organização. Ele fornece criptografia de volume para o sistema operacional e discos de dados de suas máquinas virtuais de cluster. Ele também se integra com [Azure Key Vault](/azure/key-vault/) para ajudá-lo a controlar e gerenciar as chaves e os segredos de criptografia de disco e garante que todos os dados nos discos de VM sejam criptografados em repouso no armazenamento do Azure. As configurações de segurança do cluster permitem habilitar a criptografia de disco no cluster.
  
## <a name="enable-encryption-at-rest"></a>Habilitar criptografia em repouso
  
Habilitar a [criptografia em repouso](/azure/security/azure-security-encryption-atrest) no cluster fornece proteção de dados para dados armazenados (em repouso). 

1. Na portal do Azure, vá para o recurso de cluster de Data Explorer do Azure. No título **configurações** , selecione **segurança**. 

    ![Ativar a criptografia em repouso](media/manage-cluster-security/security-encryption-at-rest.png)

1. Na janela **segurança** , selecione **ativado** para a configuração de segurança de **criptografia de disco** . 

1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Verificar integridade do cluster](/azure/data-explorer/check-cluster-health)
