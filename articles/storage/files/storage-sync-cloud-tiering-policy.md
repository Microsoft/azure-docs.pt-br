---
title: Sincronização de Arquivos do Azure políticas de camadas de nuvem | Microsoft Docs
description: Detalhes sobre como as políticas de espaço livre de data e volume funcionam juntas para cenários diferentes.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3020737e91e1fe5c4af3e23a147fa0ea16037d3b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204286"
---
# <a name="cloud-tiering-policies"></a>Políticas de camadas de nuvem

A camada de nuvem tem duas políticas que determinam quais arquivos estão em camadas na nuvem: a **política de espaço livre do volume** e a política de **Data**.

A **política de espaço livre do volume** garante que um percentual especificado do volume local no qual o ponto de extremidade do servidor está localizado seja sempre mantido livre. 

Os arquivos de camadas de **política de data** último acessados x dias atrás ou posterior. A política de espaço livre do volume sempre terá precedência; Quando não houver espaço livre suficiente no volume para armazenar o máximo de dias de arquivos, conforme descrito pela política de data, Sincronização de Arquivos do Azure substituirá a política de data e continuará a colocar os arquivos mais frios até que a porcentagem de espaço livre do volume seja atendida.

## <a name="how-both-policies-work-together"></a>Como ambas as políticas funcionam em conjunto

Usaremos um exemplo para ilustrar como essas políticas funcionam: digamos que você tenha configurado Sincronização de Arquivos do Azure em um volume local de 500 GB e a camada de nuvem nunca tenha sido habilitada. Estes são os arquivos em seu compartilhamento de arquivos:

|Nome do Arquivo |Hora do último acesso  |Tamanho do arquivo  |Armazenado em |
|----------|------------------|-----------|----------|
|Arquivo 1    | 2 dias atrás  | 10 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 2    | 10 dias atrás | 30 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 3    | 1 ano atrás | 200 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 4    | 1 ano, 2 dias atrás | 130 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 5    | 2 anos, 1 dia atrás | 140 GB | Servidor e compartilhamento de arquivos do Azure

**Alteração 1:** Você habilitou a camada de nuvem, definiu uma política de espaço livre em volume de 20% e manteve a política de data desabilitada. Com essa configuração, a camada de nuvem garante 20% (neste caso, 100 GB) de espaço é mantida livre e disponível no computador local. Como resultado, a capacidade total do cache local é de 400 GB. Esse 400 GB armazenará os arquivos mais recentes e acessados com frequência no volume local.

Com essa configuração, somente os arquivos de 1 a 4 seriam armazenados no cache local e o arquivo 5 seria em camadas. Isso é de apenas 370 GB de 400 GB que poderiam ser usados. O arquivo 5 é 140 GB e excederá o limite de 400 GB se ele tiver sido armazenado em cache localmente. 

**Alteração 2:** Digamos que um usuário acesse o arquivo 5. Isso torna o arquivo 5 o arquivo acessado mais recentemente no compartilhamento. Como resultado, o arquivo 5 seria armazenado no cache local e, para caber no limite de 400 GB, o arquivo 4 seria em camadas. A tabela a seguir mostra onde os arquivos são armazenados, com essas atualizações:

|Nome do Arquivo |Hora do último acesso  |Tamanho do arquivo  |Armazenado em |
|----------|------------------|-----------|----------|
|Arquivo 5    | 2 horas atrás | 140 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 1    | 2 dias atrás  | 10 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 2    | 10 dias atrás | 30 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 3    | 1 ano atrás | 200 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 4    | 1 ano, 2 dias atrás | 130 GB | Compartilhamento de arquivos do Azure, em camadas localmente

**Alteração 3:** Digamos que você atualizou as políticas para que a política de camadas baseada em data seja de 60 dias e a política de espaço livre do volume seja 70%. Agora, somente até 150 GB podem ser armazenados no cache local. Embora o arquivo 2 tenha sido acessado há menos de 60 dias, a política de espaço livre do volume substituirá a política de data e o arquivo 2 será colocado em camadas para manter o% de espaço livre local de 70%.

**Alteração 4:** Se você alterou a política de espaço livre do volume para 20% e, em seguida, usou `Invoke-StorageSyncFileRecall` para recuperar todos os arquivos que se encaixam na unidade local enquanto obedecem às políticas de camadas de nuvem, a tabela ficaria assim:

|Nome do Arquivo |Hora do último acesso  |Tamanho do arquivo  |Armazenado em |
|----------|------------------|-----------|----------|
|Arquivo 5    | 1 hora atrás  | 140 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 1    | 2 dias atrás  | 10 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 2    | 10 dias atrás | 30 GB | Servidor e compartilhamento de arquivos do Azure
|Arquivo 3    | 1 ano atrás | 200 GB | Compartilhamento de arquivos do Azure, em camadas localmente
|Arquivo 4    | 1 ano, 2 dias atrás | 130 GB | Compartilhamento de arquivos do Azure, em camadas localmente

Nesse caso, os arquivos 1, 2 e 5 seriam armazenados localmente em cache e os arquivos 3 e 4 seriam em camadas. Como a política de data é de 60 dias, os arquivos 3 e 4 são em camadas, mesmo que a política de espaço livre do volume permita até 400 GB localmente.

> [!NOTE] 
> Os arquivos não são automaticamente rechamados quando os clientes alteram a política de espaço livre do volume para um valor menor (por exemplo, de 20% a 10%) ou altere a política de data para um valor maior (por exemplo, de 20 dias a 50 dias).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Vários pontos de extremidade do servidor em um volume local

A camada de nuvem pode ser habilitada para vários pontos de extremidade de servidor em um único volume local. Para essa configuração, você deve definir o espaço livre do volume para o mesmo valor de todos os pontos de extremidade do servidor no mesmo volume. Se você definir diferentes políticas de espaço livre de volume para vários pontos de extremidade de servidor no mesmo volume, a maior porcentagem de espaço livre de volume terá precedência. Isso é chamado de **política de espaço livre do volume em vigor**. Por exemplo, se você tiver três pontos de extremidade de servidor no mesmo volume local, um definido como 15%, outro definido como 20% e um terceiro definido como 30%, todos eles começarão a hierarquizar os arquivos mais frios quando tiverem menos de 30% de espaço livre disponível.

## <a name="next-steps"></a>Próximas etapas
* [Monitorar camadas de nuvem](storage-sync-monitor-cloud-tiering.md)
