---
title: "Criptografia do Serviço de Armazenamento do Azure para dados em repouso | Microsoft Docs"
description: "Use o recurso de Criptografia do Serviço de Armazenamento do Azure para criptografar seu Armazenamento de Blobs do Azure no lado do serviço ao armazenar os dados, e descriptografá-lo ao recuperar os dados."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: tamram
ms.openlocfilehash: 772c36c8310a4bf30c62def507382fe74427e0d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Criptografia do Serviço de Armazenamento do Azure para dados em repouso
A SSE (Criptografia do Serviço de Armazenamento) do Azure para dados em repouso ajuda a proteger seus dados a fim de atender aos compromissos de conformidade e segurança da organização. Com esse recurso, o Armazenamento do Azure criptografa automaticamente seus dados antes de persistir no armazenamento e os descriptografa antes da recuperação. A criptografia, a descriptografia e o gerenciamento de chaves são totalmente transparentes para os usuários.

As seções a seguir fornecem orientações detalhadas sobre como usar os recursos de Criptografia do Serviço de Armazenamento, bem como os cenários com suporte e experiências do usuário.

## <a name="overview"></a>Visão geral
O Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que, juntos, permitem aos desenvolvedores criar aplicativos seguros. Os dados podem ser protegidos em trânsito, entre um aplicativo e o Azure, usando a [Criptografia do lado do cliente](../storage-client-side-encryption.md), HTTPs ou SMB 3.0. A Criptografia do Serviço de Armazenamento fornece criptografia em repouso, lidando com a criptografia, a descriptografia e o gerenciamento de chaves de forma totalmente transparente. Todos os dados são criptografados usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, um dos codificadores de blocos mais potentes.

A SSE criptografa dados quando eles são gravados no Armazenamento do Azure e pode ser usada para o Armazenamento de Blobs do Azure e o Armazenamento de Arquivos. Ela funciona para o seguinte:

* Armazenamento Standard: contas de armazenamento de uso geral para o armazenamento de Blobs e Arquivos e contas de armazenamento de Blobs
* Armazenamento Premium 
* Todos os níveis de redundância (LRS, ZRS, GRS e RA-GRS)
* Contas de armazenamento do Azure Resource Manager (mas não clássico) 
* Todas as regiões.

Para saber mais, veja as perguntas frequentes.

Para habilitar ou desabilitar a Criptografia do Serviço de Armazenamento para uma conta de armazenamento, faça logon no [Portal do Azure](https://portal.azure.com) e selecione uma conta de armazenamento. Na folha Configurações, procure a seção de Serviço Blob, conforme mostrado nesta captura de tela, e clique em Criptografia.

![Captura de tela do Portal mostrando a opção Criptografia](./media/storage-service-encryption/image1.png)
<br/>*Figura 1: habilitar a SSE para serviço Blob (etapa 1)*

![Captura de tela do Portal mostrando a opção Criptografia](./media/storage-service-encryption/image3.png)
<br/>*Figura 2: habilitar a SSE para serviço de arquivo (etapa 1)*

Depois de clicar na configuração da Criptografia, habilite ou desabilite a Criptografia do Serviço de Armazenamento.

![Captura de tela do Portal mostrando as propriedades da criptografia](./media/storage-service-encryption/image2.png)
<br/>*Figura 3: Habilitar a SSE para o Serviço Blob e Arquivo (etapa 2)*

## <a name="encryption-scenarios"></a>Cenários de criptografia
A Criptografia do Serviço de Armazenamento pode ser habilitada no nível da conta de armazenamento. Depois de habilitada, os clientes escolherão quais serviços serão criptografados. Ela oferece suporte aos seguintes cenários de cliente:

* Criptografia do Armazenamento de Blobs e do Armazenamento de Arquivos em contas do Resource Manager.
* Criptografia do Serviço Blob e Arquivo em contas de armazenamento clássico, depois de migradas para contas de armazenamento do Resource Manager.

A SSE tem as seguintes limitações:

* Não há suporte para a criptografia de contas de armazenamento clássicas.
* Dados existentes - A SSE criptografa apenas os dados recém-criados após a habilitação da criptografia. Se, por exemplo, você criar uma nova conta de armazenamento do Resource Manager mas não ativar a criptografia e, na sequência, carregar blobs ou VHDs arquivados nessa conta de armazenamento e então ativar a SSE, esses blobs não serão criptografados, a menos que sejam reconfigurados ou copiados.
* Suporte do Marketplace – habilite a criptografia de VMs criadas no Marketplace usando o [portal do Azure](https://portal.azure.com), o PowerShell e a CLI do Azure. Imagem base de VHD permanecerá não criptografada; no entanto, quaisquer gravações feitas após a rotação da VM serão criptografadas.
* Dados de filas e tabelas não serão criptografados.

## <a name="getting-started"></a>Introdução
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Etapa 1: [Criar uma nova conta de armazenamento](../storage-create-storage-account.md).
### <a name="step-2-enable-encryption"></a>Etapa 2: Habilitar a criptografia.
Você pode habilitar a criptografia usando o [portal do Azure](https://portal.azure.com).

> [!NOTE]
> Se você quiser habilitar ou desabilitar programaticamente a Criptografia do Serviço de Armazenamento em uma conta de armazenamento, use a [API REST do Provedor de Recursos de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx), a [Biblioteca de Cliente do Provedor de Recursos de Armazenamento do .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou a [CLI do Azure](../storage-azure-cli.md).
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>Etapa 3: Copiar dados para a conta de armazenamento
Se você habilitar a SSE para o serviço Blob, todos os blobs gravados nessa conta de armazenamento serão criptografados. Blobs que já estiverem na conta de armazenamento não serão criptografados até que sejam gravados novamente. Você pode copiar dados de uma conta de armazenamento para uma com criptografia da SSE, ou até mesmo habilitar a SSE e copiar os blobs de um contêiner para outro para garantir que os dados anteriores sejam criptografados. É possível fazer isso usando qualquer uma das ferramentas a seguir. Este é o mesmo comportamento do armazenamento de arquivos.

#### <a name="using-azcopy"></a>Como usar o AzCopy
O AzCopy é um utilitário de linha de comando do Windows projetado para copiar dados entre o Armazenamento de Blobs, de Arquivos e de Tabelas do Microsoft Azure usando comandos simples com o desempenho ideal. Você pode usá-lo para copiar seus blobs ou arquivos de uma conta de armazenamento para outra que tenha a SSE habilitada. 

Para saber mais, visite [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md).

#### <a name="using-smb"></a>Como usar o SMB
Os Arquivos do Azure oferecem compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão. Você pode montar um compartilhamento de arquivo de um cliente no local ou no Azure. Uma vez montado, use ferramentas como o Robocopy para copiar arquivos para os compartilhamentos de arquivos do Azure. Para obter mais informações, consulte [Como montar um Compartilhamento de Arquivos do Azure no Windows](../files/storage-how-to-use-files-windows.md) e [Como montar um Compartilhamento de Arquivos do Azure no Linux](../files/storage-how-to-use-files-linux.md).


#### <a name="using-the-storage-client-libraries"></a>Como usar as Bibliotecas de Cliente de Armazenamento
Você pode copiar dados de arquivo ou de blob para e do armazenamento de blobs ou entre contas de armazenamento usando nosso conjunto avançado de Bibliotecas de cliente de armazenamento, que incluem .NET, C++, Java, Android, Node.js, PHP, Python e Ruby.

Para saber mais, confira [Introdução ao armazenamento de blobs do Azure usando o .NET](../blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Como usar o Gerenciador de Armazenamento
Use um gerenciador de armazenamento para criar contas de armazenamento, carregar e baixar dados, exibir o conteúdo de blobs e navegar por diretórios. Você pode usar uma dessas opções para carregar blobs em sua conta de armazenamento com a criptografia habilitada. Com alguns gerenciadores de armazenamento, também é possível copiar dados de um armazenamento de blobs existente para um contêiner diferente na conta de armazenamento ou para uma nova conta de armazenamento com a SSE habilitada.

Para saber mais, visite [Pesquisadores de armazenamento do Azure](../storage-explorers.md).

### <a name="step-4-query-the-status-of-the-encrypted-data"></a>Etapa 4: Consultar o status dos dados criptografados
Uma versão atualizada das bibliotecas de Cliente de Armazenamento foi implantada para permitir que você consulte o estado de um objeto para determinar se ele está criptografado ou não. Disponível apenas para armazenamento de blobs. O suporte para armazenamento de arquivos está previsto. 

Enquanto isso, você pode chamar [Obter propriedades da conta](https://msdn.microsoft.com/library/azure/mt163553.aspx) para verificar se a conta de armazenamento tem a criptografia habilitada ou para exibir as propriedades da conta de armazenamento no Portal do Azure.

## <a name="encryption-and-decryption-workflow"></a>Fluxo de trabalho da criptografia e da descriptografia
Aqui está uma breve descrição de como funciona o fluxo de trabalho de criptografia/descriptografia:

* O cliente habilita a criptografia na conta de armazenamento.
* Quando o cliente grava novos dados (PUT Blob, PUT Block, PUT Page, PUT File etc.) no armazenamento de blobs ou no armazenamento de arquivos, todas as gravações são criptografadas usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis.
* Quando o cliente precisa acessar os dados (GET Blob etc.), esses dados são descriptografados automaticamente antes de retornar para o usuário.
* Se a criptografia estiver desabilitada, as novas gravações não serão criptografadas, e os dados criptografados existentes permanecerão criptografados até que sejam regravados pelo usuário. Enquanto a criptografia estiver habilitada, as gravações no Armazenamento de Arquivo ou de blobs serão criptografadas. O estado dos dados não muda com a habilitação/desabilitação da criptografia na conta de armazenamento.
* Todas as chaves de criptografia são armazenadas, criptografadas e gerenciadas pela Microsoft.

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Perguntas frequentes sobre a Criptografia do Serviço de Armazenamento de dados em repouso
**P: Tenho uma conta de armazenamento clássica. Posso habilitar o SSE nela?**

R: Não, apenas contas de armazenamento do Resource Manager dão suporte à SSE.

**P: Como posso criptografar os dados em minha conta de armazenamento clássica?**

R: Você pode criar uma nova conta de armazenamento do Resource Manager e copiar seus dados usando o [AzCopy](storage-use-azcopy.md) da conta de armazenamento clássica existente para sua nova conta de armazenamento do Resource Manager. 

Se você migrar sua conta de armazenamento clássico para uma conta de armazenamento do Resource Manager, essa operação será instantânea: ela alterará o tipo de conta, mas não afetará os dados existentes. Todos os novos dados gravados serão criptografados somente depois que a criptografia for habilitada. Para obter mais informações, consulte [Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/). Observe que há suporte para isso apenas nos serviços Blob e Arquivo.

**P: posso ter uma conta de armazenamento do Resource Manager existente. Posso habilitar o SSE nela?**

R: Sim, mas apenas dados gravados recentemente serão criptografados. Ela não criptografa dados que já existiam anteriormente. Ainda não há suporte para a visualização do armazenamento de arquivos.

**P: Eu gostaria de criptografar os dados atuais em uma conta de armazenamento do Resource Manager existente?**

R: Você pode habilitar a SSE a qualquer momento em uma conta de armazenamento do Resource Manager. No entanto, dados que já existiam não serão criptografados. Para criptografar os dados existentes, você pode copiá-los para outro nome ou outro contêiner e, depois, remover as versões não criptografadas.

**P: Estou usando o armazenamento Premium. Posso usar o SSE?**

R: Sim, o SSE recebe suporte no Armazenamento Standard e no Armazenamento Premium.  Não há suporte para o Armazenamento Premium no Serviço Arquivo.

**P: Se eu criar uma nova conta de armazenamento, habilitar o SSE e depois criar uma nova VM usando a conta de armazenamento, isso significa que minha VM está criptografada?**

R: Sim. Todos os discos criados que usam a nova conta de armazenamento serão criptografados, desde que eles sejam criados após a habilitação do SSE. Se a VM tiver sido criada usando o Azure Marketplace, a imagem base do VHD permanecerá não criptografada; no entanto, quaisquer gravações feitas após a rotação da VM serão criptografadas.

**P: Posso criar novas contas de armazenamento com o SSE habilitado usando o Azure PowerShell e a CLI do Azure?**

R: Sim.

**P: Há algum custo adicional no Armazenamento do Azure se o SSE estiver habilitado?**

R: Não há qualquer custo adicional.

**P: Quem gerencia as chaves de criptografia?**

R: As chaves são gerenciadas pela Microsoft.

**P: Posso usar minhas próprias chaves de criptografia?**

R: Estamos trabalhando no fornecimento de recursos para que os clientes tragam suas próprias chaves de criptografia.

**P: Posso revogar o acesso às chaves de criptografia?**

R: Não no momento; as chaves são totalmente gerenciadas pela Microsoft.

**P: O SSE é habilitado por padrão quando eu crio uma nova conta de armazenamento?**

R: O SSE não está habilitado por padrão. Você pode usar o portal do Azure para habilitá-lo. Você também pode habilitar esse recurso por meio de programação usando a API REST do Provedor de Recursos de Armazenamento.

**P: Qual é a diferença para o Azure Disk Encryption?**

R: Esse recurso é usado para criptografar dados no Armazenamento de Blobs do Azure. A Azure Disk Encryption é usada para criptografar o sistema operacional e discos de dados em VMs de IaaS. Para obter mais detalhes, visite nosso [Guia de segurança do armazenamento](../storage-security-guide.md).

**P: E se eu habilitar o SSE e depois habilitar a Azure Disk Encryption nos discos?**

R: Isso funcionará perfeitamente. Os dados serão criptografados pelos dois métodos.

**P: Minha conta de armazenamento está configurada para ser replicada de forma geograficamente redundante. Se eu habilitar o SSE, minha cópia redundante também será criptografada?**

R: Sim, todas as cópias da conta de armazenamento são criptografadas, e todas as opções de redundância – LRS (Armazenamento com Redundância Local), ZRS (Armazenamento com Redundância de Zona), GRS (Armazenamento com Redundância Geográfica) e RA-GRS (Armazenamento com Redundância Geográfica de Acesso de Leitura) – têm suporte.

**P: Não consigo habilitar a criptografia em minha conta de armazenamento.**

R: A conta é uma conta de armazenamento do Resource Manager? Não há suporte para contas de armazenamento clássicas. 

**P: A SSE é permitida somente em regiões específicas?**

R: A SSE está disponível em todas as regiões para o armazenamento de blobs. Verifique a seção de disponibilidade para o armazenamento de arquivos. 

**P: Como devo contatar alguém se eu tiver problemas ou quiser enviar comentários?**

R: Entre em contato com [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) para falar sobre problemas relacionados à Criptografia do Serviço de Armazenamento.

## <a name="next-steps"></a>Próximas etapas
O Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que, juntos, permitem aos desenvolvedores criar aplicativos seguros. Para obter mais detalhes, visite o [Guia de segurança do armazenamento](../storage-security-guide.md).

