---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 9b8812b1fca6a72a69f06a6c0278da8ee4d4c852
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841377"
---
O agente de Sincronização de arquivos do Azure é atualizado regularmente para adicionar novos recursos e resolver problemas. Recomendamos que você configure o Microsoft Update para obter atualizações para o agente de Sincronização de arquivos do Azure à medida que elas ficarem disponíveis.

#### <a name="major-vs-minor-agent-versions"></a>Versões do agente principal versus secundário
* As versões do agente principal geralmente contêm novos recursos e têm um número crescente como a primeira parte do número da versão. Por exemplo: \*2.\*.\*\*
* As versões do agente secundário também são chamadas de "patches" e são lançadas com mais frequência do que as versões do principal. Geralmente contêm correções de bug e pequenas melhorias, mas sem novos recursos. Por exemplo: \*\*.3.\*\*

#### <a name="upgrade-paths"></a>Caminhos de atualização
Há quatro maneiras aprovadas e testadas para instalar as atualizações do agente de Sincronização de Arquivos do Azure. 
1. **(Preferencial) Configure o Microsoft Update para baixar e instalar automaticamente as atualizações do agente.**  
    É sempre recomendável executar todas as atualizações de Sincronização de arquivos do Azure para garantir que você tenha acesso às últimas correções para o Server Agent. O Microsoft Update simplifica esse processo, baixando e instalando atualizações automaticamente para você.
2. **Use AfsUpdater.exe para baixar e instalar atualizações de agente.**  
    O AfsUpdater.exe está localizado no diretório de instalação do agente. Clique duas vezes no executável para baixar e instalar atualizações de agente. 
3. **Corrigir um agente existente de Sincronização de arquivos do Azure utilizando um arquivo de patch do Microsoft Update ou um executável .msp. O último pacote de atualização de Sincronização de arquivos do Azure pode ser baixado no [Catálogo do Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Executar um executável .msp atualizará a instalação de Sincronização de arquivos do Azure com o mesmo método usado automaticamente pelo Microsoft Update no caminho de atualização anterior. A aplicação de um patch do Microsoft Update realizará uma atualização no local de uma instalação e Sincronização de arquivos do Azure.
4. **Baixe o instalador mais recente do agente de sincronização de arquivos do Azure da [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Para fazer upgrade de uma instalação existente do agente de Sincronização de Arquivos do Azure, desinstale a versão mais antiga e então instale a última versão do instalador baixado. O registro do servidor, os grupos de sincronização e outras configurações são mantidos pelo instalador de Sincronização de arquivos do Azure.

#### <a name="automatic-agent-lifecycle-management"></a>Gerenciamento de ciclo de vida do agente automático
Com a versão do agente 6, a equipe de sincronização de arquivo introduziu um recurso de atualização automática do agente. Você pode selecionar qualquer um dos dois modos e especificar uma janela de manutenção em que a atualização deve ser tentada no servidor. Esse recurso é criado para ajudá-lo com o gerenciamento de ciclo de vida do agente fornecendo uma grade de proteção impedindo que seu agente de expiração ou permitindo uma sem complicações, mantenha-se a configuração atual.
1. O **configuração padrão** tentará impedir que o agente de expiração. Em 21 dias da data de expiração lançado um agente, o agente tentará atualizar automaticamente. Tentativa de atualizar uma vez por semana em 21 dias antes da expiração e na janela de manutenção selecionado será iniciada. **Essa opção não elimina a necessidade de colocar patches comuns do Microsoft Update.**
2. Opcionalmente, você pode selecionar que o agente irá atualizar automaticamente em si, assim que uma nova versão do agente se torna disponível (no momento, não é aplicável a servidores em cluster). Essa atualização ocorrerá durante a janela de manutenção selecionado e permitir que seu servidor para se beneficiar de aprimoramentos e novos recursos assim que eles se tornar disponíveis. Essa é a configuração de recomendada, preocupações que fornecerá versões do agente principal, bem como patches regulares de atualização ao seu servidor. Cada agente liberado é na qualidade de GA. Mesmo se você selecionar o atualizados automaticamente quando uma nova versão se torna a opção disponível, talvez não seja oferecida a atualização imediatamente após o lançamento. Inicialmente, novos agentes são oferecidos a um pequeno número de servidores e, em seguida, podemos expandir a oferta gradualmente. Quando flighting for concluída, o agente também ficará disponível no Microsoft Update e [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garantia de gerenciamento de alterações e ciclo de vida do agente
A sincronização de arquivos do Azure é um serviço de nuvem que continuamente introduz novos recursos e aprimoramentos. Isso significa que uma versão específica do agente de Sincronização de arquivos do Azure somente poderá ter suporte por um tempo limitado. Para facilitar a implantação, as regras a seguir garantem que você tem suficiente tempo e notificação para acomodar upgrades/atualizações de agente em seu processo de gerenciamento de alterações:

- As versões do agente principal terão suporte por pelo menos seis meses, a partir da data da versão inicial.
- Garantimos que há uma sobreposição de pelo menos três meses entre o suporte das versões do agente principal. 
- Avisos para servidores registrados que utilizam um agente que expirará em breve serão emitidos pelo menos três meses antes da expiração. É possível verificar se um servidor registrado está usando uma versão mais antiga do agente na seção de servidores registrados de um Serviço de Sincronização de Armazenamento.
- O tempo de vida de uma versão do agente secundário está associado à versão principal associada. Por exemplo, quando a versão do agente 3.0 for lançada, as versões do agente 2.\* serão todas definidas para expirar conjuntamente.

> [!Note]
> A instalação de uma versão do agente com um aviso de expiração exibirá um aviso, porém com êxito. A tentativa de instalar ou conectar uma versão do agente expirada não tem suporte e será bloqueada.
