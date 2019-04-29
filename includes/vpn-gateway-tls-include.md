---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 07/27/2018
ms.date: 01/21/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 0d5c3b55d20be19d4aeb92b82d6e44d417259a7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319959"
---
1. Abra um prompt de comando com privilégios elevados clicando duas vezes em **Prompt de Comando** e selecionando **Executar como administrador**.
2. Execute os seguintes comandos no prompt de comando:

   ```
   reg add HKLM\SYSTEM\CurrentControlSet\Services\RasMan\PPP\EAP\13 /v TlsVersion /t REG_DWORD /d 0xfc0
   reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
   if %PROCESSOR_ARCHITECTURE% EQU AMD64 reg add "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
   ```

3. Instale as seguintes atualizações:
  
   * [KB3140245](https://www.catalog.update.microsoft.com/search.aspx?q=kb3140245)
   * [KB2977292](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2977292)

4. Reinicialize o computador.
5. Conecte-se à VPN.

> [!NOTE]
> Você precisará definir a chave de registro acima, se você estiver executando uma versão mais antiga do Windows 10 (10240).
>
