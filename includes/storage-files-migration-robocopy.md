---
title: Mapear uma estrutura de pastas para uma topologia Sincronização de Arquivos do Azure
description: Mapeie uma estrutura de arquivos e pastas existente para compartilhamentos de arquivos do Azure para uso com Sincronização de Arquivos do Azure. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: cde85e245c8cc6ae8c55b24270f125bacc111737
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547536"
---
```console
Robocopy /MT:16 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Comutador              | Significado |
|---------------------|---------|
| /MT                 | Permite que o RoboCopy seja executado em vários threads. O padrão é 8 e o máximo é 128. Corresponda esse valor à contagem de núcleos do processador e contagem de threads por núcleo. Considere se os núcleos precisam ser reservados para outras tarefas que um servidor de produção possa ter. |
| /NP                 | O progresso da cópia para cada arquivo e pasta não será exibido. A exibição do progresso reduz significativamente o desempenho da cópia. |
| / NFL                | Especifica que os nomes de arquivo não são registrados. Melhora o desempenho da cópia. |
| /NDL                | Especifica que os nomes de diretório não são registrados. Melhora o desempenho da cópia. |
| /B                  | Executa o RoboCopy no mesmo modo que um aplicativo de backup usaria. Ele permite que o RoboCopy mova arquivos aos quais o usuário atual não tem permissões. |
| /MIR                | *A origem do espelho para o destino* permite que o Robocopy só precise copiar deltas entre a origem e o destino. Subdiretórios vazios serão copiados. Itens (arquivos ou pastas) que foram alterados ou não existem no destino serão copiados. Os itens que existem no destino, mas não na fonte, serão limpos (excluídos) do destino. Ao usar essa opção, é imperativo que você corresponda exatamente à estrutura de pastas de origem e destino. "Correspondência" significa que você copia do nível correto de origem e de pasta para o nível de pasta correspondente no destino. Em seguida, uma cópia "atualizada" pode ser bem-sucedida. Quando a origem e o destino são incompatíveis, usar o levará `/MIR` a exclusões e cópias em grande escala. |
| /IT                 | Garante que a fidelidade seja preservada em determinados cenários de espelhamento. </br>*Exemplo* : se entre dois Robocopy for executado, um arquivo apresentará uma alteração de ACL e uma atualização de atributo: por exemplo, será marcado como *oculto*. Sem/IT, a alteração da ACL pode ser perdida pelo RoboCopy e não é transferida para o local de destino. |
|CopiarObjeto`[copyflags]`  | Fidelidade da cópia de arquivo (padrão se não especificado for `/COPY:DAT` ), sinalizadores de cópia: `D` = data, `A` = atributos, `T` = carimbos de data/hora, `S` = Security = NTFS ACLs, `O` = Information Owner, `U` = A<u>u</u>underting Information. As informações de auditoria não podem ser armazenadas em um compartilhamento de arquivos do Azure. |
| /DCOPY:`[copyflags]`| Fidelidade para a cópia de diretórios (padrão se não for especificado é `/DCOPY:DA` ), sinalizadores de cópia: `D` = data, `A` = atributos, `T` = carimbos de data/hora. |
| /UNILOG:<file name> | Gera o status do arquivo de LOG como UNICODE (Substitui o log existente). |
| /LFSM               | **somente para destinos com armazenamento em camadas** </br>Usar o/LFSM solicita o RoboCopy para operar em ' modo de pouco espaço livre '. Essa opção só é útil para destinos com armazenamento em camadas, que pode ficar sem capacidade local antes que o RoboCopy possa ser concluído. Essa opção foi adicionada especificamente para uso com um destino habilitado para camada de nuvem Sincronização de Arquivos do Azure. Ele pode ser usado independentemente do Sincronização de Arquivos do Azure. Nesse modo, o RoboCopy fará uma pausa sempre que uma cópia de arquivo fizer com que o espaço livre do volume de destino fique abaixo de um valor ' Floor '. Esse valor pode ser especificado pela `/LFSM:n` forma do sinalizador. O parâmetro `n` é especificado na base 2: `nKB` , `nMB` ou `nGB` . Se `/LFSM` for especificado sem nenhum valor de piso explícito, o andar será definido como 10 por cento do tamanho do volume de destino. O modo de pouco espaço livre é incompatível com/MT,/EFSRAW,/B e/ZB. |
| /Z                  | **uso cuidadoso** </br>Copia arquivos no modo de reinicialização, o uso é recomendado apenas em um ambiente de rede instável. Essa opção reduz significativamente o desempenho de cópia devido ao registro em log extra. |
| /ZB                 | **uso cuidadoso** </br>Usa o modo de reinicialização. Se o acesso for negado, essa opção usará o modo de backup. Essa opção reduz significativamente o desempenho de cópia devido ao ponto de verificação. |
   