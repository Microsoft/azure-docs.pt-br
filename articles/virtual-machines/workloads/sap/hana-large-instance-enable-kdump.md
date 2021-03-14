---
title: Script para habilitar kdump em SAP HANA (instâncias grandes) | Microsoft Docs
description: Script para habilitar kdump no tipo de SAP HANA (instâncias grandes) do HLI Type I, HLI tipo II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e4b06cbcbef79e243116bddb33adbcf6476fac8a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213361"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>Kdump para SAP HANA em Instâncias Grandes do Azure (HLI)

Configurar e habilitar o kdump é uma etapa necessária para solucionar problemas de falhas do sistema que não têm uma causa clara.
Há ocasiões em que um sistema falhará inesperadamente que não pode ser explicado por um problema de hardware ou infraestrutura.
Nesses casos, ele pode ser um problema de sistema operacional ou aplicativo e o kdump permitirá que o SUSE determine por que um sistema falhou.

## <a name="enable-kdump-service"></a>Habilitar serviço kdump

Este documento descreve os detalhes sobre como habilitar o serviço kdump no Azure HANA em instâncias grandes (**tipo I e tipo II**)

## <a name="supported-skus"></a>SKUs com suporte

|  Tipo de instância grande do Hana   |  Fornecedor do sistema operacional   |  Versão do pacote do so   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Pré-requisitos

- O serviço kdump usa `/var/crash` o diretório para gravar despejos, verifique se a partição correspondente a esse diretório tem espaço suficiente para acomodar despejos.

## <a name="setup-details"></a>Detalhes de configuração

- O script para habilitar kdump pode ser encontrado [aqui](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh)
> [!NOTE]
> Esse script é feito com base em nossa configuração de laboratório e o cliente deve contatar o fornecedor do sistema operacional para qualquer ajuste adicional.
> O LUN separado será provisionado para os servidores novos e existentes para salvar os despejos e o script cuidar da configuração do sistema de arquivos fora do LUN.
> A Microsoft não será responsável por analisar o despejo. O cliente precisa abrir um tíquete com o fornecedor do sistema operacional para que ele seja analisado.

- Execute este script no SAP HANA em instâncias grandes usando o comando abaixo

    > [!NOTE]
    > privilégio sudo necessário para executar este comando.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Se o comando gerar kdump for habilitado com êxito, certifique-se de reinicializar o sistema para aplicar as alterações com êxito.

- Se a saída do comando falhar ao executar determinada operação, saindo!!!!, o serviço kdump não estiver habilitado. Consulte a seção [problema de suporte](#support-issue).

## <a name="test-kdump"></a>Testar kdump

> [!NOTE]
>  A operação abaixo disparará uma falha de kernel e uma reinicialização do sistema.

- Disparar uma falha de kernel

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- Depois que o sistema for reinicializado com êxito, verifique o `/var/crash` diretório para os logs de falhas do kernel.

- Se o `/var/crash` diretório tiver a data atual, o kdump será habilitado com êxito.

## <a name="support-issue"></a>Problema de suporte

Se o script falhar com um erro ou kdump não estiver habilitado, gere a solicitação de serviço com a equipe de suporte da Microsoft com os seguintes detalhes.

* ID da assinatura do HLI

* Nome do servidor

* Fornecedor do sistema operacional

* Versão do SO

* Versão do kernel

## <a name="related-documents"></a>Documentos relacionados
- Para saber mais sobre como [Configurar o kdump](https://www.suse.com/support/kb/doc/?id=3374462)
