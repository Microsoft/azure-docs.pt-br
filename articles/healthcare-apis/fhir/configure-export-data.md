---
title: Definir configurações de exportação na API do Azure para FHIR
description: Este artigo descreve como definir configurações de exportação na API do Azure para FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: matjazl
ms.openlocfilehash: ee110420c697afb6ecad857ba823c61d03c6be6c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046956"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Configurar a configuração de exportação e configurar a conta de armazenamento

A API do Azure para FHIR dá suporte ao comando $export que permite exportar os dados da API do Azure para a conta FHIR para uma conta de armazenamento.

Há três etapas envolvidas na configuração da exportação na API do Azure para FHIR:

1. Habilite a identidade gerenciada na API do Azure para o serviço FHIR.
2. Criar uma conta de armazenamento do Azure (se não for feito antes) e atribuir permissão à API do Azure para FHIR à conta de armazenamento.
3. Selecionando a conta de armazenamento na API do Azure para FHIR como exportar conta de armazenamento.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Habilitando identidade gerenciada na API do Azure para FHIR

A primeira etapa na configuração da API do Azure para FHIR para exportação é habilitar a identidade gerenciada em todo o sistema no serviço. Para obter mais informações sobre identidades gerenciadas no Azure, consulte [sobre identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Para fazer isso, vá para a API do Azure para o serviço FHIR e selecione **identidade**. Alterar o status para **ativado** permitirá a identidade gerenciada na API do Azure para o serviço FHIR.

![Habilitar identidade gerenciada](media/export-data/fhir-mi-enabled.png)

Agora, você pode passar para a próxima etapa criando uma conta de armazenamento e atribuir permissão ao nosso serviço.

## <a name="adding-permission-to-storage-account"></a>Adicionando permissão à conta de armazenamento

A próxima etapa na exportação é atribuir permissão para a API do Azure para o serviço FHIR gravar na conta de armazenamento.

Depois de criar uma conta de armazenamento, vá para **controle de acesso (iam)** na conta de armazenamento e selecione **Adicionar atribuição de função**.

![Exportar atribuição de função](media/export-data/fhir-export-role-assignment.png)

Aqui, você adicionará a função de **colaborador de dados de blob de armazenamento** ao nosso nome de serviço e, em seguida, selecionará **salvar**.

![Adicionar função](media/export-data/fhir-export-role-add.png)

Agora você está pronto para selecionar a conta de armazenamento na API do Azure para FHIR como uma conta de armazenamento padrão para $export.

## <a name="selecting-the-storage-account-for-export"></a>Selecionando a conta de armazenamento para $export

A etapa final é atribuir a conta de armazenamento do Azure que a API do Azure para FHIR usará para exportar os dados para o. Para fazer isso, acesse **integração** na API do Azure para o serviço FHIR e selecione a conta de armazenamento.

![Armazenamento de exportação FHIR](media/export-data/fhir-export-storage.png)

Depois de concluir esta etapa final, agora você está pronto para exportar os dados usando $export comando.

> [!Note]
> Somente as contas de armazenamento na mesma assinatura da API do Azure para FHIR podem ser registradas como destino para operações de $export.

Para obter mais informações sobre como configurar definições de banco de dados, controle de acesso, habilitar o log de diagnóstico e usar cabeçalhos personalizados para adicionar os logs de auditoria, consulte:

>[!div class="nextstepaction"]
>[Configurações adicionais](azure-api-for-fhir-additional-settings.md)
