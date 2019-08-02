---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171992"
---
As alterações feitas ao compartilhamento de Arquivos do Azure usando o Portal do Azure ou SMB não são detectadas e replicadas imediatamente como as alterações no Ponto de extremidade do servidor são. Os Arquivos do Azure ainda não têm notificações/diário de alteração, portanto, não há nenhuma maneira de iniciar automaticamente uma sessão de sincronização quando os arquivos são alterados. No Windows Server, a sincronização de Arquivos do Azure usa o [diário de USN do Windows](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) para iniciar automaticamente uma sessão de sincronização quando os arquivos são alterados.<br /><br /> Para detectar alterações para o compartilhamento de arquivos do Azure, a sincronização de arquivos do Azure tem um trabalho agendado chamado um *alterar o trabalho de detecção*. Um trabalho de detecção de alteração enumera todos os arquivos no compartilhamento de arquivos e, em seguida, compara a versão de sincronização para esse arquivo. Quando o trabalho de detecção de alteração determina que os arquivos foram alterados, a sincronização de Arquivos do Azure inicia uma sessão de sincronização. O trabalho de detecção de alteração é iniciado a cada 24 horas. Como o trabalho de detecção de alteração funciona enumerando todos os arquivos no compartilhamento de Arquivos do Azure, a detecção de troca demora mais nos namespaces grandes do que namespaces menores. Para esses namespaces, pode levar mais de uma vez a cada 24 horas para determinar quais arquivos foram alterados.<br /><br />
Observe que as alterações feitas em um compartilhamento de arquivos do Azure usando REST não atualizam a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização. <br /><br />
Estamos explorando adicionar detecção de alteração para um compartilhamento de arquivos do Azure semelhante ao USN para volumes no Windows Server. Ajude-nos a priorizar esse recurso para desenvolvimento futuro votando no [UserVoice de Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
