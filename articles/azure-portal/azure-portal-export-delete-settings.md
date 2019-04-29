---
title: Exportar ou excluir configurações do portal do Azure | Microsoft Docs
description: Saiba como você pode exportar ou excluir suas configurações de usuário, painéis privados e configurações personalizadas no portal do Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: kfollis
ms.openlocfilehash: fde7ffbaa3ef4d47eea48302a99948932aeb4f00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551629"
---
# <a name="export-or-delete-user-settings"></a>Exportar ou excluir configurações do usuário

Você pode usar as configurações e recursos no portal do Azure para criar uma experiência personalizada. Informações sobre as configurações personalizadas são armazenadas no Azure. Você pode exportar ou excluir os dados de usuário a seguir:

* Painéis privados no portal do Azure
* Configurações do usuário, como favoritas assinaturas ou diretórios e último diretório conectado
* Temas e outras configurações do portal personalizadas

É uma boa ideia para exportar e examinar suas configurações antes de excluí-los. Recriação de painéis ou refazer configurações personalizadas pode ser demorado.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportar ou excluir suas configurações do portal

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No cabeçalho do portal, selecione **configurações**.

    ![Captura de tela que mostra o ícone de engrenagem das configurações do portal](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Selecione **Exportar todas as configurações** ou **Excluir todas as configurações e os dashboards particulares**.

    ![Captura de tela que mostra o portal de exportar e excluir configurações](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      A tabela a seguir descreve essas ações.

      | Ação | DESCRIÇÃO |
      | --- | --- |
      | **Exportar todas as configurações** | Cria um arquivo. JSON que contém as configurações de usuário, como o tema de cores, Favoritos e painéis privados.|
      | **Excluir todas as configurações e painéis privados** | Exclui todos os links para os painéis privados e outras configurações personalizadas que você fez para o portal. |

> [!NOTE]
> Devido à natureza dinâmica das configurações de usuário e o risco de corrupção de dados, você não pode importar configurações de arquivo. JSON.
>
>


## <a name="next-steps"></a>Próximas etapas

* [Criar e compartilhar painéis do Azure](azure-portal-dashboard-share-access.md)
* [Adicionar, remover e classificar favoritos](azure-portal-add-remove-sort-favorites.md)
