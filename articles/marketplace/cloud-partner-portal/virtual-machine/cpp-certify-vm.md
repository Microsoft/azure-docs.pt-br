---
title: Certificar a imagem de VM para o Azure Marketplace | Microsoft Docs
description: Explica como testar e enviar uma imagem de VM para a certificação do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 24430b1b785a24da06a8ea51594147040e6d5bd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638279"
---
# <a name="certify-your-vm-image"></a>Certificar a imagem da VM

Após criar e implantar a VM (máquina virtual), você deverá testar e enviar a imagem da VM para a certificação do Azure Marketplace. Este artigo explica onde obter a *Ferramenta de Teste de Certificação para Certificado do Azure*, como usar essa ferramenta para certificar a imagem da VM e como carregar os resultados da verificação no contêiner do Azure onde residem os VHDs. 


## <a name="download-and-run-the-certification-test-tool"></a>Baixar e executar a ferramenta de teste de certificação

A Ferramenta de Teste de Certificação para Certificado do Azure executa em uma máquina Windows local, mas testa uma VM do Linux ou do Windows baseado no Azure.  Ela verifica se a imagem da VM do usuário é compatível com o Microsoft Azure, ou seja, se as diretrizes e os requisitos de preparação do VHD foram cumpridos. A saída da ferramenta é um relatório de compatibilidade, que você carregará no [Portal do Cloud Partner](https://cloudpartner.azure.com) para solicitar a certificação da VM.

1. Baixe e instale a [Ferramenta de Teste de Certificação para Certificado do Azure](https://www.microsoft.com/download/details.aspx?id=44299) mais recente. 
2. Abra a ferramenta de certificação e, em seguida, clique em **Iniciar Novo Teste**.
3. Na tela **Informações do Teste**, insira um **Nome de Teste** para a execução de teste.
4. Selecione a **Plataforma** para a VM, tanto `Windows Server` ou `Linux`. A escolha da plataforma impactará nas opções restantes.
5. Se a VM estiver usando esse serviço de banco de dados, marque a caixa de seleção **Testar para Banco de Dados SQL do Azure**.

   ![Página inicial da ferramenta de teste de certificação](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Conectar a ferramenta de certificação a uma imagem da VM

  A ferramenta conecta-se às VMs baseadas no Windows com o [PowerShell](https://docs.microsoft.com/powershell/) e conecta-se às VMs do Linux por meio de [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Conectar a ferramenta de certificação a uma imagem da VM do Linux

1. Selecione o modo **Autenticação SSH**: `Password Authentication` ou `key File Authentication`.
2. Se estiver usando a autenticação baseada em senha, insira valores para o **Nome DNS da VM**,  **Nome de usuário** e **Senha**.  Opcionalmente, é possível alterar o número da **Porta SSH** padrão.

     ![Autenticação de senha da Imagem de VM do Linux](./media/publishvm_026.png)

3. Se estiver usando a autenticação baseada em arquivo, insira valores para o **Nome DNS da VM**, **Nome de usuário** e local da **Chave Privada**.  Opcionalmente, você pode fornecer uma **frase secreta** ou alterar o número da **Porta SSH**.

     ![Autenticação de arquivo da imagem da VM do Linux](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Conectar a ferramenta de certificação a uma Imagem VM baseada em Windows**
1. Insira o **nome DNS da VM** totalmente qualificado (por exemplo, `MyVMName.Cloudapp.net`).
2. Insira os valores para o **Nome de Usuário** e **Senha**.

   ![Autenticação de senha de Imagem de VM do Windows](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Executar um teste de certificação

Após fornecer os valores de parâmetro para a imagem de VM na ferramenta de certificação, selecione **Conexão de Teste** para garantir uma conexão válida com a VM. Após verificar a conexão, selecione **Avançar** para iniciar o teste.  Quando o teste for concluído, uma tabela será exibida com os resultados do teste (Aprovado/Falha/Aviso).  O exemplo a seguir mostra os resultados do teste para um teste de VM do Linux. 

![Resultados do teste de certificação para imagem da VM do Linux](./media/publishvm_029.png)

Se algum dos testes falhar, a imagem *não* será certificada. Nesse caso, revise os requisitos e as mensagens de falha, faça as alterações indicadas e execute novamente o teste. 

Após o teste automatizado, será necessário fornecer informações adicionais sobre a imagem da VM na tela **Questionário**.  Ele contém duas guias que você deve concluir.  A guia **Avaliação Geral** contém perguntas no modo **Verdadeiro/Falso**, enquanto a **Personalização de Kernel** contém várias perguntas de seleção e de forma livre.  Preencha as perguntas nas duas guias e selecione **Avançar**.

![Questionário da Ferramenta de Certificação](./media/publishvm_030.png)

A última tela permite que você forneça informações adicionais como informações de acesso SSH para uma imagem de VM do Linux e uma explicação para quaisquer avaliações com falha, se você estiver buscando exceções. 

Por fim, clique em **Gerar Relatório** para baixar os resultados do teste e os arquivos de log dos casos de teste executados, além das respostas ao questionário. Salve os resultados no mesmo contêiner que os VHDs.

![Salvar resultados do teste de certificação](./media/publishvm_031.png)


## <a name="next-steps"></a>Próximas etapas

Em seguida, você [gerará um URI (Uniform Resource Identifier) para cada VHD](./cpp-get-sas-uri.md) enviado ao marketplace. 
