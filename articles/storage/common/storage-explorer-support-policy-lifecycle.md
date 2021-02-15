---
title: Ciclo de vida de suporte do Gerenciador de Armazenamento do Azure | Microsoft Docs
description: Visão geral da política de suporte e do ciclo de vida para Gerenciador de Armazenamento do Azure
services: storage
author: MRayermannMSFT
manager: jinglouMSFT
ms.service: storage
ms.topic: article
ms.date: 07/10/2020
ms.author: marayerm
ms.openlocfilehash: dc89631208c6a72c4b48d49260f584149d6c4260
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679939"
---
# <a name="azure-storage-explorer-support-lifecycle-and-policy"></a>Ciclo de vida e política de suporte do Gerenciador de Armazenamento do Azure

Este documento aborda o ciclo de vida de suporte e a política para Gerenciador de Armazenamento do Azure.

## <a name="update-schedule-and-process"></a>Atualizar agendamento e processo

O Gerenciador de Armazenamento do Azure é lançado quatro a seis vezes por ano. A Microsoft também pode ignorar essa agenda para liberar correções para problemas críticos.

Gerenciador de Armazenamento verifica se há atualizações a cada hora e as baixa quando estão disponíveis. No entanto, a aceitação do usuário é necessária para iniciar o processo de instalação. Se os usuários optarem por atualizar em um momento diferente, eles poderão verificar manualmente se há atualizações. No Windows e no Linux, os usuários podem verificar se há atualizações selecionando **verificar atualizações** no menu **ajuda** . No macOS, **as verificações de atualizações** podem ser encontradas no **menu do aplicativo**. Os usuários também podem ir diretamente para a página de download de [Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) para baixar e instalar a versão mais recente.

É altamente recomendável usar sempre as versões mais recentes do Gerenciador de Armazenamento. Se um problema estiver impedindo a atualização para a versão mais recente do Gerenciador de Armazenamento, abra um problema em nosso [GitHub](https://github.com/microsoft/AzureStorageExplorer).

## <a name="support-lifecycle"></a>Ciclo de vida de suporte

O Gerenciador de Armazenamento é regido pela [política moderna do ciclo de vida](https://support.microsoft.com/help/30881/modern-lifecycle-policy). Espera-se que os usuários mantenham sua instalação do Gerenciador de Armazenamento atualizado. Manter-se atualizado garante que os usuários tenham os recursos mais recentes, aprimoramentos de desempenho, segurança e confiabilidade do serviço.

A partir da versão 1.14.1, qualquer versão Gerenciador de Armazenamento que tenha mais de 12 meses será considerada sem suporte. Todas as versões antes de 1.14.1 serão consideradas incompatíveis a partir de 14 de julho de 2021. As versões que estão sem suporte não têm mais garantia de funcionar totalmente como projetadas e esperadas. Para obter uma lista de todas as versões, sua data de lançamento e o fim da data de suporte, consulte [versões](#releases).

A partir da versão 1.13.0, um alerta no aplicativo será exibido quando uma versão estiver aproximadamente um mês longe de ficar sem suporte. O alerta incentiva os usuários a atualizarem para a versão mais recente do Gerenciador de Armazenamento. Depois que uma versão estiver sem suporte, o alerta no aplicativo será exibido em cada inicialização.

## <a name="releases"></a>Versões

Esta tabela descreve a data de lançamento e o fim da data de suporte para cada versão do Gerenciador de Armazenamento do Azure.

| Versão do Gerenciador de Armazenamento  | Data de liberação       | Data do fim do suporte |
|:-------------------------:|:------------------:|:-------------------:|
| v 1.17.0                   | 15 de dezembro de 2020  | 15 de dezembro de 2021   |
| v 1.16.0                   | 10 de novembro de 2020  | 10 de novembro de 2021   |
| v 1.15.1                   | 2 de setembro de 2020  | 2 de setembro de 2021   |
| v 1.15.0                   | 27 de agosto de 2020    | 27 de agosto de 2021     |
| v 1.14.2                   | 16 de julho de 2020      | 16 de julho de 2021       |
| v 1.14.1                   | 14 de julho de 2020      | 14 de julho de 2021       |
| v 1.14.0                   | 24 de junho de 2020      | 14 de julho de 2021       |
| v 1.13.1                   | 18 de maio de 2020       | 14 de julho de 2021       |
| v 1.13.0                   | 1 de maio de 2020        | 14 de julho de 2021       |
| v 1.12.0                   | 16 de janeiro de 2020   | 14 de julho de 2021       |
| v 1.11.2                   | 17 de dezembro de 2019  | 14 de julho de 2021       |
| v 1.11.1                   | 20 de novembro de 2019  | 14 de julho de 2021       |
| v 1.11.0                   | 4 de novembro de 2019   | 14 de julho de 2021       |
| v 1.10.1                   | 19 de setembro de 2019 | 14 de julho de 2021       |
| v 1.10.0                   | 12 de setembro de 2019 | 14 de julho de 2021       |
| v1.9.0                    | 1º de julho de 2019       | 14 de julho de 2021       |
| v 1.8.1                    | 10 de maio de 2019       | 14 de julho de 2021       |
| v 1.8.0                    | 2 de maio de 2019        | 14 de julho de 2021       |
| v 1.7.0                    | 5 de março de 2019      | 14 de julho de 2021       |
| v 1.6.2                    | 8 de janeiro de 2019    | 14 de julho de 2021       |
| v1.6.1                    | 18 de dezembro de 2018  | 14 de julho de 2021       |
| v 1.6.0                    | 4 de dezembro de 2018   | 14 de julho de 2021       |
| v 1.5.0                    | 29 de outubro de 2018   | 14 de julho de 2021       |
| v 1.4.4                    | 15 de outubro de 2018   | 14 de julho de 2021       |
| v 1.4.2                    | 24 de setembro de 2018 | 14 de julho de 2021       |
| v 1.4.1                    | 28 de Agosto de 2018    | 14 de julho de 2021       |
| v 1.3.1                    | 11 de julho de 2018      | 14 de julho de 2021       |
| v 1.2.0                    | 12 de junho de 2018      | 14 de julho de 2021       |
| v 1.1.0                    | 9 de maio de 2018        | 14 de julho de 2021       |
| v 1.0.0 (e mais antigo)        | 16 de abril de 2018     | 14 de julho de 2021       |
