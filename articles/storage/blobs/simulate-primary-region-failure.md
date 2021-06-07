---
title: Tutorial – Simule uma falha na leitura de dados da região primária
titleSuffix: Azure Storage
description: Simule um erro na leitura de dados da região primária quando a conta de armazenamento estiver configurada para RA-GZRS (armazenamento com redundância de zona geográfica com acesso de leitura).
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: 5a0bef4fbfd4ee9aa720dab430a33bbfcd0b918b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91280344"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Tutorial: Simule uma falha na leitura de dados da região primária

Este tutorial é a parte dois de uma série. Nele, você aprenderá sobre os benefícios do [RA-GZRS](../common/storage-redundancy.md) (armazenamento com redundância de zona geográfica com acesso de leitura) simulando uma falha.

Para simular uma falha, use o [roteamento estático](#simulate-a-failure-with-an-invalid-static-route) ou o [Fiddler](#simulate-a-failure-with-fiddler). Os dois métodos permitirão simular uma falha de solicitações para o ponto de extremidade primário de sua conta de armazenamento [RA-GZRS](../common/storage-redundancy.md) (armazenamento com redundância geográfica com acesso de leitura), levando o aplicativo a ler do ponto de extremidade secundário.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na segunda parte da série, você aprenderá como:

> [!div class="checklist"]
> * Executar e pausar o aplicativo
> * Simular uma falha com [uma rota estática inválida](#simulate-a-failure-with-an-invalid-static-route) ou o [Fiddler](#simulate-a-failure-with-fiddler)
> * Simular a restauração do ponto de extremidade primário

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, conclua o tutorial anterior: [Tornar os dados de seu aplicativo altamente disponíveis com o Armazenamento do Azure][previous-tutorial].

Para simular uma falha com roteamento estático, você usará um prompt de comandos com privilégios elevados.

Para simular uma falha usando o Fiddler, baixe e [instale o Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simular uma falha com uma rota estática inválida

É possível criar uma rota estática inválida para todas as solicitações do ponto de extremidade primário da sua conta RA-GZRS ([armazenamento com redundância geográfica com acesso de leitura](../common/storage-redundancy.md)). Neste tutorial, o host local é usado como o gateway para rotear solicitações à conta de armazenamento. Usando o host local como o gateway faz com que todas as solicitações ao seu ponto de extremidade primário da conta de armazenamento executem um loop dentro do host, o que leva à falha como consequência. Siga as etapas a seguir para simular uma falha e a restauração de ponto de extremidade primário com uma rota estática inválida.

### <a name="start-and-pause-the-application"></a>Inicie e pause o aplicativo

Use as instruções no [tutorial anterior][previous-tutorial] para iniciar o exemplo e baixar o arquivo de teste, confirmando que ele vem de armazenamento primário. Dependendo de sua plataforma de destino, você poderá pausar manualmente o exemplo ou esperar em um prompt.

### <a name="simulate-failure"></a>Simular falhas

Com o aplicativo em pausa, abra um prompt de comando no Windows como administrador ou execute o terminal como raiz no Linux.

Obtenha informações sobre o domínio do ponto de extremidade primário da conta de armazenamento inserindo o comando a seguir em um prompt de comando ou um terminal, substituindo `STORAGEACCOUNTNAME` pelo nome da sua conta de armazenamento.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Copie o endereço IP da sua conta de armazenamento para um editor de texto para uso posterior.

Para obter o endereço IP do seu host local, digite `ipconfig` no prompt de comando do Windows ou `ifconfig` no terminal do Linux.

Para adicionar uma rota estática para um host de destino, digite o seguinte comando em um prompt de comando do Windows ou um terminal do Linux, substituindo `<destination_ip>` pelo endereço IP da sua conta de armazenamento e `<gateway_ip>` pelo endereço IP do host local.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

Na janela com o exemplo em execução, retome o aplicativo ou pressione a tecla adequada para baixar o arquivo de exemplo e confirmar que ele vem de armazenamento secundário. Então, você pode pausar o exemplo novamente ou aguardar no prompt.

### <a name="simulate-primary-endpoint-restoration"></a>Simular a restauração do ponto de extremidade primário

Para simular o ponto de extremidade primário se tornando funcional novamente, exclua a rota estática inválida da tabela de roteamento. Isso permite que todas as solicitações ao ponto de extremidade primário sejam roteadas por meio do gateway padrão. Digite o comando a seguir em um prompt de comando do Windows ou terminal do Linux.

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

Em seguida, você poderá retomar o aplicativo ou pressionar a tecla correta para baixar o arquivo de exemplo novamente, mais uma vez confirmando que vem do armazenamento principal.

## <a name="simulate-a-failure-with-fiddler"></a>Simular uma falha com o Fiddler

Para simular uma falha com o Fiddler, injete uma resposta com falha para solicitações para o ponto de extremidade primário de sua conta de armazenamento RA-GZRS.

As seções a seguir explicam como simular uma falha e a restauração do ponto de extremidade primário com o Fiddler.

### <a name="launch-fiddler"></a>Iniciar o Fiddler

Abra o Fiddler, selecione **Regras** e **Personalizar regras**.

![Personalizar as regras do Fiddler](media/simulate-primary-region-failure/figure1.png)

O ScriptEditor do Fiddler é iniciado e exibe o arquivo **SampleRules.js**. Esse arquivo é usado para personalizar o Fiddler.

Cole o seguinte exemplo de código na função `OnBeforeResponse`, substituindo `STORAGEACCOUNTNAME` pelo nome da sua conta de armazenamento. Dependendo do exemplo, pode ser necessário substituir `HelloWorld` pelo nome do arquivo de teste (ou um prefixo, como `sampleFile`) que está sendo baixado. O novo código está comentado para garantir que ele não seja executado imediatamente.

Após a conclusão, selecione **Arquivo** e **Salvar** para salvar as alterações. Deixe a janela do ScriptEditor aberta para usar nas etapas a seguir.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Cole a regra personalizada](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>Inicie e pause o aplicativo

Use as instruções no [tutorial anterior][previous-tutorial] para iniciar o exemplo e baixar o arquivo de teste, confirmando que ele vem de armazenamento primário. Dependendo de sua plataforma de destino, você poderá pausar manualmente o exemplo ou esperar em um prompt.

### <a name="simulate-failure"></a>Simular falhas

Com o aplicativo em pausa, volte para o Fiddler e remova a marca de comentário da regra personalizada que você salvou na função `OnBeforeResponse`. Selecione **Arquivo** e **Salvar** para salvar suas alterações para que a regra entre em vigor. Esse código procura solicitações à conta de armazenamento RA-GZRS e, se o caminho contiver o nome do arquivo, retornará um código de resposta igual a `503 - Service Unavailable`.

Na janela com o exemplo em execução, retome o aplicativo ou pressione a tecla adequada para baixar o arquivo de exemplo e confirmar que ele vem de armazenamento secundário. Então, você pode pausar o exemplo novamente ou aguardar no prompt.

### <a name="simulate-primary-endpoint-restoration"></a>Simular a restauração do ponto de extremidade primário

No Fiddler, remova ou comente a regra personalizada novamente. Selecione **Arquivo** e **Salvar** para garantir que a regra não estará mais em vigor.

Na janela com o exemplo em execução, retome o aplicativo ou pressione a tecla adequada para baixar o arquivo de exemplo e confirme novamente que ele vem de armazenamento primário. Em seguida, você pode sair do exemplo.

## <a name="next-steps"></a>Próximas etapas

Na segunda parte da série, você aprendeu a simular uma falha para testar o armazenamento com redundância geográfica com acesso de leitura.

Para saber mais sobre como funciona o armazenamento RA-GZRS, bem como os riscos associados, leia o seguinte artigo:

> [!div class="nextstepaction"]
> [Criando aplicativos de HA com o RA-GZRS](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
