---
title: Definir configurações de exportação na API do Azure para FHIR
description: Este artigo descreve como definir configurações de exportação na API do Azure para FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: e4adceea5c2cd2a36d7a867ca9b9d2ad7c33c155
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91529976"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Configurar a configuração de exportação e configurar a conta de armazenamento

A API do Azure para FHIR dá suporte ao comando $export que permite exportar os dados da API do Azure para a conta FHIR para uma conta de armazenamento.

Há três etapas envolvidas na configuração da exportação na API do Azure para FHIR:

1. Habilitar identidade gerenciada na API do Azure para o serviço FHIR
2. Criar uma conta de armazenamento do Azure (se não for feito antes) e atribuir permissão à API do Azure para FHIR à conta de armazenamento
3. Selecionando a conta de armazenamento na API do Azure para FHIR como exportar conta de armazenamento

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Habilitando identidade gerenciada na API do Azure para FHIR

A primeira etapa na configuração da API do Azure para FHIR para exportação é habilitar a identidade gerenciada em todo o sistema no serviço. Você pode ler tudo sobre identidades gerenciadas no Azure [aqui](../active-directory/managed-identities-azure-resources/overview.md).

Para fazer isso, navegue até a API do Azure para o serviço FHIR e selecione folha de identidade. Alterar o status para ativado permitirá a identidade gerenciada na API do Azure para o serviço FHIR.

![Habilitar identidade gerenciada](media/export-data/fhir-mi-enabled.png)

Agora, podemos passar para a próxima etapa e criar uma conta de armazenamento e atribuir a permissão ao nosso serviço.

## <a name="adding-permission-to-storage-account"></a>Adicionando permissão à conta de armazenamento

A próxima etapa na exportação é atribuir permissão para a API do Azure para o serviço FHIR gravar na conta de armazenamento.

Depois de criarmos uma conta de armazenamento, navegue até a folha controle de acesso (IAM) na conta de armazenamento e selecione Adicionar atribuições de função

![Exportar atribuição de função](media/export-data/fhir-export-role-assignment.png)

Aqui, adicionamos o colaborador de dados de blob de armazenamento de função ao nosso nome de serviço.

![Adicionar função](media/export-data/fhir-export-role-add.png)

Agora estamos prontos para a próxima etapa, na qual podemos selecionar a conta de armazenamento na API do Azure para FHIR como uma conta de armazenamento padrão para $export.

## <a name="selecting-the-storage-account-for-export"></a>Selecionando a conta de armazenamento para $export

A etapa final é atribuir a conta de armazenamento do Azure que a API do Azure para FHIR usará para exportar os dados para o. Para fazer isso, navegue até a folha de integração na API do Azure para serviço FHIR em portal do Azure e selecione a conta de armazenamento

![Armazenamento de exportação FHIR](media/export-data/fhir-export-storage.png)

Depois disso, estamos prontos para exportar os dados usando $export comando.

>[!div class="nextstepaction"]
>[Configurações adicionais](azure-api-for-fhir-additional-settings.md)
