---
title: "Introdução aos Arquivos do Azure | Microsoft Docs"
description: "Uma visão geral dos Arquivos do Azure, um serviço que permite criar e usar compartilhamentos de arquivos de rede na nuvem usando o protocolo SMB padrão do setor."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/08/2017
ms.author: renash
ms.openlocfilehash: 81de67334f66a61582bb2c5d5a2303cf3a1a086c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-files"></a>Introdução aos Arquivos do Azure
Os Arquivos do Azure oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do protocolo [SMB (Service Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) padrão do setor (também conhecido como CIFS ou Common Internet File System). Os compartilhamentos de Arquivos do Azure podem ser montados de maneira simultânea por implantações locais ou na nuvem do Windows, do Linux e do MacOS. Além disso, compartilhamentos dos Arquivos do Azure pode ser armazenado em cache nos Windows Servers com a Sincronização de Arquivos do Azure (versão prévia) para acesso rápido perto de onde os dados estão sendo usados.

## <a name="videos"></a>Vídeos
| Introdução à Sincronização de Arquivos do Azure (2 m) | Arquivos do Azure com Sincronização (Ignite 2017) (85 m)  |
|-|-|
| [![Screencast do vídeo Introdução à Sincronização de Arquivos do Azure - clique para reproduzir!](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Screencast da apresentação Arquivos do Azure com Sincronização - clique para reproduzir!](./media/storage-files-introduction/azure-files-ignite-2017-video.png)](https://www.youtube.com/watch?v=r26jWDGF_rg) |

## <a name="why-azure-files-is-useful"></a>Por que o serviço Arquivos do Azure é útil
Os compartilhamentos de Arquivos do Azure podem ser usados para:

* **Substituir ou complementar os servidores de arquivos locais**:  
    Os Arquivos do Azure podem ser usados para substituir completamente ou complementar os servidores de arquivos tradicionais no local ou dispositivos NAS. Sistemas operacionais conhecidos, como o Linux, Windows e macOS podem montar diretamente compartilhamentos de Arquivos do Azure em qualquer lugar do mundo. Os compartilhamentos dos Arquivos do Azure também podem ser replicados com Sincronização de Arquivos do Azure para Windows Servers, no local ou na nuvem, para armazenamento dos dados em cache distribuído e com desempenho onde estão sendo usados.

* **Aplicativos de "Deslocamento e comparação"**:  
    O serviço Arquivos do Azure facilita "comparar e deslocar" aplicativos para a nuvem que espera que o compartilhamento de arquivos armazene o aplicativo de arquivo ou os dados do usuário. O serviço Arquivos do Azure permite o cenário “clássico” de comparar e deslocar, em que o aplicativo e os dados são movidos para o Azure, e o cenário “híbrido”, em que os dados do aplicativo são movidos para o serviço Arquivos do Azure e o aplicativo continua a ser executado no local. 

* **Simplificar o Desenvolvimento na Nuvem**:  
    Os Arquivos do Azure também podem ser usados de várias maneiras para simplificar novos projetos de desenvolvimento na nuvem. Por exemplo:
    * **Configurações de Aplicativo Compartilhado**:  
        Um padrão comum para aplicativos distribuídos é fazer com que os arquivos de configuração fiquem em um local centralizado, onde possam ser acessados de diversas instâncias de aplicativos. As instâncias do aplicativo podem carregar sua configuração por meio da API REST de Arquivo e os usuários podem acessá-las conforme necessário com a montagem local do compartilhamento SMB.

    * **Compartilhamento de Diagnóstico**:  
        Um compartilhamento dos Arquivos do Azure é um local conveniente para aplicativos em nuvem gravarem seus logs, métricas e despejos de memória. Os logs podem ser gravados pelas instâncias do aplicativo por meio da API REST de Arquivo e os desenvolvedores podem acessá-los ao montar o compartilhamento de arquivos em seu computador local. Isso permite maior flexibilidade, já que os desenvolvedores podem adotar o desenvolvimento em nuvem sem a necessidade de abandonar as ferramentas existentes que conhecemos e amamos.

    * **Desenv/Teste/Depuração**:  
        Quando desenvolvedores ou administradores estão trabalhando em máquinas virtuais na nuvem, eles frequentemente precisam de um conjunto de ferramentas ou utilitários. Copiar tais ferramentas e utilitários em cada VM pode ser uma atividade demorada. Ao montar um compartilhamento dos Arquivos do Azure localmente nas máquinas virtuais, o desenvolvedor e o administrador podem acessar rapidamente suas ferramentas e utilitários sem precisar copiá-las.

## <a name="key-benefits"></a>Principais benefícios
* **Acesso compartilhado**. Os compartilhamentos de Arquivos do Azure suportam o protocolo SMB padrão, ou seja, você pode substituir perfeitamente seus compartilhamentos de arquivos locais pelos compartilhamentos de Arquivos do Azure sem se preocupar com a compatibilidade dos aplicativos. Poder compartilhar um sistema de arquivos em vários computadores, aplicativos e instâncias é uma vantagem significativa com os Arquivos do Azure para aplicativos que precisam de compartilhamento. 
* **Totalmente Gerenciado**. Os compartilhamentos de Arquivos do Azure podem ser criados sem a necessidade de gerenciar um sistema operacional ou hardware. Isso significa que você não precisa lidar com a correção do sistema operacional do servidor com atualizações críticas de segurança ou com a substituição de discos rígidos com defeito.
* **Scripts e Ferramentas**. Os cmdlets do PowerShell e a CLI do Azure podem ser usados para criar, montar e gerenciar compartilhamentos dos Arquivos do Azure como parte da administração dos aplicativos do Azure. Você pode criar e gerenciar compartilhamentos de Arquivos do Azure usando o Portal do Azure e o Gerenciador de Armazenamento do Azure. 
* **Resiliência**. O serviço Arquivos do Azure foi criado do zero para estar sempre disponível. Substituir os compartilhamentos de arquivos locais pelos Arquivos do Azure significa que não é preciso estar ativado para lidar com interrupções locais de energia ou problemas de rede. 
* **Programação Familiar**. Os aplicativos executados no Azure podem acessar dados no compartilhamento por meio de [APIs de E/S do sistema](https://msdn.microsoft.com/library/system.io.file.aspx) de arquivos. Os desenvolvedores podem, portanto, utilizar seus códigos e habilidades existentes para migrar aplicativos existentes. Além das APIs de E/S do sistema, você pode usar as [Bibliotecas do Cliente de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn261237.aspx) ou a [API de REST do Armazenamento do Azure](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Próximas etapas
* [Criar um Compartilhamento de Arquivos do Azure](storage-how-to-create-file-share.md)
* [Conexão e Montagem no Windows](storage-how-to-use-files-windows.md)
* [Conexão e Montagem no Linux](storage-how-to-use-files-linux.md)
* [Conexão e Montagem no MacOS](storage-how-to-use-files-mac.md)