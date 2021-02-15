---
title: Inicialização do sistema operacional – o computador foi reiniciado inesperadamente ou encontrou um erro inesperado
description: Este artigo fornece etapas para resolver problemas em que a VM passa por uma reinicialização inesperada ou erro durante a instalação do Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: d8d2ab2bb3f24e1faa4791ebdc1ce3852f6a790e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632683"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Inicialização do sistema operacional – o computador foi reiniciado inesperadamente ou encontrou um erro inesperado

Este artigo fornece etapas para resolver problemas em que a VM (máquina virtual) passa por uma reinicialização inesperada ou erro durante a instalação do Windows.

## <a name="symptom"></a>Sintoma

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe falha na instalação do Windows com o seguinte erro:

**O computador foi reiniciado inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode continuar. Para instalar o Windows, clique em "OK" para reiniciar o computador e reinicie a instalação.**

![Erro enquanto a instalação do Windows está em andamento: o computador foi reiniciado inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode continuar. Para instalar o Windows, clique em "OK" para reiniciar o computador e reinicie a instalação.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Erro quando a instalação da instalação do Windows está iniciando serviços: o computador foi reiniciado inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode continuar. Para instalar o Windows, clique em "OK" para reiniciar o computador e reinicie a instalação.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Causa

O computador está tentando fazer uma inicialização inicial de uma [imagem generalizada](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), mas encontra problemas devido a um arquivo de resposta personalizado (Unattend.xml) sendo processado. **Não há suporte para arquivos de resposta personalizados no Azure**. 

O arquivo de resposta é um arquivo XML especial que contém definições de configuração e valores para as definições de configuração que você deseja automatizar durante a instalação de uma instalação do sistema operacional Windows Server. As opções de configuração incluem instruções sobre como particionar discos, onde encontrar a imagem do Windows a ser instalada, chaves do produto a serem aplicadas e outros comandos que você gostaria de executar.

Novamente, os arquivos de resposta personalizados não têm suporte no Azure. Assim, essa situação ocorre quando uma imagem foi preparada para uso no Azure, mas você especificou um arquivo de Unattend.xml personalizado usando o **Sysprep** com um sinalizador semelhante ao seguinte comando:

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

No Azure, use a opção **OOBE (experiência inicial no uso do sistema)** na GUI da **ferramenta de preparação do sistema** ou use `sysprep /oobe` em vez do arquivo de Unattend.xml.

Esse problema é criado com mais frequência enquanto você está usando o Sysprep com uma VM local para carregar uma VM generalizada no Azure. Nessa situação, você também pode estar interessado em como carregar corretamente uma VM generalizada.

## <a name="solution"></a>Solução

### <a name="do-not-use-unattendxml"></a>Não usar Unattend.xml

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

Para corrigir esse problema, siga [as diretrizes do Azure sobre como preparar/capturar uma imagem](../windows/upload-generalized-managed.md) e preparar uma nova imagem generalizada. Durante o Sysprep, não **use o `/unattend:<your file’s name>` sinalizador**. Em vez disso, use apenas os sinalizadores abaixo:

`sysprep /oobe /generalize /shutdown`

- O OOBE (check-of-Box-Experience) é a configuração com suporte para VMs do Azure.

Você também pode usar a **GUI da ferramenta de preparação do sistema** para realizar a mesma tarefa que o comando acima, selecionando as opções mostradas abaixo:

- Entrar na experiência de entrada
- Generalizar
- Desligar
 
![Janela da ferramenta de preparação do sistema com opções de OOBE, generalize e Shutdown selecionadas.](./media/unexpected-restart-error-during-vm-boot/3.png)
