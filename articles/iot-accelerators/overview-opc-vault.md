---
title: O que é o OPC Vault – Azure | Microsoft Docs
description: Este artigo fornece uma visão geral do OPC Vault. Ele pode configurar, registrar e gerenciar o ciclo de vida de certificados para aplicativos OPC adquiridos pelo usuário na nuvem.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 715ed204e28d6260c28fa099b40fc78aa12de44d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646499"
---
# <a name="what-is-opc-vault"></a>O que é o OPC Vault?

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

O OPC Vault é um microsserviço que pode configurar, registrar e gerenciar o ciclo de vida do certificado para aplicativos servidor e cliente da UA de OPC na nuvem. Este artigo descreve os casos de uso simples do OPC Vault.

## <a name="certificate-management"></a>Gerenciamento de certificados

Por exemplo, uma empresa de fabricação precisa conectar seu computador de servidor da UA de OPC a seu aplicativo cliente recém-criado. Quando o fabricante faz o acesso inicial ao computador do servidor, uma mensagem de erro é mostrada imediatamente no aplicativo para servidores da UA de OPC para indicar que o aplicativo cliente não é seguro. Esse mecanismo é criado no computador do servidor da UA de OPC para impedir qualquer acesso não autorizado ao aplicativo, o que impede que hackers mal-intencionados invadam o chão de fábrica.

## <a name="application-security-management"></a>Gerenciamento de segurança de aplicativo
Um profissional de segurança usa o microsserviço OPC Vault para habilitar facilmente o servidor da UA de OPC para se comunicar com qualquer aplicativo cliente, pois o OPC Vault tem todas as funções para registro, armazenamento e gerenciamento de ciclo de vida de certificado. Agora que o servidor da UA de OPC está conectado com segurança, ele pode se comunicar com o aplicativo cliente que acaba de ser criado

## <a name="the-complete-opc-vault-architecture"></a>A arquitetura completa do OPC Vault
O diagrama a seguir ilustra a arquitetura completa do OPC Vault.

![Arquitetura do OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre o Cofre OPC e seus usos, está é a próxima etapa sugerida:

[Arquitetura do OPC Vault](overview-opc-vault-architecture.md)