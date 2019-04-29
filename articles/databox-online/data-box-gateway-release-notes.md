---
title: Notas de versão de disponibilidade geral de Gateway de caixa de dados do Azure | Microsoft Docs
description: Descreve problemas em aberto críticos e resoluções para o Gateway de caixa de dados do Azure executando a versão disponibilidade geral.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754192"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Notas de versão de disponibilidade de dados do Azure/borda dados caixa Gateway geral da caixa do Azure

## <a name="overview"></a>Visão geral

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para disponibilidade geral (GA) de versão para a borda de caixa de dados do Azure e o Gateway de caixa de dados do Azure.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar seu Gateway de caixa de dados de caixa de borda/dados, examine cuidadosamente as informações contidas nas notas de versão.

A versão GA corresponde às versões de software:

- **Gateway de caixa de dados 1903 (1.5.814.447)**
- **Borda da caixa de dados 1903 (1.5.814.447)**


## <a name="whats-new"></a>Novidades

- **Novas imagens de disco virtual** -novo VHDX e VMDK agora estão disponíveis no portal do Azure. Baixe essas imagens para provisionar, configurar e implantar novos dispositivos de Gateway da caixa de dados GA. Os dispositivos do Data Box Gateway criados em versões prévias anteriores não podem ser atualizados para esta versão. Para saber mais, acesse [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Suporte de NFS** -suporte ao NFS está atualmente em visualização e disponível para v 3.0 e v 4.1 clientes que acessam os dispositivos de borda da caixa de dados e o Gateway de caixa de dados.
- **Resiliência de armazenamento** -dispositivo do Edge sua caixa de dados pode suportar a falha de um disco de dados com o recurso de resiliência de armazenamento. Esse recurso está atualmente na visualização. Você pode habilitar a resiliência de armazenamento, selecionando o **resiliente** opção a **configurações de armazenamento** em local da web da interface do usuário.


## <a name="known-issues-in-ga-release"></a>Problemas conhecidos na versão de GA

A tabela a seguir fornece um resumo dos problemas conhecidos para o Gateway de caixa de dados executando a versão.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Tipos de arquivo | Não há suporte para os seguintes tipos de arquivo: arquivos, bloquear arquivos, soquetes, pipes, links simbólicos de caracteres.  |A cópia desses arquivos resulta na criação de arquivos de comprimento 0 no compartilhamento NFS. Esses arquivos permanecem no estado de erro e também são relatados no *error.xml*. <br> Links simbólicos para diretórios resultam nos diretórios nunca serem marcados como offline. Como resultado, você não poderá ver a cruz cinza nos diretórios que indica que os diretórios estão offline e todo o conteúdo associado foi completamente carregado no Azure. |
| **2.** |Exclusão | Devido a um bug nesta versão, se um compartilhamento NFS for excluído, o compartilhamento não poderá ser excluído. O status do compartilhamento mostrará *Excluindo*.  |Isso ocorre apenas quando o compartilhamento estiver usando um nome de arquivo sem suporte. |
| **3.** |Copiar | A cópia de dados falha com erro:  Não foi possível concluir a operação solicitada devido a uma limitação do sistema de arquivos.  |Não há suporte para o Stream de dados alternativo (ADS) associado com o tamanho de arquivo maior que 128 KB.   |


## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Preparar para implantar a borda de caixa de dados do Azure](data-box-edge-deploy-prep.md).
