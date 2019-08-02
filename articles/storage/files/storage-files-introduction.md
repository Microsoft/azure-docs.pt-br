---
title: Introdução aos Arquivos do Azure | Microsoft Docs
description: Uma visão geral dos Arquivos do Azure, um serviço que permite criar e usar compartilhamentos de arquivos de rede na nuvem usando o protocolo SMB padrão do setor.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7fe95a35a6b78bae66521a09d63d376ec8757479
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699685"
---
# <a name="what-is-azure-files"></a>O que são os Arquivos do Azure?
Os Arquivos do Azure oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do [protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) padrão no setor. Os compartilhamentos de arquivos do Azure podem ser montados de maneira simultânea por implantações locais ou na nuvem do Windows, do Linux e do MacOS. Além disso, os compartilhamentos de arquivos do Azure podem ser armazenados em cache nos Windows Servers com a Sincronização de Arquivos do Azure para acesso rápido perto de onde os dados estão sendo usados.

## <a name="videos"></a>vídeos
| Introdução à Sincronização de Arquivos do Azure (2 m) | Arquivos do Azure com Sincronização (Ignite 2017) (85 m)  |
|-|-|
| [![Screencast do vídeo Introdução à Sincronização de Arquivos do Azure - clique para reproduzir!](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Screencast da apresentação Arquivos do Azure com Sincronização - clique para reproduzir!](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/watch?v=GMzh2M66E9o) |

## <a name="why-azure-files-is-useful"></a>Por que o serviço Arquivos do Azure é útil
Os compartilhamentos de arquivos do Azure podem ser usados para:

* **Substituir ou complementar os servidores de arquivos locais**:  
    Os Arquivos do Azure podem ser usados para substituir completamente ou complementar os servidores de arquivos tradicionais no local ou dispositivos NAS. Sistemas operacionais conhecidos, como o Linux, Windows e macOS podem montar diretamente compartilhamentos de arquivos do Azure em qualquer lugar do mundo. Os compartilhamentos de arquivos do Azure também podem ser replicados com a Sincronização de Arquivos do Azure para Windows Servers, no local ou na nuvem, para armazenamento dos dados em cache distribuído e com desempenho onde estão sendo usados.

* **Aplicativos de "Deslocamento e comparação"** :  
    O serviço Arquivos do Azure facilita "comparar e deslocar" aplicativos para a nuvem que espera que o compartilhamento de arquivos armazene o aplicativo de arquivo ou os dados do usuário. O serviço Arquivos do Azure permite o cenário “clássico” de comparar e deslocar, em que o aplicativo e os dados são movidos para o Azure, e o cenário “híbrido”, em que os dados do aplicativo são movidos para o serviço Arquivos do Azure e o aplicativo continua a ser executado no local. 

* **Simplificar o desenvolvimento na nuvem**:  
    Os Arquivos do Azure também podem ser usados de várias maneiras para simplificar novos projetos de desenvolvimento na nuvem. Por exemplo:
    * **Configurações de aplicativo compartilhado**:  
        Um padrão comum para aplicativos distribuídos é fazer com que os arquivos de configuração fiquem em um local centralizado, onde possam ser acessados de diversas instâncias de aplicativos. As instâncias do aplicativo podem carregar sua configuração por meio da API REST de Arquivo e os usuários podem acessá-las conforme necessário com a montagem local do compartilhamento SMB.

    * **Compartilhamento de diagnóstico**:  
        Um compartilhamento de arquivos do Azure é um local conveniente para aplicativos de nuvem gravarem seus logs, métricas e despejos de memória. Os logs podem ser gravados pelas instâncias do aplicativo por meio da API REST de Arquivo e os desenvolvedores podem acessá-los ao montar o compartilhamento de arquivos em seu computador local. Isso permite maior flexibilidade, já que os desenvolvedores podem adotar o desenvolvimento em nuvem sem a necessidade de abandonar as ferramentas existentes que conhecemos e amamos.

    * **Desenv/Teste/Depuração**:  
        Quando desenvolvedores ou administradores estão trabalhando em máquinas virtuais na nuvem, eles frequentemente precisam de um conjunto de ferramentas ou utilitários. Copiar tais ferramentas e utilitários em cada VM pode ser uma atividade demorada. Ao montar um compartilhamento de arquivos do Azure localmente nas máquinas virtuais, o desenvolvedor e o administrador podem acessar rapidamente suas ferramentas e utilitários sem precisar copiá-las.

## <a name="key-benefits"></a>Principais benefícios
* **Acesso compartilhado**. Os compartilhamentos de arquivos do Azure suportam o protocolo SMB padrão, ou seja, você pode substituir perfeitamente seus compartilhamentos de arquivos locais pelos compartilhamentos de arquivos do Azure sem se preocupar com a compatibilidade dos aplicativos. Poder compartilhar um sistema de arquivos em vários computadores, aplicativos e instâncias é uma vantagem significativa com os Arquivos do Azure para aplicativos que precisam de compartilhamento. 
* **Totalmente gerenciado**. Os compartilhamentos de arquivos do Azure podem ser criados sem a necessidade de gerenciar um sistema operacional ou hardware. Isso significa que você não precisa lidar com a correção do sistema operacional do servidor com atualizações críticas de segurança ou com a substituição de discos rígidos com defeito.
* **Scripts e ferramentas**. Os cmdlets do PowerShell e a CLI do Azure podem ser usados para criar, montar e gerenciar compartilhamentos dos Arquivos do Azure como parte da administração dos aplicativos do Azure. Você pode criar e gerenciar compartilhamentos de Arquivos do Azure usando o Portal do Azure e o Gerenciador de Armazenamento do Azure. 
* **Resiliência**. O serviço Arquivos do Azure foi criado do zero para estar sempre disponível. Substituir os compartilhamentos de arquivos locais pelos Arquivos do Azure significa que não é preciso estar ativado para lidar com interrupções locais de energia ou problemas de rede. 
* **Programação familiar**. Os aplicativos executados no Azure podem acessar dados no compartilhamento por meio de [APIs de E/S do sistema](https://msdn.microsoft.com/library/system.io.file.aspx) de arquivos. Os desenvolvedores podem, portanto, utilizar seus códigos e habilidades existentes para migrar aplicativos existentes. Além das APIs de E/S do sistema, você pode usar as [Bibliotecas do Cliente de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn261237.aspx) ou a [API de REST do Armazenamento do Azure](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Próximas etapas
* [Criar um compartilhamento de arquivos do Azure](storage-how-to-create-file-share.md)
* [Conexão e montagem no Windows](storage-how-to-use-files-windows.md)
* [Conexão e montagem no Linux](storage-how-to-use-files-linux.md)
* [Conexão e montagem no MacOS](storage-how-to-use-files-mac.md)
