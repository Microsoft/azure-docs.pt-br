---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 12e9bec0c560f1b068b07a1b6afe218a112e439f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94553195"
---
Após criar um certificado raiz autoassinado, exporte o arquivo .cer de chave pública do certificado raiz (não a chave privada). Posteriormente, será feito upload desse arquivo para o Azure. As etapas a seguir ajudarão você a exportar o arquivo .cer para seu certificado raiz autoassinado:

1. Para obter um arquivo .cer do certificado, abra **Gerenciar certificados de usuário**. Localize o certificado raiz autoassinado, normalmente em 'Certificados – Usuário Atual\Pessoal\Certificados', então clique nele com o botão direito do mouse. Clique em **Todas as Tarefas** e, em seguida, em **Exportar**. Isso abre o **Assistente para Exportação de Certificados**. Se você não encontrar o certificado em Usuário\Pessoal\Certificados, pode ser que você tenha aberto acidentalmente “Certificados - Computador Local” em vez de “Certificados - Usuário Atual”. Se você quiser abrir o Gerenciador de Certificados no escopo do usuário atual usando o PowerShell, digite *certmgr* na janela do console.

   ![Captura de tela mostra a janela certificados para o usuário atual com certificados selecionados e um menu contextual com exportar selecionado de todas as tarefas.](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. No Assistente, clique em **Avançar**.

   ![Exportar o certificado](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Selecione **Não exportar a chave privada** e clique em **Avançar**.

   ![Não exportar a chave privada](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base 64 (.CER).** e clique em **Avançar**.

   ![Codificado em Base 64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Para o **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Avançar**.

   ![Captura de tela mostra o assistente para exportação de certificados com uma caixa de texto nome de arquivo e uma opção de procura.](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Clique em **Concluir** para exportar o certificado.

   ![Captura de tela mostra o assistente para exportação de certificados com as configurações selecionadas.](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. O certificado foi exportado com êxito.

   ![Captura de tela mostra uma mensagem informando que a exportação foi bem-sucedida.](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. O certificado exportado é semelhante a este:

   ![Captura de tela mostra um ícone de certificado e um nome de arquivo com a extensão de nome de arquivo c e r.](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Se você abrir o certificado exportado usando o Bloco de Notas, verá algo semelhante a este exemplo. A seção em azul contém as informações carregadas para o Azure. Se você abrir o certificado com o Bloco de Notas e ele não for semelhante a este, isso normalmente significa que o certificado não foi exportado usando o formato X.509 (.CER) codificado em Base-64. Além disso, se você quiser usar um editor de texto diferente, é importante saber que alguns editores podem introduzir formatação não intencional em segundo plano. Isso pode criar problemas ao fazer upload do texto desse certificado para o Azure.

   ![Abrir com Bloco de Notas](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
