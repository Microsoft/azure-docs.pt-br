---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "66244584"
---
| Entidade                                       | Convenções   |
|----------------------------------------------|-----------------------------------------------------|
| Nomes de contêiner de blob de blocos e blob de páginas | Precisa ser um nome DNS válido com tamanho de 3 a 63 caracteres. <br>  Deve começar com uma letra ou número. <br> Pode conter apenas letras minúsculas, números e o hífen (-). <br> Cada hífen (-) precisa ser imediatamente precedido e seguido por uma letra ou um número. <br> Hifens consecutivos não são permitidos em nomes. |
| Nomes de compartilhamento de arquivos do Azure                  | O mesmo que o descrito acima                                          |
| Nomes de arquivo e diretório para arquivos do Azure     |<li> Uso de maiúsculas e minúsculas preservado, sem diferenciação. Não deve exceder 255 caracteres. </li><li> Não pode terminar com barra (/). </li><li>Se fornecido, será removido automaticamente. </li><li> Os seguintes caracteres não são permitidos: <code>" \\ / : \| < > * ?</code></li><li> Os caracteres reservados de URL precisam ser escapados corretamente. </li><li> Caracteres de caminho de URL ilegais não são permitidos. Pontos de código como \\ uE000 não são caracteres Unicode válidos. Alguns caracteres ASCII ou Unicode, como caracteres de controle (0x00 a 0x1F, \\ u0081, etc.), também não são permitidos. Para saber quais são as regras que regem as cadeias de caracteres Unicode em HTTP/1.1, confira o RFC 2616, Section 2.2: Basic Rules (RFC 2616, Seção 2.2: regras básicas) e o RFC 3987. </li><li> Os nomes de arquivo a seguir não são permitidos: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, caractere de ponto (.) e dois caracteres de ponto (..).</li>|
| Nomes de blob para blobs de blocos e blobs de página      | </li><li>Nomes de blob diferenciam maiúsculas de minúsculas e podem conter qualquer combinação de caracteres. </li><li>O nome do blob deve ter entre 1 e 1.024 caracteres. </li><li>Os caracteres reservados de URL precisam ser escapados corretamente. </li><li>O número de segmentos de caminho que incluem o nome do blob não pode exceder 254. Um segmento de caminho é a cadeia de caracteres entre caracteres delimitadores consecutivos (por exemplo, a barra '/') que correspondem ao nome de um diretório virtual.</li> |
