---
title: Copiar aplicativos e dados para nós de pool
description: Saiba como copiar aplicativos e dados para nós de pool.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 0109171fd78dc11058daa30bf4604bebc1eeb857
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703640"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Copiar aplicativos e dados para nós de pool

O lote do Azure dá suporte a várias maneiras de obter dados e aplicativos em nós de computação para que estejam disponíveis para uso por tarefas.

O método escolhido pode depender do escopo do seu arquivo ou aplicativo. Os dados e aplicativos podem ser necessários para executar todo o trabalho e, portanto, precisam ser instalados em todos os nós. Alguns arquivos ou aplicativos podem ser necessários apenas para uma tarefa específica. Talvez outras pessoas precisem ser instaladas para o trabalho, mas não precisam estar em todos os nós. O Lote tem ferramentas para cada um desses cenários.

## <a name="determine-the-scope-required-of-a-file"></a>Determinar o escopo necessário de um arquivo

Você precisa determinar o escopo de um arquivo: se é o arquivo necessário para um pool, um trabalho ou uma tarefa. Os arquivos que têm o escopo para o pool devem usar pacotes de aplicativos de pool ou uma tarefa de inicialização. Os arquivos com escopo para o trabalho devem usar uma tarefa de preparação de trabalho. Um bom exemplo de arquivos com escopo no nível do pool ou do trabalho são os aplicativos. Os arquivos com escopo para a tarefa devem usar arquivos de recurso de tarefa.

## <a name="pool-start-task-resource-files"></a>Arquivos de recurso de tarefa de início do pool

Para aplicativos ou dados que precisam ser instalados em cada nó no pool, use o pool iniciar arquivos de recursos de tarefa. Use esse método junto com um [pacote de aplicativos](batch-application-packages.md) ou a coleção de arquivos de recursos da tarefa inicial para executar um comando de instalação.  

Por exemplo, você pode usar a linha de comando Iniciar tarefa para mover ou instalar aplicativos. Você também pode especificar uma lista de arquivos ou contêineres em uma conta de armazenamento do Azure. Para obter mais informações, consulte [Adicionar # ResourceFile na documentação REST](/rest/api/batchservice/pool/add#resourcefile).

Se cada trabalho executado no pool executar um aplicativo (. exe) que deve primeiro ser instalado com um arquivo. msi, você precisará definir a propriedade Wait da tarefa de início para **êxito** como **true**. Para obter mais informações, consulte [Adicionar # StartTask na documentação REST](/rest/api/batchservice/pool/add#starttask).

## <a name="application-package-references"></a>Referências do pacote de aplicativos

Para aplicativos ou dados que precisam ser instalados em cada nó no pool, considere o uso de [pacotes de aplicativos](batch-application-packages.md). Não há nenhum comando de instalação associado a um pacote de aplicativos, mas você pode usar uma tarefa de inicialização para executar qualquer comando de instalação. Se o seu aplicativo não requer instalação ou consiste em um grande número de arquivos, você pode usar esse método.

Os pacotes de aplicativos são úteis quando você tem um grande número de arquivos, porque eles podem combinar muitas referências de arquivo em uma carga pequena. Se você tentar incluir mais de 100 arquivos de recurso separados em uma tarefa, o serviço de Lote poderá enfrentar limitações internas do sistema para uma tarefa individual. Os pacotes de aplicativos também são úteis quando você tem muitas versões diferentes do mesmo aplicativo e precisa escolher entre eles.

## <a name="job-preparation-task-resource-files"></a>Arquivos de recurso de tarefa de preparação de trabalho

Para aplicativos ou dados que devem ser instalados para que o trabalho seja executado, mas não precisa ser instalado em todo o pool, considere o uso de [arquivos de recurso de tarefa de preparação de trabalho](./batch-job-prep-release.md).

Por exemplo, se o pool tiver muitos tipos diferentes de trabalhos, e apenas um tipo de trabalho precisar de um arquivo. msi para ser executado, faz sentido colocar a etapa de instalação em uma tarefa de preparação de trabalho.

## <a name="task-resource-files"></a>Arquivos de recursos de tarefas

Os arquivos de recurso de tarefa são apropriados quando seu aplicativo ou dados são relevantes apenas para uma tarefa individual.

Por exemplo, você pode ter cinco tarefas, cada uma processando um arquivo diferente e, em seguida, gravando a saída no armazenamento de blob nesse caso, o arquivo de entrada deve ser especificado na coleção de arquivos de recursos de tarefa, pois cada tarefa tem seu próprio arquivo de entrada.

## <a name="additional-ways-to-get-data-onto-nodes"></a>Outras maneiras de obter dados em nós

Como você tem controle sobre os nós do lote do Azure e pode executar executáveis personalizados, você pode extrair dados de qualquer número de fontes personalizadas. Verifique se o nó do lote tem conectividade com o destino e se você tem credenciais para essa fonte no nó.

Alguns exemplos de maneiras de transferir dados para nós do lote são:

- Baixar dados do SQL
- Baixar dados de outros serviços Web/locais personalizados
- Mapear um compartilhamento de rede

## <a name="azure-storage"></a>Armazenamento do Azure

Tenha em mente que o armazenamento de BLOBs tem destinos de escalabilidade de download. Os destinos de escalabilidade do compartilhamento de arquivo do Armazenamento do Azure são os mesmos que os de um único blob. O tamanho afetará o número de nós e pools necessários.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como usar [pacotes de aplicativos com o lote](batch-application-packages.md).
- Saiba mais sobre como [trabalhar com nós e pools](nodes-and-pools.md).
