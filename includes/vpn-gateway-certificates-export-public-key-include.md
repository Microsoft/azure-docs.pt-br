---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afd4836229c60ebef1536d4fa1ca4206a492e56d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171798"
---
Após criar um certificado raiz autoassinado, exporte o arquivo .cer de chave pública do certificado raiz (não a chave privada). Posteriormente, será feito upload desse arquivo para o Azure. As etapas a seguir ajudarão você a exportar o arquivo .cer para seu certificado raiz autoassinado:

1. Para obter um arquivo .cer do certificado, abra **Gerenciar certificados de usuário**. Localize o certificado raiz autoassinado, normalmente em 'Certificados – Usuário Atual\Pessoal\Certificados', então clique nele com o botão direito do mouse. Clique em **Todas as Tarefas** e, em seguida, em **Exportar**. Isso abre o **Assistente para Exportação de Certificados**. Se você não encontrar o certificado em Usuário\Pessoal\Certificados, pode ser que você tenha aberto acidentalmente “Certificados - Computador Local” em vez de “Certificados - Usuário Atual”. Se você quiser abrir o Gerenciador de Certificados no escopo do usuário atual usando o PowerShell, digite *certmgr* na janela do console.

   ![Exportação](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. No Assistente, clique em **Avançar**.

   ![Exportar o certificado](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Selecione **Não exportar a chave privada** e clique em **Avançar**.

   ![Não exportar a chave privada](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base 64 (.CER).** e clique em **Avançar**.

   ![Codificado em Base 64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Para o **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Avançar**.

   ![Procurar](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Clique em **Concluir** para exportar o certificado.

   ![Concluir](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. O certificado foi exportado com êxito.

   ![Êxito](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. O certificado exportado é semelhante a este:

   ![Exportado](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Se você abrir o certificado exportado usando o Bloco de Notas, verá algo semelhante a este exemplo. A seção em azul contém as informações carregadas para o Azure. Se você abrir o certificado com o Bloco de Notas e ele não for semelhante a este, isso normalmente significa que o certificado não foi exportado usando o formato X.509 (.CER) codificado em Base-64. Além disso, se você quiser usar um editor de texto diferente, é importante saber que alguns editores podem introduzir formatação não intencional em segundo plano. Isso pode criar problemas ao fazer upload do texto desse certificado para o Azure.

   ![Abrir com Bloco de Notas](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
