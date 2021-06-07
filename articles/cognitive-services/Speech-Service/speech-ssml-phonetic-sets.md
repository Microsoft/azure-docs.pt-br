---
title: Conjuntos fonéticos de fala – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como o alfabeto fonético do serviço de fala é mapeado para o alfabeto fonético internacional (IPA) e quando usar qual conjunto.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 580de0112c99eef4ba22ba946f6a2b81bc326001
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102432036"
---
# <a name="speech-service-phonetic-sets"></a>Conjuntos fonéticos do serviço de fala

O serviço de fala define alfabetos fonéticos ("conjuntos de telefone" para curto), consistindo em sete idiomas; ,,,,, `en-US` `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` e `zh-TW` . Os conjuntos de telefone do serviço de fala normalmente são mapeados para o <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">alfabeto fonético internacional (IPA) </a>. Os conjuntos de telefone do serviço de fala são usados em conjunto com a [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md), como parte da oferta de serviço de conversão de texto em fala. Neste artigo, você aprenderá como esses conjuntos de telefone são mapeados e quando usar o conjunto de telefone.

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>Suprasegmentals em inglês

| Exemplo 1 (início para consoante, palavra inicial para vogal) | Exemplo 2 (multivozes para consoante, palavra média núcleo para vogal) | Exemplo 3 (coda para consoante, palavra final para vogal) | Comentários |
|--|--|--|--|
| levar este hambúrguer/b er **1** r-g AX r/ | falafel/f AX-l AA **1** -f AX l/ | guitarra/g IH-t AA **1** r/ | Conjunto de telefone do serviço de fala Coloque estresse após a vogal da sílaba sob estresse |
| inopportune/IH **2** -n AA-p AX r-t UW 1 n/ | dissimilaridade/d IH-s IH **2**-m AX-l eh 1-r AX-t iy/ | força de obra/w er 1 r k-f ao **2** r s/ | Conjunto de telefone do serviço de fala Coloque estresse após a vogal da sílaba com subestresse |

### <a name="english-vowels"></a>Vogais em inglês

| `sapi` | `ipa` | Exemplo 1     | Exemplo 2 | Exemplo 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **ea** t       | f **EE** l  | Vall **ey**                  |
| Ih     | `ɪ`   | **i** f        | **eu me**  |                             |
| Ey     | `eɪ`  | **um** te       | g **um** te  | d                     |
| Eh     | `ɛ`   | **e** muito     | p **e** t   | r **eh** (rara palavra finally) |
| ae     | `æ`   | **um** tivas    | c **a** t   | n **Ah** (incomum palavra finally) |
| aa     | `ɑ`   | **o** bstinate | p **o** ppy | r **Ah** (incomum palavra finally) |
| ao     | `ɔ`   | intervalo de **o**    | c **au** se | UT **Ah**                    |
| Ah     | `ʊ`   | b **OO** k      |           |                             |
| Omo     | `oʊ`  | **o** LD       | CL **o** ne | g **o**                      |
| UW     | `u`   | **C** ber      | b **OO** St | t **OO**                     |
| neles     | `ʌ`   | NCLE **u**     | c **u** t   |                             |
| AA     | `aɪ`  | **i** CE       | p **te**  | FL **y**                     |
| Aw     | `aʊ`  | **ou** t       | s **ou** th | c **Omo**                     |
| Oy     | `ɔɪ`  | **Oi** l       | j **Oi** n  | t **Oy**                     |
| UW y   | `ju`  | **Yu** ma      | homem h **u** | **ovo** f                     |
| AX     | `ə`   | **um** go       | wom **a** n | são **um**                    |

### <a name="english-r-colored-vowels"></a>R-vogais coloridas em inglês

| `sapi` | `ipa` | Exemplo 1    | Exemplo 2      | Exemplo 3  |
|--------|-------|--------------|----------------|------------|
| Ih r   | `ɪɹ`  | s **Ear**     | t **ir** amisu   | n **Ear**   |
| r eh   | `ɛɹ`  | plano de **ar** | ently de **ar** do aplicativo | SC **ar** e  |
| Ah r   | `ʊɹ`  |              |                | c **Your** e   |
| r   | `aɪɹ` | **Irar** Land  | substituir **ir** para f  | CH **OIR**  |
| Aw r   | `aʊɹ` | s de **hora**    | p **enor** realizando   | s   |
| ao r   | `ɔɹ`  | **ou** Alo   | m **ou** Al      | **OAR** s   |
| r AA   | `ɑɹ`  | tist **ar**   | St **ar** t      | **ar** c    |
| r er   | `ɝ`   | **Ear** th    | b **ir** d       | f **seu**    |
| AX r   | `ɚ`   |              | todos os GY **er**    | **er** supr |

### <a name="english-semivowels"></a>Semivowels em inglês

| `sapi` | `ipa` | Exemplo 1           | Exemplo 2  | Exemplo 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w** i, s **UE** de | Al **w** ays |           |
| a      | `j`   | **y** ARD, f **e** w   | em **eu** ligado  |           |

### <a name="english-aspirated-oral-stops"></a>Interrupções verbais aspirateada em inglês

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2   | Exemplo 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p** UT   | Ha **PP**  | **p** de Fla   |
| b      | `b`   | **b** IG   | número **b** er  | CRA **b**   |
| t      | `t`   | **t** ALK  | CAPI **t** Al | sough **t** |
| d      | `d`   | IG **d**   | foi executado **d** OM  | ro **d**    |
| k      | `k`   | **c** UT   | SLA **CK** er | Ira **q**   |
| g      | `g`   | **g** o    | um **g** o     | **g** de Dra   |

### <a name="english-nasal-stops"></a>Nasal em inglês paradas

| `sapi` | `ipa` | Exemplo 1        | Exemplo 2  | Exemplo 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m** em, Smash   | era da AC **m** | Roo **m**    |
| n      | `n`   | **n** o, s **n** º | te **n** t   | chicke **n** |
| ção     | `ŋ`   |                  | Li **n** k   | s **ing**    |

### <a name="english-fricatives"></a>Fricatives em inglês

| `sapi` | `ipa` | Exemplo 1   | Exemplo 2        | Exemplo 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f** ork    | Le **f** t         | Hal **f**   |
| v      | `v`   | **v** Alor   | e **v** ent        | lo **v** e   |
| th     | `θ`   | **em**    | empa **th** y      | segunda **-** feira  |
| feita     | `ð`   | **en**    | er **th**       | Smoo |
| s      | `s`   | **s** it     | **s** k de ri         | **s** de fatos  |
| z      | `z`   | **z** AP     | **s** y         | criança **s**   |
| sh     | `ʃ`   | **sh** e    | abbrevia **ti** ativado | **sh** de ru   |
| zh     | `ʒ`   | Acques **J** | lei **s** lhar     | Gara **g** e |
| h      | `h`   | do **h**    | EN **h** aparência      | a-**h** a!  |

### <a name="english-affricates"></a>Affricates em inglês

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2    | Exemplo 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch** em  | Fu **t** lhar   | Atta **ch** |
| jh     | `dʒ`  | **j** Oy   | Ori **g** inal | Oran **g** e |

### <a name="english-approximants"></a>Approximants em inglês

| `sapi` | `ipa` | Exemplo 1          | Exemplo 2  | Exemplo 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l** ID, g **l** AD  | Ace PA **l** | Qui **ll** |
| r      | `ɹ`   | **r** Ed, b **r** ing | Bo **RR** | ta **r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Suprasegmentals francesa

No entanto, o conjunto de telefone do serviço de fala coloca estresse após a vogal da sílaba sob estresse; o `fr-FR` conjunto de telefone do serviço de fala não dá suporte ao ' ˌ ' de subestresse IPA. Se a subcarga IPA for necessária, você deverá usar o IPA diretamente.

### <a name="french-vowels"></a>Vogais francesas

| `sapi` | `ipa` | Exemplo 1     | Exemplo 2       | Exemplo 3 |
|--------|-------|---------------|-----------------|-----------|
| um      | `a`   | **um** rbre     | p **a** TTE       | ir **a**   |
| aa     | `ɑ`   |               | p **.** te        | p **a** s   |
| AA ~   | `ɑ̃`  | Fant de **en**    | Enf **en** t      | t **em** PS |
| AX     | `ə`   |               | p **e** Tite      | l **e**    |
| Eh     | `ɛ`   | **e** lle      | p **e** RDU       | ét **ai** t |
| eu     | `ø`   | **œU** FS      | CR **UE**     | t **eu**  |
| Ey     | `e`   | ému           | crétin          | ôté       |
| Eh ~   | `ɛ̃`  | porta **instantânea** | p **Ein** turar    | Passe-os **em** |
| iy     | `i`   | **eu** Dée      | te      | Sou **eu**   |
| OE     | `œ`   | **œU** f       | p **UE** r        |           |
| nossa     | `ɔ`   | **o** bstacle  | c **o** RPS       |           |
| Oh ~   | `ɔ̃`  | **em** ze      | r **em** Deur     | b **em**   |
| Omo     | `o`   | diteur **au**  | b **EAU** de vitória    | p **ô**    |
| OE ~   | `œ̃ ` | **anula**        | l **desfazer** di       | br **un**  |
| UW     | `u`   | **ou** trage   | INTR **ou** vable | **CN**    |
| uy     | `y`   | **u** ne       | p **u** nir       | Él **u**   |

### <a name="french-consonants"></a>Consoantes francesas

| `sapi` | `ipa` | Exemplo 1   | Exemplo 2     | Exemplo 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b** ête    | Ha **b** Ille   | ro **b** e                         |
| d      | `d`   | irar **d**    | Ron **d** EUR   | Chau **d** e                       |
| f      | `f`   | **f** Emme   | Su **FF** ixe   | Bo **f**                          |
| g      | `g`   | **g** auche  | é **g** EPA     | BA **Gu** e                        |
| ção     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)Park **ing** |
| hipótese     | `ɥ`   | Ile h **u**   | n **u** irar     |                                  |
| k      | `k`   | arte **c**   | é **c** aille   | ser **c**                          |
| l      | `l`   | **l** Lon    | é **l** irar     | BA **l**                          |
| m      | `m`   | **Adam**  | er **m** ia     | OC **mm** e                        |
| n      | `n`   | **n** UOs    | ir do te **n**     | Bo **NN** e                        |
| NJ     | `ɲ`   |             |               | Pei **GN** e                       |
| p      | `p`   | **p** te   | Re-**p** como     | CA **p**                          |
| r      | `ʁ`   | **r** em     | **p e r** de cha   | enviado por **r**                       |
| s      | `s`   | Ourir **s**  | um **SS** ez     | PA **SS** e                        |
| sh     | `ʃ`   | **ch** | Ma **ch** inha   | OC **ch** e                        |
| t      | `t`   | **t** ête    | ô **t** er      | ne **t**                          |
| v      | `v`   | **v** ent    | em **v**, digite  | Rê **v** e                         |
| w      | `w`   | **ou** i     | f **ou** inha    |                                  |
| a      | `j`   | **y** OD     | p **i** étiner  | **Ille** Marse                    |
| z      | `z`   | * * z * * éro   | Rai **s** onner | ro **s** e                         |
| zh     | `ʒ`   | Ardin **j**  | homem **g** er    | piè **g** e                        |
|        | `n‿`  |             |               | Arbre u **n**                     |
|        | `t‿`  |             |               | Quan **d**                        |
|        | `z‿`  |             |               | di **x**                          |

<a id="fr-1"></a>
**1** *somente para algumas palavras estrangeiras.*

> [!TIP]
> O `fr-FR` conjunto de telefone do serviço de fala não dá suporte às seguintes Liasions francesas, `n‿` , `t‿` e `z‿` . Se forem necessários, você deve considerar o uso do IPA diretamente.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Alemão suprasegmentals

| Exemplo 1 (início para consoante, palavra inicial para vogal) | Exemplo 2 (multivozes para consoante, palavra média núcleo para vogal) | Exemplo 3 (coda para consoante, palavra final para vogal) | Comentários |
|--|--|--|--|
| Anders/a **1** n-d AX r s/ | Multiplikationszeichen/m Ah l-t iy-p l iy-k a-TS y Omo **1** n s-TS e-c n/ | Biologie/b iy-Omo-g iy **1**/ | Conjunto de telefone do serviço de fala Coloque estresse após a vogal da sílaba sob estresse |
| Allgemeinwissen/a **2** l-g AX-m e 1 n-v IH-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z Oh **2** AX r-g Ah ng s-f IH AX r-m a/ | Computertomographie/k Oh m-p y UW 1-t AX r-t Omo-m e-g r a-f iy **2**/ | Conjunto de telefone do serviço de fala Coloque estresse após a vogal da sílaba com subestresse |

### <a name="german-vowels"></a>Vogais alemãs

| `sapi` | `ipa`     | Exemplo 1                             | Exemplo 2     | Exemplo 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| um     | `aː`      | **Um** ber                              | Maßst **b**   | Esquema **a**                         |
| um      | `a`       | **Um** Bfall                            | B **a** ch      | Agath **a**                         |
| nossa     | `ɔ`       | **O** Sten                             | Sten de PF **o**   |                                    |
| Eh    | `ɛː`      | **Ä** hnlichkeit                       | B **ä** r       | [<sup>1</sup>](#de-v-1) **AE** fasci |
| Eh     | `ɛ`       | **ä** ndern                            | ProZ **e** NT   | **AE** Amygdal                      |
| AX     | `ə`       | [<sup>2</sup>](#de-v-2)' v **e** rstauen | Aach **e** n    | Frag **e**                          |
| iy     | `iː`      | **Eu** executei                              | ABB **IE** gt   | Relativitätstheor **IE**            |
| Ih     | `ɪ`       | **Eu** nnung                            | s **i** NGen    | **Y** de madeira                          |
| eu     | `øː`      | **Ö** Sen                              | ABL **ö** Sten  | Malm **ö**                          |
| Omo     | `o`, `oː` | **o** hnE                              | Balk **o** n    | Trept                        |
| OE     | `œ`       | **Ö** ffnung                           | BEF **ö** rdern |                                    |
| Ey     | `e`, `eː` | **E** Berhard                          | ABF **e** gt    | b                                  |
| UW     | `uː`      | **U**                               | H **u** t       | AKK **u**                           |
| Ah     | `ʊ`       | Nterschiedes **U**                     | b **u** NT      |                                    |
| conclusão     | `yː`      | **Ü** bermut                           | PFL **ü** gt    | Homens **ü**                           |
| uy     | `ʏ`       | **ü** ppig                             | Haste S **y**    |                                    |

<a id="de-v-1"></a>
**1** *somente em palavras de origem estrangeira, como: fasci **AE**.*<br>
<a id="de-v-2"></a>
**2** *palavra-inicialmente somente em palavras de origem estrangeira, como **um** ppointment. Sílaba – inicialmente em: ' v **e** rstauen.*

### <a name="german-diphthong"></a>Alemão diphthong

| `sapi` | `ipa`       | Exemplo 1    | Exemplo 2          | Exemplo 3 |
|--------|-------------|--------------|--------------------|-----------|
| AA     | `ai`        | nsam de **Ei**   | Unabhängigk **Ei** t | Abt **Ei** |
| Aw     | `au`        | ßen **au**    | ABB **au** St        | St **au**  |
| Oy     | `ɔy`, `ɔʏ̯` | Phorie da **UE** | TR **äu** MT         | SCH **eu** |

### <a name="german-semivowels"></a>Alemão semivowels

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2    | Exemplo 3  |
|--------|-------|-----------|--------------|------------|
| AX r   | `ɐ`   |           | abänd **er** n | bloquear **er** |

### <a name="german-consonants"></a>Consoantes alemãs

| `sapi` | `ipa` | Exemplo 1 | Exemplo 2 | Exemplo 3 |
|--|--|--|--|--|
| b | `b` | **B** ANK |  | [<sup>1</sup>](#de-c-1) Pu **b** |  |
| c | `ç` | **Ch** Emie | mögli **ch** St | [<sup>2</sup>](#de-c-2)i **ch** |
| d | `d` | anken **d** | [<sup>3</sup>](#de-c-3) Len **d** l | [<sup>4</sup>](#de-c-4) Clau **d** e |  |
| jh | `ʤ` | Eff **J** | gemana **g** t | [<sup>5</sup>](#de-c-5) **G** e |
| f | `f` | **F** ahrtdauer | Angri **FF** slustig | abbruchrei **f** |  |
| g | `g` | **g** UT |  | [<sup>6</sup>](#de-c-6) **G** do GRE |  |
| h | `h` | **H** ausanbau |  |  |  |
| a | `j` | **J** OD | Reakt **i** | Hu **i** |  |
| k | `k` | **K** OMA | Aspe **k** t | FLEC **k** |  |
| l | `l` | **l** au | ähne **l** n | zuvie **l** |  |
| m | `m` | **&** UT | Um **t** | Leh **m** |  |
| n | `n` | **n** º | u **n** d | Não |  |
| ção | `ŋ` | [<sup>7</sup>](#de-c-7)**ng** Uyen | **NK** de schwa | R **ing** |  |
| p | `p` | **P** Artner | Abru **p** t | **P** de ti |  |
| repetição | `pf` | ERD de **PF** | camiseta **de** Dam | Para **PF** |  |
| r | `ʀ`, `r`, `ʁ` | Eise **R** | KNU **RR** t | Haa **r** |  |
| s | `s` | taccato de [<sup>8</sup>](#de-c-8)**S** | t **bi** | Mie **s** |  |
| sh | `ʃ` | **SCH** ulo | Mi **SCH** t | Lappi **SCH** |  |
| t | `t` | **T** Raum | Raße S **t** | Mu **t** |  |
| ts | `ts` | UG **Z** | Ar **z** t | Wit **z** |  |
| ch | `tʃ` | **Tsch** echien | aufgepu **tsch** t | bundesdeu **tsch** |  |
| v | `v` | **w** Inken | Q **u** Alle | [<sup>9</sup>](#de-c-9) GR **OO** ve |  |
| x | [<sup>10</sup>](#de-c-10) `x` ,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) BA **ch** erach | Ma **ch** t mögli **ch** St | Schma **ch** ' i **ch** |
| z | `z` | uper **s** |  |  |  |
| zh | `ʒ` | **G** enre | B **re** ezinski | Edvi **g** e |

<a id="de-c-1"></a>
**1** *somente em palavras de origem estrangeira, como: PU **b**.*<br>
<a id="de-c-2"></a>
**2** *soft "ch" após "e" e "i"*<br>
<a id="de-c-3"></a>
**3** *somente em palavras de origem estrangeira, como: Len **d** l.*<br>
<a id="de-c-4"></a>
**4** *somente em palavras de origem estrangeira, como: Clau **d** e.*<br>
<a id="de-c-5"></a>
**5** *somente em palavras de origem estrangeira, como: canal **g** e.*<br>
<a id="de-c-6"></a>
**6** *Word-terminally somente em palavras de origem estrangeira, como o GRE **g**.*<br>
<a id="de-c-7"></a>
**7** *somente em palavras de origem estrangeira, como: **ng** Uyen.*<br>
<a id="de-c-8"></a>
**8** *somente em palavras de origem estrangeira, como: **S** taccato.*<br>
<a id="de-c-9"></a>
**9** *somente em palavras de origem estrangeira, como: GR **OO** ve.*<br>
<a id="de-c-10"></a>
**10** *o IPA `x` é um "ch" rígido após todas as vogais não iniciais (a, AA, Oh, Ah, UW e diphthong aw).*<br>
<a id="de-c-11"></a>
**11** *o IPA `ç` é um ' ch ' suave após as vogais frontais (IH, iy, eh, AE, uy, UE, OE, a UE também está em diphthongs, Oy) e consoantes*<br>
<a id="de-c-12"></a>
**12** *palavras-inicialmente apenas em palavras de origem estrangeira, como: **J** uan. Sílaba – inicialmente, também em palavras como: BA **ch** erach.*<br>

### <a name="german-oral-consonants"></a>Consoantes verbais em alemão

| `sapi` | `ipa` | Exemplo 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b AX-^ a 1 x t-l IH c/ |

> [!NOTE]
> Precisamos adicionar um telefone [GS \] entre duas vogais distintas, exceto que as duas vogais são um diphthong genuíno. Essa consoante oral é uma parada glotal, para obter mais informações, consulte <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank"> parada glotal <span class="docon docon-navigate-external x-hidden-focus"> </a> </a> .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Vogais em espanhol

| `sapi` | `ipa` | Exemplo 1    | Exemplo 2     | Exemplo 3    |
|--------|-------|--------------|---------------|--------------|
| um      | `a`   | **um** LTO     | c **a** ntar    | CAS **a**     |
| i      | `i`   | **eu** bérica  | Spa **do** AV    | imposto **i**     |
| e      | `e`   | **e** Lefante | às **&** nPara    | Elefant **e** |
| o      | `o`   | **o** caso    | ENC **o** ntrar | ocasenc **o** |
| u      | `u`   | sted **u**    | p **u** NTA     | Juanl **u**   |

### <a name="spanish-consonants"></a>Consoantes do espanhol

| `sapi` | `ipa`      | Exemplo 1  | Exemplo 2      | Exemplo 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b** aobab |                | am **b**        |
|        | `β`        |            | Bao **b** AB     | BAOBA **b**     |
| ch     | `tʃ`       | **ch** eque | Co **&**      | Marraque **ch** |
| d      | `d`        | Edo **d**   |                | portlan **d**   |
|        | `ð`        |            | de **d** o       | verda **d**     |
| f      | `f`        | **f** ácil  | elementos **f** antes   | Pu **f**        |
| g      | `g`        | **g** Anga  |                | dópin **g**     |
|        | `ɣ`        |            | um **g** UA       | tuare **g**     |
| j      | `j`        | **eu** odo   | Cal **insira**   | Re-**y**        |
| JJ     | `j.j` `jj` |            | vi **ll** a      |                |
| k      | `k`        | Oche **c**  | Bo **c** a       | titáni **c**    |
| l      | `l`        | **l** ápiz  | um **l** a        | fio **l**     |
| precisa     | `ʎ`        | **ave**  | desarro **ll** |                |
| m      | `m`        | ordem **m** | um ar **m**       | álbu **m**      |
| n      | `n`        | **n** Ada   | CE **n** a       | rató **n**      |
| NJ     | `ɲ`        | **ñ** Aña   | Ara **ñ** azo    |                |
| p      | `p`        | a oca do **p**   | para **p** o       | ARM **p**       |
| r      | `ɾ`        |            | CA **r** a       | abri **r**      |
| RR     | `r`        | adio **r**  | Co **RR** e      | Pu **RR**       |
| s      | `s`        | Aco **s**   | VA **s** o       | pelo **s**      |
| t      | `t`        | **t** oldo  | um **t** ar       | Disque **t**    |
| th     | `θ`        | Ebra **z**  | a **z** UL       | lápi **z**      |
| w      | `w`        | ESO h **u**  | AG **u** a       | gua **u**       |
| x      | `x`        | **j** OTA   | um **j** o        | relo **j**      |

> [!TIP]
> O `es-ES` conjunto de telefone do serviço de fala não dá suporte às seguintes IPA do espanhol, `β` , `ð` e `ɣ` . Se forem necessários, você deve considerar o uso do IPA diretamente.

# <a name="zh-cn"></a>[ZH-CN](#tab/zh-CN)

O telefone do serviço de fala definido para `zh-CN` o é baseado no conjunto de <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">pinyin </a> de telefone nativo definido.

### <a name="tone"></a>Tom

| Tom pinyin | `sapi` | Exemplo de caractere |
|-------------|--------|-------------------|
| mā          | MA 1  | 妈                 |
| má          | Ma 2  | 麻                 |
| mǎ          | ma 3  | 马                 |
| mà          | Ma 4  | 骂                 |
| ma          | Ma 5  | 嘛                 |

#### <a name="example"></a>Exemplo

| Caractere | Serviço de Fala                |
|-----------|-------------------------------|
| 组织关系      | zu 3-Zhi 1-Guan 1-XI 5 |
| 累进        | Lei 3 – Jin 4                 |
| 西宅巷       | XI 1-Zhai 2-Xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

O telefone do serviço de fala definido para `zh-TW` o é baseado no telefone <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">bopomofo </a> nativo definido.

### <a name="tone"></a>Tom

| Tom do serviço de fala | Tom bopomofo | Exemplo (Word) | Telefones de serviço de fala | Bopomofo | Pinyin (拼音) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | vazio         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇ ㄗ˙               | 一ㄥˇ ㄗ˙  | yǐng Zi    |

#### <a name="example"></a>Exemplo

| Caractere | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

O telefone do serviço de fala definido para `ja-JP` o é baseado no conjunto <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">kana </a> do telefone nativo.

### <a name="stress"></a>Stress

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` subestresse  |

#### <a name="example"></a>Exemplo

| Caractere | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | Vá ˈ wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ +  | sajitecikaˌ |

***