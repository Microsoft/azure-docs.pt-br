---
title: Conectar-se ao servidor SFTP com SSH - aplicativos lógicos do Azure
description: Automatize tarefas que monitoram, criam, gerenciam, enviam e recebem arquivos para um servidor SFTP usando o SSH e os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 7479be6a14c7d1ace5d60defad0eda51d2aa814b
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296559"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitore, crie e gerencie arquivos SFTP usando SSH e os Aplicativos Lógicos do Azure

Para automatizar tarefas que monitoram, criam, enviam e recebem arquivos em um servidor [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) usando o protocolo [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/), você pode criar e automatizar fluxos de trabalho de integração usando os Aplicativos Lógicos do Azure e o conector SFTP-SSH. O SFTP é um protocolo de rede que fornece acesso a arquivos, transferência de arquivos e gerenciamento de arquivos em qualquer fluxo de dados confiável. Aqui estão algumas tarefas de exemplo que você pode automatizar:

* Monitorar quando arquivos são adicionados ou alterados.
* Obter, criar, copiar, renomear, atualizar, listar e excluir arquivos.
* Crie pastas.
* Obter conteúdo e metadados do arquivo.
* Extrair o arquivo para pastas.

Você pode usar gatilhos que monitoram eventos em seu servidor SFTP e disponibilizam a saída para outras ações. Você pode usar ações que executam várias tarefas em seu servidor SFTP. Você também pode ter outras ações em seu aplicativo lógico usando a saída das ações do SFTP. Por exemplo, se você recuperar regularmente arquivos do servidor SFTP, poderá enviar alertas por email sobre esses arquivos e seu conteúdo usando o conector do Office 365 Outlook ou o conector Outlook.com. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

As diferenças entre o conector SFTP SSH e o conector SFTP, examine os [comparar SFTP SSH versus SFTP](#comparison) seção mais adiante neste tópico.

## <a name="limits"></a>limites

* Por padrão, as ações de SFTP SSH podem ler ou gravar arquivos que são *1 GB ou menor* , mas somente no *15 MB* partes por vez. Para lidar com arquivos maiores do que 15 MB, suporte a ações SFTP-SSH [mensagem agrupamento](../logic-apps/logic-apps-handle-large-messages.md), exceto para a ação de cópia de arquivo, que pode manipular apenas os arquivos 15 MB. O **obter conteúdo do arquivo** ação usa implicitamente o agrupamento de mensagem. 

* Gatilhos de SFTP SSH não dão suporte a agrupamento. Ao solicitar o conteúdo do arquivo, disparadores selecione somente os arquivos que são 15 MB ou menor. Para obter os arquivos mais de 15 MB, siga esse padrão em vez disso:

  * Usar um SFTP SSH gatilho que retorna propriedades de arquivo, como **quando um arquivo é adicionado ou modificado (somente propriedades)** .

  * Siga o gatilho com o SFTP-SSH **obter conteúdo do arquivo** ação, que lê o arquivo completo e usa implicitamente o agrupamento de mensagem.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Comparar SFTP-SSH vs SFTP

Aqui estão outras diferenças importantes entre o conector SFTP-SSH e o conector SFTP, onde o conector SFTP-SSH tem esses recursos:

* Usa o [biblioteca SSH.NET](https://github.com/sshnet/SSH.NET), que é uma biblioteca de SSH (Secure Shell) do código-fonte aberto que dá suporte ao .NET.

  > [!NOTE]
  >
  > O conector SFTP-SSH suporta *somente* essas chaves privadas, formatos, algoritmos e impressões digitais:
  >
  > * **Formatos de chave privada**: chaves RSA (Rivest Shamir Adleman) e DSA (Algoritmo de Assinatura Digital) nos formatos OpenSSH e ssh.com
  > * **Algoritmos de criptografia**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC e AES-256-CBC
  > * **Impressão digital**: MD5

* Por padrão, as ações de SFTP SSH podem ler ou gravar arquivos que são *1 GB ou menor* , mas somente no *15 MB* partes por vez. Para lidar com arquivos maiores do que 15 MB, ações de SFTP SSH podem usar [agrupamento de mensagem](../logic-apps/logic-apps-handle-large-messages.md). No entanto, a ação de cópia de arquivo dá suporte apenas 15 MB arquivos porque essa ação não dá suporte ao agrupamento de mensagem. Gatilhos de SFTP SSH não dão suporte a agrupamento.

* Fornece a ação **Criar pasta**, que cria uma pasta no caminho especificado no servidor SFTP.

* Fornece a ação **Renomear arquivo**, que renomeia um arquivo no servidor SFTP.

* Armazena em cache a conexão com o servidor SFTP *por até 1 hora*, o que melhora o desempenho e reduz o número de tentativas de conexão com o servidor. Para definir a duração desse comportamento de armazenamento em cache, edite a propriedade [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) na configuração do SSH em seu servidor SFTP.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Seu endereço de servidor SFTP e credenciais de conta, que permitem que seu aplicativo lógico acesse sua conta SFTP. Você também precisa acessar uma chave privada SSH e a senha da chave privada SSH.

  > [!IMPORTANT]
  >
  > O conector SFTP-SSH suporta *somente* estes formatos de chave privada, algoritmos e impressões digitais:
  >
  > * **Formatos de chave privada**: chaves RSA (Rivest Shamir Adleman) e DSA (Algoritmo de Assinatura Digital) nos formatos OpenSSH e ssh.com
  > * **Algoritmos de criptografia**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC e AES-256-CBC
  > * **Impressão digital**: MD5
  >
  > Ao criar seu aplicativo lógico, depois de adicionar o acionador ou a ação SFTP-SSH desejada, você precisará fornecer informações de conexão para o servidor SFTP. 
  > Se você estiver usando uma chave privada SSH, certifique-se de ***copiar*** a chave do arquivo de chave privada SSH e ***colar*** essa chave nos detalhes da conexão, ***não insira ou edite manualmente a chave***, o que pode causar falha na conexão. 
  > Para mais informações, consulte as etapas posteriores neste artigo.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta SFTP. Para começar com um acionador SFTP-SSH, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação SFTP-SSH, inicie seu aplicativo lógico com outro acionador, por exemplo, o acionador **Recorrência**.

## <a name="how-sftp-ssh-triggers-work"></a>Como funcionam os gatilhos de SFTP SSH

Gatilhos de SFTP SSH funcionam no sistema de arquivos SFTP de sondagem e procurando por qualquer arquivo que foi alterado desde a última sondagem. Algumas ferramentas permitem preservar o registro de data e hora quando os arquivos são alterados. Nesses casos, você precisa desativar esse recurso para que seu gatilho funcione. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| WinSCP | Vá para **opções** > **preferências** > **transferir** > **editar**  >  **Preservar o carimbo de hora** > **desabilitar** |
| FileZilla | Ir para **Transferir** > **Preservar registros de data e hora de arquivos transferidos** > **Desativar** |
|||

Quando um gatilho encontra um novo arquivo, o gatilho verifica se ele está concluído e não gravado parcialmente. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo gravado parcialmente, o gatilho observa o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna o arquivo imediatamente. O gatilho retorna o arquivo apenas ao executar a sondagem do servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho.

## <a name="connect-to-sftp-with-ssh"></a>Conectar-se ao SFTP com SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "sftp ssh" como seu filtro. Na lista de gatilhos, selecione o gatilho desejado.

   -ou-

   Para aplicativos lógicos existentes, na última etapa em que deseja adicionar uma ação, escolha **Nova etapa**. Na caixa de pesquisa, digite "sftp ssh" como seu filtro. Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça os detalhes necessários para sua conexão.

   > [!IMPORTANT]
   >
   > Ao inserir sua chave privada SSH na propriedade **chave privada SSH**, siga estas etapas adicionais, que ajudam a fornecer o valor completo e correto para essa propriedade. 
   > Uma chave inválida faz com que a conexão falhe.

   Embora você possa usar qualquer editor de texto, aqui estão etapas de exemplo que mostram como copiar e colar corretamente a chave usando o Notepad.exe como exemplo.

   1. Abra seu arquivo de chave privada SSH em um editor de texto. Essas etapas usam o bloco de notas do exemplo.

   1. Sobre o bloco de notas **edite** menu, selecione **Selecionar tudo**.

   1. Selecione **Editar** > **Copiar**.

   1. No acionador ou ação SFTP-SSH que você adicionou, cole a chave *completa* que você copiou na propriedade **chave privada SSH**, que suporta várias linhas.  ***Certifique-se de colar*** a chave. ***Não insira ou edite manualmente a chave***.

1. Quando terminar de inserir os detalhes da conexão, escolha **Criar**.

1. Agora, forneça os detalhes necessários para o acionador ou a ação selecionada e continue criando o fluxo de trabalho do seu aplicativo lógico.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>Gatilho SFTP – SSH: Quando um arquivo é adicionado ou modificado

Esse acionador inicia um fluxo de trabalho de aplicativo lógico quando um arquivo é adicionado ou alterado em um servidor SFTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e obtém o conteúdo com base em se o conteúdo atende a uma condição especificada. Em seguida, você pode adicionar uma ação que obtém o conteúdo do arquivo e coloca esse conteúdo em uma pasta no servidor SFTP.

**Exemplo corporativo**: você pode usar esse gatilho para monitorar uma pasta SFTP para novos arquivos que representam pedidos de clientes. Em seguida, você pode usar uma ação de SFTP, como **Obter conteúdo de arquivo**, para obter o conteúdo do pedido para processamento posterior e armazenar esse pedido em um banco de dados de pedidos.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>Ação SFTP – SSH: Obter o conteúdo usando o caminho

Esta ação obtém o conteúdo de um arquivo em um servidor SFTP. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do arquivo deve atender. Se a condição for verdadeira, a ação que obtém o conteúdo poderá ser executada.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/sftpconnector/) do conector.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
