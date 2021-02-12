---
title: Matriz de suporte para backup de compartilhamento de arquivos do Azure
description: Fornece um resumo das configurações de suporte e limitações ao fazer backup de compartilhamentos de arquivos do Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 999cb4c764e9960c509d19faa61016b2522259ac
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819853"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matriz de suporte para backup de compartilhamento de arquivos do Azure

Você pode usar o [serviço de Backup do Azure](./backup-overview.md) para fazer backup de compartilhamentos de arquivos do Azure. Este artigo resume as configurações de suporte ao fazer backup de compartilhamentos de arquivos do Azure com o Backup do Azure.

## <a name="supported-regions"></a>Regiões com suporte

### <a name="ga-regions-for-azure-file-shares-backup"></a>Regiões de GA para backup de compartilhamentos de arquivos do Azure

O backup de compartilhamentos de arquivos do Azure está disponível em todas as regiões, **exceto** para: Alemanha central (soberanas), Alemanha Nordeste (soberanas), Leste da China, leste da China 2, Norte da China, norte da China 2, US gov Iowa

## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

| Detalhes da conta de armazenamento | Suporte                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo de conta            | O backup do Azure dá suporte a compartilhamentos de arquivos do Azure presentes nas contas de armazenamento de tipo v1, de finalidade geral, V2 e do armazenamento de arquivos |
| Desempenho              | O Backup do Azure oferece suporte a compartilhamentos de arquivos nas contas de Armazenamento Premium e padrão |
| Replicação              | Há suporte para compartilhamentos de arquivos do Azure em contas de armazenamento com qualquer tipo de replicação |
| Firewall habilitado         | Compartilhamentos de arquivos do Azure em contas de armazenamento com regras de firewall que permitem que os serviços Microsoft Azure acessem a conta de armazenamento têm suporte|

## <a name="supported-file-shares"></a>Compartilhamentos de arquivos com suporte

| Tipo de compartilhamento de arquivos                                   | Suporte   |
| -------------------------------------------------- | --------- |
| Standard                                           | Com suporte |
| grande                                              | Com suporte |
| Premium                                            | Com suporte |
| Compartilhamentos de arquivos conectados ao serviço de sincronização de Arquivos do Azure | Com suporte |

## <a name="protection-limits"></a>Limites de proteção

| Configuração                                                      | Limite |
| ------------------------------------------------------------ | ----- |
| Número máximo de compartilhamentos de arquivos que podem ser protegidos por cofre por dia| 200   |
| Número máximo de contas de armazenamento que podem ser registradas por cofre por dia | 50    |
| Número máximo de compartilhamentos de arquivos que podem ser protegidos por cofre | 2000   |
| Número máximo de contas de armazenamento que podem ser registradas por cofre | 200   |

## <a name="backup-limits"></a>Limites do Backup

| Configuração                                      | Limite |
| -------------------------------------------- | ----- |
| Número máximo de backups sob demanda por dia | 10   |
| Número máximo de backups agendados por dia | 1     |

## <a name="restore-limits"></a>Limites de restauração

| Configuração                                                      | Limite   |
| ------------------------------------------------------------ | ------- |
| Número máximo de restaurações por dia                           | 10      |
| Número máximo de arquivos por restauração                         | 99      |
| Tamanho máximo de restauração recomendado por restauração para compartilhamentos de arquivos grandes | 15 TiB |

## <a name="retention-limits"></a>Limites de retenção

| Configuração                                                      | Limite    |
| ------------------------------------------------------------ | -------- |
| Total máximo de pontos de recuperação por compartilhamento de arquivos a qualquer momento | 200      |
| Retenção máxima do ponto de recuperação criado pelo backup sob demanda | 10 anos |
| Retenção máxima de pontos de recuperação diários (instantâneos) por compartilhamento de arquivos| 200 dias |
| Retenção máxima de pontos de recuperação semanais (instantâneos) por compartilhamento de arquivos | 200 semanas |
| Retenção máxima de pontos de recuperação mensais (instantâneos) por compartilhamento de arquivos | 120 meses |
| Retenção máxima de pontos de recuperação anuais (instantâneos) por compartilhamento de arquivos | 10 anos |

## <a name="supported-restore-methods"></a>Métodos de restauração compatíveis

| Método de restauração     | Detalhes                                                      |
| ------------------ | ------------------------------------------------------------ |
| Restauração completa de compartilhamento | Você pode restaurar um compartilhamento completo de arquivos para a localização original ou uma localização alternativa |
| Restauração no nível do item | Você pode restaurar arquivos e pastas individuais para a localização original ou alternativa |

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Fazer backup de compartilhamentos de arquivos do Azure](backup-afs.md)
* Saiba como [Restaurar compartilhamentos de arquivos do Azure](restore-afs.md)
* Saiba como [Gerenciar backups de compartilhamentos de arquivos do Azure](manage-afs-backup.md)
