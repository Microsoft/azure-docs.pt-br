---
title: Experimentação - Serviço de Decisão Personalizada
titlesuffix: Azure Cognitive Services
description: Este artigo é um guia para experimentação com Serviço de Decisão Personalizada.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b5f8c853218a1db53f4dd23e7254b35990a7132b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829167"
---
# <a name="experimentation"></a>Experimentação

Seguindo a teoria de [CB (bandidos contextuais)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), o Serviço de Decisão Personalizada observa repetidamente um contexto, executa uma ação e observa uma recompensa para a ação escolhida. Um exemplo é a personalização do conteúdo: o contexto descreve um usuário, as ações são histórias candidatas e a recompensa mede o quanto o usuário gostou da história recomendada.

O Serviço de Decisão Personalizada produz uma política, pois mapeia de contextos para ações. Com uma política de destino específica, você quer saber qual é a recompensa esperada. Uma maneira de estimar a recompensa é usar uma política online e permitir que ele escolha ações (por exemplo, histórias recomendadas aos usuários). No entanto, essa avaliação online pode ser cara por dois motivos:

* Ela expõe os usuários a uma política experimental não testada.
* Ela não pode ser dimensionada para avaliar várias políticas de destino.

A avaliação fora da política é um paradigma alternativo. Se você tiver logs de um sistema online existente que execute uma política de registro em log, a avaliação fora da política poderá estimar as recompensas esperadas das novas políticas de destino.

Usando o arquivo de log, a Experimentação busca encontrar a política com a maior recompensa esperada estimada. Políticas de destino são parametrizadas por argumentos [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki). No modo padrão, o script testa uma variedade de argumentos Vowpal Wabbit acrescentando a `--base_command`. O script executa as ações a seguir:

* A detecção automática apresenta namespaces das primeiras linhas `--auto_lines` do arquivo de entrada.
* Executa uma primeira limpeza em hiperparâmetros (`learning rate`, `L1 regularization` e `power_t`).
* Testa a avaliação da política `--cb_type` (pontuação de propensão inversa (`ips`) ou duplamente robusta (`dr`). Para obter mais informações, veja [Exemplo de bandido contextual](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Resta marginais.
* Testa recursos de interação quadrática:
   * **fase de força bruta**: Testa todas as combinações com pares `--q_bruteforce_terms` ou menos.
   * **fase Greedy**: Adiciona o melhor par até que não haja melhorias para rodadas de `--q_greedy_stop`.
* Executa uma segunda limpeza em hiperparâmetros (`learning rate`, `L1 regularization` e `power_t`).

Os parâmetros que controlam essas etapas incluem alguns argumentos Vowpal Wabbit:
- Opções de Manipulação de exemplo:
  - namespaces compartilhados
  - namespaces de ação
  - namespaces marginais
  - recursos quadráticos
- Opções de Regra de Atualização
  - taxa de aprendizagem
  - Regularização de L1
  - valor de potência t

Para uma explicação detalhada sobre os argumentos acima, consulte [Argumentos de linha de comando Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Pré-requisitos
- Vowpal Wabbit: Instalado e no caminho.
  - Windows: [Usar o instalador `.msi`](https://github.com/eisber/vowpal_wabbit/releases).
  - Outras plataformas: [Obter o código-fonte](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Instalado e no caminho.
- NumPy: Use o gerenciador de pacotes de sua escolha.
- O repositório *Microsoft/mwt-ds*: [Clonar o repositório](https://github.com/Microsoft/mwt-ds).
- Arquivo de log JSON do Serviço de Decisão: Por padrão, o comando base inclui `--dsjson`, que habilita a análise de JSON do Serviço de Decisão do arquivo de dados de entrada. [Obtenha um exemplo desse formato](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Uso
Vá para `mwt-ds/DataScience` e execute `Experimentation.py` com os argumentos relevantes, conforme detalhado no código a seguir:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Um log dos resultados é anexado ao arquivo *mwt-ds/DataScience/experiments.csv*.

### <a name="parameters"></a>parâmetros
| Entrada | DESCRIÇÃO | Padrão |
| --- | --- | --- |
| `-h`, `--help` | Mostrar mensagem de ajuda e sair. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Caminho do arquivo de dados (formato `.json` ou `.json.gz` – cada linha é um `dsjson`). | Obrigatório |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Comando Vowpal Wabbit de base.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Número de processos paralelos a usar. | Processadores lógicos |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Namespaces de recurso compartilhado (por exemplo, `abc` significa namespaces `a`, `b` e `c`).  | Detecção automática do arquivo de dados |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Namespaces do recurso de ação. | Detecção automática do arquivo de dados |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Namespaces do recurso marginal. | Detecção automática do arquivo de dados |  
| `--auto_lines AUTO_LINES` | Número de linhas do arquivo de dados a verificar para a detecção automática de namespaces de recursos. | `100` |  
| `--only_hp` | Limpe somente hiperparâmetros (`learning rate`, `L1 regularization` e `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Intervalo de taxa de aprendizagem como valores positivos `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Intervalo de regularização L1 como valores positivos `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Intervalo de Power_t como valores positivos `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Número de pares quadráticos para testar na fase de força bruta. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Rodadas sem melhorias, após o que a fase de pesquisa gananciosa quadrática é interrompida. | `3` |  

### <a name="examples"></a>Exemplos
Para usar os valores padrão predefinidos:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

De modo equivalente, o Vowpal Wabbit também pode ingerir arquivos `.json.gz`:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Para limpar somente hiperparâmetros (`learning rate`, `L1 regularization` e `power_t`, parando após a etapa 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
