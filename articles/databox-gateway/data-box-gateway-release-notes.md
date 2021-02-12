---
title: Notas de versão de disponibilidade geral do Gateway do Azure Data Box | Microsoft Docs
description: Descreve problemas e resoluções críticos em aberto para o Gateway do Azure Data Box executando a versão de disponibilidade geral.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 2984f7990b9570c5ec57633de7f7e50162fb6f46
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581527"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Notas de versão de disponibilidade geral do Azure Data Box Edge/Gateway do Azure Data Box

## <a name="overview"></a>Visão geral

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para a versão de GA (disponibilidade geral) para Azure Data Box Edge e Gateway do Azure Data Box. 

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar seu Data Box Edge/Gateway do Data Box, examine atentamente as informações contidas nas notas de versão.

A versão GA corresponde às versões de software:

- **Gateway do Data Box 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Novidades

- **Novas imagens de disco virtual** – agora, há um novo VHDX e VMDK disponíveis no portal do Azure. Baixe essas imagens para provisionar, configurar e implantar novos dispositivos Gateway do Data Box GA. Os dispositivos do Data Box Gateway criados em versões prévias anteriores não podem ser atualizados para esta versão. Para saber mais, acesse [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Suporte a NFS** -o suporte a NFS está atualmente em visualização e disponível para clientes v 3.0 e v 4.1 que acessam os dispositivos Data Box Edge e gateway do data box.
- **Resiliência de armazenamento** -seu dispositivo de data Box Edge pode resistir à falha de um disco de dados com o recurso de resiliência de armazenamento. Esse recurso está atualmente na visualização. Você pode habilitar a resiliência de armazenamento selecionando a opção **resiliente** nas **configurações de armazenamento** na interface do usuário da Web local.


## <a name="known-issues-in-ga-release"></a>Problemas conhecidos na versão GA

A tabela a seguir fornece um resumo dos problemas conhecidos para o Gateway do Data Box versão em execução.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Tipos de arquivo | Não há suporte para os seguintes tipos de arquivo: Arquivos de caractere, arquivos de bloco, soquetes, Pipes, links simbólicos.  |A cópia desses arquivos resulta na criação de arquivos de comprimento 0 no compartilhamento NFS. Esses arquivos permanecem no estado de erro e também são relatados no *error.xml*. <br> Links simbólicos para diretórios resultam nos diretórios nunca serem marcados como offline. Como resultado, você não poderá ver a cruz cinza nos diretórios que indica que os diretórios estão offline e todo o conteúdo associado foi completamente carregado no Azure. |
| **2.** |Exclusão | Devido a um bug nesta versão, se um compartilhamento NFS for excluído, o compartilhamento não poderá ser excluído. O status do compartilhamento mostrará *Excluindo*.  |Isso ocorre apenas quando o compartilhamento estiver usando um nome de arquivo sem suporte. |
| **3.** |Copiar | Falha na cópia de dados com erro: a operação solicitada não pôde ser concluída devido a uma limitação do sistema de arquivos.  |Não há suporte para o fluxo de dados alternativo (ADS) associado ao tamanho de arquivo maior que 128 KB.   |


## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Prepare-se para implantar Azure data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md).
