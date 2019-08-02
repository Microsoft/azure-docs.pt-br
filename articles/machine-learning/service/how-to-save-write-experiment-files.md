---
title: Evitar limitações de armazenamento e experimentar latência com diretórios de entrada e saídas
description: Neste artigo, você aprenderá onde salvar os arquivos de entrada de teste e onde gravar arquivos de saída para evitar erros de limitação de armazenamento e experiências de latência.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 1f9199b5bae0c82cd46750d8ef5522a0d3579671
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595274"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Onde salvar e gravar arquivos para experimentos de aprendizado de máquina do Azure

Neste artigo, você aprenderá onde salvar os arquivos de entrada e onde gravar arquivos de saída de seus experimentos para evitar armazenamento limitam erros e experimentar a latência.

Quando iniciar o treinamento é executado em um [destino de computação](how-to-set-up-training-targets.md), eles são isolados dos ambientes externos. O objetivo deste design é garantir que a capacidade de reprodução e portabilidade do experimento. Se você executar o mesmo script duas vezes, o mesmo ou outro destino de computação, você receberá os mesmos resultados. Com esse design, você pode tratar destinos de computação como recursos de computação sem monitoração de estado, cada um sem afinidade com os trabalhos que estão sendo executados depois que tiverem terminados com.

## <a name="where-to-save-input-files"></a>Onde salvar os arquivos de entrada

Antes de iniciar um experimento em seu computador local ou em um destino de computação, você deve garantir que os arquivos necessários estão disponíveis para o destino de computação, como arquivos de dependência e arquivos de dados do que seu código precisa ser executado.

O Azure Machine Learning executa scripts de treinamento, copiando a pasta de script inteiro para o contexto de computação de destino e, em seguida, cria um instantâneo. O limite de armazenamento para instantâneos de teste é de 300 MB e/ou arquivos de 2000.

Por esse motivo, recomendamos:

* **Armazenar os arquivos em um Azure Machine Learning [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** Isso evita problemas de latência do experimento e tem as vantagens de acesso a dados de um destino de computação remota, o que significa que a autenticação e montagem são gerenciadas pelo serviço de Azure Machine Learning. Saiba mais sobre a especificação de um repositório de dados como seu diretório de origem e carregar arquivos para seu armazenamento de dados na [acessar dados de seus repositórios de dados](how-to-access-data.md) artigo.

* **Se você só precisa de alguns arquivos de dados e scripts de dependência e não é possível usar um repositório de dados,** colocar os arquivos no mesmo diretório da pasta que o script de treinamento. Especifique essa pasta como seu `source_directory` diretamente em seu script de treinamento ou no código que chama o script de treinamento.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limites de armazenamento de instantâneos de teste

Para testes, o Azure Machine Learning faz automaticamente um instantâneo do seu código com base no diretório que sugerir quando você configura a execução do teste. Isso tem um limite total de 300 MB e/ou arquivos de 2000. Se você exceder esse limite, você verá o seguinte erro:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Para resolver esse erro, armazene seus arquivos de teste em um repositório de dados. Se você não pode usar um repositório de dados, a tabela a seguir oferece possíveis soluções alternativas.

Experimento&nbsp;descrição|Solução de limite de armazenamento
---|---
Menos de 2000 arquivos & não é possível usar um repositório de dados| Substituir o limite de tamanho do instantâneo com <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Isso pode levar vários minutos dependendo do número e tamanho dos arquivos.
Deve usar o diretório de scripts específicos| Tornar um `.amlignore` arquivo para excluir arquivos do instantâneo do teste que não fazem parte do código-fonte. Adicione os nomes de arquivo para o `.amlignore` de arquivo e coloque-o no mesmo diretório que o script de treinamento. O `.amlignore` arquivo usa o mesmo [sintaxe e padrões](https://git-scm.com/docs/gitignore) como um `.gitignore` arquivo.
Pipeline|Usar um subdiretório diferente para cada etapa
Notebooks Jupyter| Criar um `.amlignore` de arquivos ou mover o bloco de anotações em um subdiretório de novo e vazio e executar seu código novamente.

## <a name="where-to-write-files"></a>Onde gravar arquivos

Devido ao isolamento de testes de treinamento, as alterações que ocorrem durante execuções de arquivos não são necessariamente persistentes fora de seu ambiente. Se seu script modificar os arquivos locais para computação, as alterações não serão mantidas para a sua próxima execução do experimento e nem serão propagadas para o computador cliente automaticamente. Portanto, as alterações feitas durante o teste de primeira execução não e não deverão afetar aquelas no segundo.

Ao gravar alterações, é recomendável que a gravação de arquivos para um repositório de dados do Azure Machine Learning. Ver [acessar dados de seus repositórios de dados](how-to-access-data.md).

Se você não precisar de um repositório de dados, gravar arquivos para o `./outputs` e/ou `./logs` pasta.

>[!Important]
> Duas pastas *gera* e *logs*, recebem o tratamento especial pelo Azure Machine Learning. Durante o treinamento, quando você grava arquivos a serem`./outputs` e`./logs` pastas, os arquivos carregará automaticamente para o histórico de execução, para que você tenha acesso a eles quando sua execução ser concluída.

* **Para saída, como mensagens de status ou resultados de pontuação** gravar arquivos para o `./outputs` pasta, portanto, eles são persistidos como artefatos no histórico de execução. Lembre-se do número e tamanho dos arquivos gravados nessa pasta, como latência pode ocorrer quando o conteúdo é carregado para o histórico de execução. Se a latência for uma preocupação, gravar arquivos em um repositório de dados é recomendado.

* **Para salvar o arquivo escrito como logs de histórico de execuções** gravar arquivos no `./logs` pasta. Os logs forem carregados em tempo real, portanto, esse método é adequado para streaming de atualizações dinâmicas de uma execução remota.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [acessando dados em seus armazenamentos de dados](how-to-access-data.md).

* Saiba mais sobre [como definir metas de treinamento](how-to-set-up-training-targets.md).
