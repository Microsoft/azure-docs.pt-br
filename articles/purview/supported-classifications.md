---
title: Lista de classificações com suporte
description: Esta página lista as classificações de sistema com suporte no Azure alcance.
author: anmuk601
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 02/05/2021
ms.openlocfilehash: 60aede65f8217d46844398d0199ff7edca7f36a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200799"
---
# <a name="supported-classifications-in-azure-purview"></a>Classificações com suporte no Azure alcance

Este artigo lista as classificações de sistema com e com suporte no Azure alcance (versão prévia).


- **Limite de correspondência distinta**: o número total de valores de dados distintos que precisam ser encontrados em uma coluna antes que o verificador execute o padrão de dados nele. Nossas regras de classificação do sistema exigem que haja pelo menos 8 valores distintos em cada coluna para serem sujeitos à classificação. O sistema requer esse valor para garantir que a coluna contenha dados suficientes para que o verificador classifique com precisão. Por exemplo, uma coluna que contém várias linhas que contêm o valor 1 não será classificada. As colunas que contêm uma linha com um valor e o restante das linhas têm valores nulos também não serão classificadas. Se você especificar vários padrões, esse valor se aplicará a cada um deles.

- **Limite mínimo de correspondência**: é o percentual mínimo de correspondências de valor de dados em uma coluna que deve ser encontrada pelo verificador para que a classificação seja aplicada. O valor de classificação do sistema é definido em 60%.


## <a name="defined-system-classifications"></a>Classificações de sistema definidas

O Azure alcance classifica os dados por [Regex](https://wikipedia.org/wiki/Regular_expression) e o [filtro de flor](https://wikipedia.org/wiki/Bloom_filter). As listas a seguir descrevem o formato, o padrão e as palavras-chave para as classificações do sistema definidas pelo alcance do Azure.

Cada nome de classificação é prefixado pela MICROSOFT.

## <a name="bloom-filter-classifications"></a>As classificações de filtro de flor

## <a name="city-country-and-place"></a>Cidade, país e lugar

Os filtros de cidade, país e lugar foram preparados usando os melhores conjuntos de dados disponíveis para preparar o dado.

## <a name="person"></a>Pessoa

O filtro de incair na pessoa foi preparado usando os dois conjuntos de valores abaixo.

- [2010 US censo de dados para sobrenomes (entradas 162-K)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Nomes populares de bebê (de SSN), usando todos os anos 1880-2019 (98-K entradas)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>Classificações RegEx

## <a name="aba-routing"></a>Roteamento de ABA

### <a name="format"></a>Formatar

Nove dígitos, que podem estar em um padrão formatado ou não formatado

### <a name="pattern"></a>Padrão

Binário

- quatro dígitos começando com 0, 1, 2, 3, 6, 7 ou 8
- um hífen
- quatro dígitos
- um hífen
- um dígito

Não formatado: nove dígitos consecutivos começando com 0, 1, 2, 3, 6, 7 ou 8

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Número de identidade nacional da Argentina (DNI)

### <a name="format"></a>Formatar

Oito dígitos com ou sem pontos

### <a name="pattern"></a>Padrão

Oito dígitos:

- dois dígitos
- um período opcional
- três dígitos
- um período opcional
- três dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Número da conta bancária da Austrália

### <a name="format"></a>Formatar

Seis a 10 dígitos com ou sem um número de filial do estado do banco

### <a name="pattern"></a>Padrão

O número da conta é de seis a 10 dígitos.

Número da ramificação do estado do banco da Austrália:

- três dígitos
- um hífen
- três dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>Número da licença do driver da Austrália

### <a name="format"></a>Formatar
Nove letras e dígitos

### <a name="pattern"></a>Padrão
nove letras e dígitos:

- dois dígitos ou letras (não diferencia maiúsculas de minúsculas)
- dois dígitos
- cinco dígitos ou letras (não diferencia maiúsculas de minúsculas)

OU

- uma a duas letras opcionais (não diferencia maiúsculas de minúsculas)
- quatro a nove dígitos

OU

- nove dígitos ou letras (não diferencia maiúsculas de minúsculas)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Número de Medicare australiano

### <a name="format"></a>Formatar

10-11 dígitos

### <a name="pattern"></a>Padrão

10-11 dígitos:

- o primeiro dígito está no intervalo de 2-6
- nono dígito é um dígito de verificação
- décimo dígito é o dígito do problema
- o décimo primeiro dígito (opcional) é o número individual

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Número do passaporte da Austrália

### <a name="format"></a>Formatar

Uma letra seguida por sete dígitos

### <a name="pattern"></a>Padrão

Uma letra (não diferencia maiúsculas de minúsculas) seguida de sete dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_passport"></a>Palavra-chave \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Palavra-chave \_ \_ número do passaporte \_

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Número do arquivo de imposto da Austrália

### <a name="format"></a>Formatar

oito a nove dígitos

### <a name="pattern"></a>Padrão

oito a nove dígitos geralmente apresentados com espaços da seguinte maneira:

- três dígitos
- um espaço opcional
- três dígitos
- um espaço opcional
- dois a três dígitos em que o último dígito é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_australia_tax_file_number"></a>Palavra-chave \_ \_ número do arquivo de imposto Austrália \_ \_

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>Número nacional da Bélgica

### <a name="format"></a>Formatar

11 dígitos além de delimitadores opcionais

### <a name="pattern"></a>Padrão

11 dígitos mais delimitadores:

- seis dígitos e dois períodos opcionais no formato AA. MM. DD para data de nascimento
- Um delimitador opcional de ponto, traço, espaço
- três dígitos sequenciais (ímpares para homens, mesmo para mulheres)
- Um delimitador opcional de ponto, traço, espaço
- dois dígitos de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_belgium_national_number"></a>Palavra-chave \_ Bélgica \_ \_ número nacional

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>Número do CPF do Brasil

### <a name="format"></a>Formatar

11 dígitos que incluem um dígito de verificação e podem ser formatados ou não formatados

### <a name="pattern"></a>Padrão

Binário

- três dígitos
- um período
- três dígitos
- um período
- três dígitos
- um hífen
- dois dígitos, que são dígitos de verificação

Não formatado:

- 11 dígitos onde os dois últimos dígitos são dígitos de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_brazil_cpf"></a>Palavra-chave \_ \_ CPF

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>CNPJ (número da entidade legal) do Brasil

### <a name="format"></a>Formatar

14 dígitos que incluem um número de registro, número de ramificação e dígitos de cheque, além de delimitadores

### <a name="pattern"></a>Padrão

14 dígitos, mais delimitadores:

- dois dígitos
- um período
- três dígitos
- um período
- três dígitos (esses oito primeiros dígitos são o número de registro)
- uma barra
- número de ramificação de quatro dígitos
- um hífen
- dois dígitos, que são dígitos de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_brazil_cnpj"></a>\_CNPJ do Brasil da palavra-chave \_

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>RG (cartão de identificação nacional) do Brasil

### <a name="format"></a>Formatar

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Padrão

:::no-loc text="Registro Geral (old format):":::

- dois dígitos
- um período
- três dígitos
- um período
- três dígitos
- um hífen
- um dígito, que é um dígito de verificação

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 dígitos
- um hífen
- um dígito, que é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_brazil_rg"></a>Palavra-chave \_ RG do Brasil \_

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>Número da conta bancária do Canadá

### <a name="format"></a>Formatar

7 ou 12 dígitos

### <a name="pattern"></a>Padrão

Um número de conta bancária do Canadá tem 7 ou 12 dígitos.

Um número de trânsito da conta bancária do Canadá é:

- cinco dígitos
- um hífen
- três dígitos ou
- um zero &quot; 0&quot;
- oito dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_canada_bank_account_number"></a>Palavra-chave \_ \_ número da conta bancária do Canadá \_ \_

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Número da licença do driver do Canadá

### <a name="format"></a>Formatar

Varia por província

### <a name="pattern"></a>Padrão

Vários padrões que abrangem Alberta, Colúmbia Britânica, Manitoba, New Brunswick, Newfoundland/Labrador, Nova Escócia, Ontário, Príncipe Edward Island, Quebec e Saskatchewan

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_province_name_drivers_license_name"></a>Palavra-chave \_ [nome da província \_ ] \_ nome da licença de drivers \_ \_

- A abreviação da província, por exemplo, AB
- O nome da província, por exemplo, Alberta

#### <a name="keyword_canada_drivers_license"></a>Palavra-chave \_ \_ drivers de licença do Canadá \_

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Número do serviço de integridade do Canadá

### <a name="format"></a>Formatar

10 dígitos

### <a name="pattern"></a>Padrão

10 dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_canada_health_service_number"></a>Palavra-chave \_ \_ número de serviço de integridade Canadá \_ \_

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Número do passaporte do Canadá

### <a name="format"></a>Formatar

duas letras maiúsculas seguidas por seis dígitos

### <a name="pattern"></a>Padrão

duas letras maiúsculas seguidas por seis dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_canada_passport_number"></a>Palavra-chave número do passaporte do \_ Canadá \_ \_

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Palavra-chave \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Número de identificação de integridade pessoal do Canadá (PHIN)

### <a name="format"></a>Formatar

nove dígitos

### <a name="pattern"></a>Padrão

nove dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_canada_phin"></a>Palavra-chave \_ Canada \_ Phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Palavra-chave \_ Canadá \_ província

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Número de seguro social do Canadá

### <a name="format"></a>Formatar

nove dígitos com hifens ou espaços opcionais

### <a name="pattern"></a>Padrão

Binário

- três dígitos
- um hífen ou espaço
- três dígitos
- um hífen ou espaço
- três dígitos

Não formatado: nove dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_sin"></a>Palavra-chave \_ sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Palavra-chave \_ sin \_ colaborativa

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Número do cartão de identidade do Chile

### <a name="format"></a>Formatar

sete a oito dígitos, além de delimitadores um dígito ou letra de cheque

### <a name="pattern"></a>Padrão

sete a oito dígitos, além de delimitadores:

- um a dois dígitos
- um período opcional
- três dígitos
- um período opcional
- três dígitos
- um traço
- um dígito ou letra (não diferencia maiúsculas de minúsculas), que é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_chile_id_card"></a>\_Placa de \_ ID do Chile de palavra-chave \_

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>Número PRC (cartão de identidade residente) da China

### <a name="format"></a>Formatar

18 dígitos

### <a name="pattern"></a>Padrão

18 dígitos:

- seis dígitos, que são um código de endereço
- oito dígitos no formato AAAAMMDD, que são a data de nascimento
- três dígitos, que são um código de pedido
- um dígito, que é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_china_resident_id"></a>Palavra-chave da \_ \_ ID residente da China \_

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Número do cartão de crédito

### <a name="format"></a>Formatar

14 a 16 dígitos, que podem ser formatados ou não formatados (dddddddddddddddd) e que devem passar no teste Luhn.

### <a name="pattern"></a>Padrão

Padrão complexo e robusto que detecta cartões de todas as principais marcas em todo o mundo, incluindo Visa, MasterCard, cartão Discover, JCB, American Express, cartões de presente e cartões cliente.

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_cc_verification"></a>\_Verificação de CC da palavra-chave \_

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>\_Nome do CC da palavra-chave \_

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Número da licença do driver da Croácia

Essa entidade de tipo de informações confidenciais só está disponível no tipo de informação confidencial do número de licença do driver da UE.

### <a name="format"></a>Formatar

oito dígitos sem espaços e delimitadores

### <a name="pattern"></a>Padrão

oito dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_eu_drivers_license_number"></a>O \_ \_ número da carteira do driver da UE \_ \_

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>\_ \_ \_ Número da carteira de motorista do driver da UE do Croácia \_ Words \_

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Número do cartão de identidade da Croácia

Essa entidade de tipo de informações confidenciais está incluída no tipo de informações confidenciais do número de identificação nacional da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

nove dígitos

### <a name="pattern"></a>Padrão

nove dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_croatia_id_card"></a>Placa de identificação da palavra-chave \_ Croácia \_ \_

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>Número da OIB (identificação pessoal da Croácia)

### <a name="format"></a>Formatar

11 dígitos

### <a name="pattern"></a>Padrão

11 dígitos:

- 10 dígitos
- o dígito final é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_croatia_oib_number"></a>\_ \_ Número OIB da palavra-chave Croácia \_

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>Número de identificação pessoal da Dinamarca

### <a name="format"></a>Formatar

10 dígitos contendo um hífen

### <a name="pattern"></a>Padrão

10 dígitos:

- seis dígitos no formato DDMMYY, que são a data de nascimento
- um hífen
- quatro dígitos em que o dígito final é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_denmark_id"></a>ID da palavra-chave \_ Dinamarca \_

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>Número do cartão de débito da UE

### <a name="format"></a>Formatar

16 dígitos

### <a name="pattern"></a>Padrão

Padrão complexo e robusto

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_eu_debit_card"></a>Cartão de débito da palavra-chave \_ UE \_ \_

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Dicionário de \_ termos de cartão de palavra-chave \_ \_

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>\_ \_ \_ Dicto de termos de segurança de cartão de palavras \_

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Dicionário de palavra-chave de termos de validade de \_ cartão \_ \_ \_

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>Número da licença do driver da UE

Essas são as entidades no tipo de informação confidencial do número de licença do driver da UE.

- Áustria
- Bélgica
- Bulgária
- Croácia
- Chipre
- Tcheco
- Dinamarca
- Estônia
- Finlândia
- França
- Alemanha
- Grécia
- Hungria
- Irlanda
- Itália
- Letônia
- Lituânia
- Luxemburg
- Malta
- Países Baixos
- Polônia
- Portugal
- Romênia
- Eslováquia
- Eslovênia
- Espanha
- Suécia
- Inglaterra

## <a name="eu-national-identification-number"></a>Número de identificação nacional da UE

Essas são as entidades no tipo de informações confidenciais do número de identificação nacional da UE.

- Áustria
- Bélgica
- Bulgária
- Croácia
- Chipre
- Tcheco
- Dinamarca
- Estônia
- Finlândia
- França
- Alemanha
- Grécia
- Hungria
- Irlanda
- Itália
- Letônia
- Lituânia
- Luxemburg
- Malta
- Países Baixos
- Polônia
- Portugal
- Romênia
- Eslováquia
- Eslovênia
- Espanha
- Inglaterra

## <a name="eu-passport-number"></a>Número do Passport da UE

Estas são as entidades no número do Passport da UE informações confidenciais typeThese são as entidades no grupo de números do Passport da UE.

- Áustria
- Bélgica
- Bulgária
- Croácia
- Chipre
- Tcheco
- Dinamarca
- Estônia
- Finlândia
- França
- Alemanha
- Grécia
- Hungria
- Irlanda
- Itália
- Letônia
- Lituânia
- Luxemburg
- Malta
- Países Baixos
- Polônia
- Portugal
- Romênia
- Eslováquia
- Eslovênia
- Espanha
- Suécia
- Inglaterra

## <a name="eu-social-security-number-or-equivalent-identification"></a>Número do seguro social ou identificação equivalente da UE

Essas são as entidades que estão no número do seguro social da UE ou no tipo de informações confidenciais de identificação equivalente.

- Áustria
- Bélgica
- Croácia
- Tcheco
- Dinamarca
- Finlândia
- França
- Alemanha
- Grécia
- Hungria
- Portugal
- Espanha
- Suécia

## <a name="eu-tax-identification-number"></a>Número de identificação do imposto da UE

Essas entidades estão no tipo de informação confidencial do número de identificação de imposto da UE.

- Áustria
- Bélgica
- Bulgária
- Croácia
- Chipre
- Tcheco
- Dinamarca
- Estônia
- Finlândia
- França
- Alemanha
- Grécia
- Hungria
- Irlanda
- Itália
- Letônia
- Lituânia
- Luxemburg
- Malta
- Países Baixos
- Polônia
- Portugal
- Romênia
- Eslováquia
- Eslovênia
- Espanha
- Suécia
- Inglaterra



## <a name="finland-national-id"></a>ID nacional da Finlândia

### <a name="format"></a>Formatar

seis dígitos mais um caractere que indica um século mais três dígitos, além de um dígito de verificação

### <a name="pattern"></a>Padrão

O padrão deve incluir todos os seguintes itens:

- seis dígitos no formato DDMMYY, que são uma data de nascimento
- marcador de século ('-', ' + ' ou ' a ')
- número de identificação pessoal de três dígitos
- um dígito ou uma letra (não diferencia maiúsculas de minúsculas) que é um dígito de verificação

### <a name="keywords"></a>Palavras-chave

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Número do passaporte da Finlândia

Essa entidade de tipo de informações confidenciais está disponível no tipo de informações confidenciais do número do Passport da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

combinação de nove letras e dígitos

### <a name="pattern"></a>Padrão

combinação de nove letras e dígitos:

- duas letras (não diferencia maiúsculas de minúsculas)
- sete dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_eu_passport_number_common"></a>Palavras-chave \_ número do Passport da UE \_ \_ \_ comum

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Palavra-chave \_ Finlândia \_ número do passaporte \_

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Número da licença do driver da França

Essa entidade de tipo de informações confidenciais está disponível no tipo de informação confidencial do número de licença do driver da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

12 dígitos

### <a name="pattern"></a>Padrão

12 dígitos com validação para padrões semelhantes de desconto, como números de telefone em francês

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_french_drivers_license"></a>Palavra-chave de licença de drivers em \_ francês \_ \_

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>Placa de ID nacional da França (CNI)

### <a name="format"></a>Formatar

12 dígitos

### <a name="pattern"></a>Padrão

12 dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_france_eu_national_id_card"></a>Palavras-chave \_ França da \_ UE \_ National \_ ID \_ Card

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Número do Passport da França

Essa entidade de tipo de informações confidenciais está disponível no tipo de informações confidenciais do número do Passport da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

nove dígitos e letras

### <a name="pattern"></a>Padrão

nove dígitos e letras:

- dois dígitos
- duas letras (não diferencia maiúsculas de minúsculas)
- cinco dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_passport"></a>Palavra-chave \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>INSEE (número de seguro social da França) ou identificação equivalente

Essa entidade de tipo de informações confidenciais está incluída no número de segurança social da UE e no tipo de informações confidenciais da ID equivalente e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

15 dígitos

### <a name="pattern"></a>Padrão

Deve corresponder a um dos dois padrões:

- 13 dígitos seguidos de um espaço seguido de dois dígitos ou
- 15 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_fr_insee"></a>Palavra-chave \_ fr \_ INSEE

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>Número da licença do driver da Alemanha

Essa entidade de tipo de informações confidenciais está incluída no tipo de informação confidencial do número de licença do driver da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

combinação de 11 dígitos e letras

### <a name="pattern"></a>Padrão

11 dígitos e letras (não diferencia maiúsculas de minúsculas):

- um dígito ou uma letra
- dois dígitos
- seis dígitos ou letras
- um dígito
- um dígito ou uma letra

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_german_drivers_license_number"></a>Palavra-chave \_ \_ número de licença de drivers alemão \_ \_

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Número do cartão de identidade da Alemanha

### <a name="format"></a>Formatar

desde 1 de novembro de 2010: nove letras e dígitos

de 1 de abril de 1987 até 31 de outubro de 2010:10 dígitos

### <a name="pattern"></a>Padrão

desde 1 de novembro de 2010:

- uma letra (não diferencia maiúsculas de minúsculas)
- oito dígitos

de 1 de abril de 1987 até 31 de outubro de 2010:

- 10 dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_germany_id_card"></a>Placa de ID da palavra-chave \_ Alemanha \_ \_

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Número do Passport da Alemanha

Essa entidade de tipo de informações confidenciais está incluída no tipo de informações confidenciais do número do Passport da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

10 dígitos ou letras

### <a name="pattern"></a>Padrão

O padrão deve incluir todos os seguintes itens:

- o primeiro caractere é um dígito ou uma letra desse conjunto (C, F, G, H, J, K)
- três dígitos
- cinco dígitos ou letras deste conjunto (C,-H, J-N, P, R, T, V-Z)
- um dígito

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_german_passport"></a>Palavra-chave do \_ \_ Passport alemão

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>Cartão de ID nacional da Grécia

### <a name="format"></a>Formatar

Combinação de 7-8 letras e números mais um traço

### <a name="pattern"></a>Padrão

Sete letras e números (formato antigo):

- Uma letra (qualquer letra do alfabeto grego)
- Um traço
- Seis dígitos

Oito letras e números (novo formato):

- Duas letras cujo caractere em maiúscula ocorre tanto no grego quanto no alfabeto latino (ABEZHIKMNOPTYX)
- Um traço
- Seis dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_greece_id_card"></a>Cartão de ID da palavra-chave \_ Grécia \_ \_

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>Número de HKID (placa de identidade de Hong Kong)

### <a name="format"></a>Formatar

Combinação de 8-9 letras e números, além de parênteses opcionais em relação ao caractere final

### <a name="pattern"></a>Padrão

Combinação de 8-9 letras:

- 1-2 letras (não diferencia maiúsculas de minúsculas)
- Seis dígitos
- O caractere final (qualquer dígito ou a letra a), que é o dígito de verificação e é opcionalmente colocado entre parênteses.

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_hong_kong_id_card"></a>\_Placa de ID de Hong Kong de palavra-chave \_ \_ \_

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>Endereço IP

### <a name="format"></a>Formatar

#### <a name="ipv4"></a>IPv4:

Padrão complexo, que conta para versões formatadas (períodos) e não formatados (sem pontos) dos endereços IPv4

#### <a name="ipv6"></a>IPv6:

Padrão complexo que conta para números IPv6 formatados (que incluem dois-pontos)

### <a name="pattern"></a>Padrão

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_ipaddress"></a>IPAddress de palavra-chave \_

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Número de conta permanente da Índia (PAN)

### <a name="format"></a>Formatar

10 letras ou dígitos

### <a name="pattern"></a>Padrão

10 letras ou dígitos:

- Três letras (não diferencia maiúsculas de minúsculas)
- Uma letra em C, P, H, F, A, T, B, L, J, G (não diferencia maiúsculas de minúsculas)
- Uma letra
- Quatro dígitos
- Uma letra (não diferencia maiúsculas de minúsculas)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_india_permanent_account_number"></a>Palavra-chave \_ \_ número da conta permanente da Índia \_ \_

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Número da Aadhaar (identificação exclusiva da Índia)

### <a name="format"></a>Formatar

12 dígitos contendo espaços opcionais ou traços

### <a name="pattern"></a>Padrão

12 dígitos:

- Um dígito, que não é 0 ou 1
- Três dígitos
- Um espaço ou Dash opcional
- Quatro dígitos
- Um espaço ou Dash opcional
- O dígito final, que é o dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_india_aadhar"></a>Palavra-chave \_ Índia \_ aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>Número do KTP (cartão de identidade da Indonésia)

### <a name="format"></a>Formatar

16 dígitos contendo períodos opcionais

### <a name="pattern"></a>Padrão

16 dígitos:

- Código de província de dois dígitos
- Um ponto (opcional)
- Código de Regency ou cidade de dois dígitos
- Código do subdistrito de dois dígitos
- Um ponto (opcional)
- Seis dígitos no formato DDMMYY, que são a data de nascimento
- Um ponto (opcional)
- Quatro dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_indonesia_id_card"></a>Placa de identificação da palavra-chave da \_ Indonésia \_ \_

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>IBAN (número de conta bancária internacional)

### <a name="format"></a>Formatar

Código do país (duas letras) mais os dígitos de verificação (dois dígitos) mais o :::no-loc text="bban"::: número (até 30 caracteres)

### <a name="pattern"></a>Padrão

O padrão deve incluir todos os seguintes itens:

- Código do país de duas letras
- Dois dígitos de verificação (seguidos por um espaço opcional)
- 1-7 grupos de quatro letras ou dígitos (podem ser separados por espaços)
- 1-3 letras ou dígitos

O formato de cada país é ligeiramente diferente. O tipo de informações confidenciais do IBAN cobre estes 60 países:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Palavras-chave

Nenhum

## <a name="ireland-personal-public-service-pps-number"></a>Número do PPS (serviço público pessoal) da Irlanda

### <a name="format"></a>Formatar

Formato antigo (até 31 de dezembro de 2012):

- sete dígitos seguidos por 1-2 letras

Novo formato (1 Jan 2013 e posterior):

- sete dígitos seguidos de duas letras

### <a name="pattern"></a>Padrão

Formato antigo (até 31 de dezembro de 2012):

- sete dígitos
- uma a duas letras (não diferencia maiúsculas de minúsculas)

Novo formato (1 Jan 2013 e posterior):

- sete dígitos
- uma letra (não diferencia maiúsculas de minúsculas), que é um dígito de verificação alfabética
- Uma letra opcional no intervalo de A-I ou &quot; W&quot;

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_ireland_eu_national_id_card"></a>Palavras-chave \_ Ireland \_ UE \_ National \_ ID \_ Card

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Número da conta bancária de Israel

### <a name="format"></a>Formatar

13 dígitos

### <a name="pattern"></a>Padrão

Binário

- dois dígitos
- um traço
- três dígitos
- um traço
- oito dígitos

Não formatado:

- 13 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_israel_bank_account_number"></a>Palavra-chave de \_ \_ conta bancária Israel \_ \_

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Número de identificação nacional de Israel

### <a name="format"></a>Formatar

nove dígitos

### <a name="pattern"></a>Padrão

nove dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_israel_national_id"></a>\_ \_ ID nacional de palavra-chave Israel \_

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Número da licença do driver da Itália

Essa entidade de tipo de informações confidenciais está incluída no tipo de informação confidencial do número de licença do driver da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

uma combinação de 10 letras e dígitos

### <a name="pattern"></a>Padrão

uma combinação de 10 letras e dígitos:

- uma letra (não diferencia maiúsculas de minúsculas)
- a letra &quot; a &quot; ou &quot; V &quot; (não diferencia maiúsculas de minúsculas)
- sete dígitos
- uma letra (não diferencia maiúsculas de minúsculas)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_italy_drivers_license_number"></a>Palavra-chave \_ número de licença de drivers de Itália \_ \_ \_

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>Número da conta bancária do Japão

### <a name="format"></a>Formatar

sete ou oito dígitos

### <a name="pattern"></a>Padrão

número da conta bancária:

- sete ou oito dígitos
- código da ramificação da conta bancária:
- quatro dígitos
- um espaço ou Dash (opcional)
- três dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_jp_bank_account"></a>\_ \_ Conta bancária JP de palavra-chave \_

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>\_Código da \_ \_ ramificação do Banco JP de palavra-chave \_

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Número da licença do driver do Japão

### <a name="format"></a>Formatar

12 dígitos

### <a name="pattern"></a>Padrão

12 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_jp_drivers_license_number"></a>\_Número de \_ licença de drivers \_ \_ de palavra-chave JP

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>Número do passaporte do Japão

### <a name="format"></a>Formatar

duas letras seguidas por sete dígitos

### <a name="pattern"></a>Padrão

duas letras (não diferencia maiúsculas de minúsculas) seguidas por sete dígitos

#### <a name="keyword_jp_passport"></a>Palavra-chave \_ JP \_ Passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Número do cartão de residência do Japão

### <a name="format"></a>Formatar

12 letras e dígitos

### <a name="pattern"></a>Padrão

12 letras e dígitos:

- duas letras (não diferencia maiúsculas de minúsculas)
- oito dígitos
- duas letras (não diferencia maiúsculas de minúsculas)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_jp_residence_card_number"></a>\_Número da \_ placa de residência JP \_ de palavra-chave \_

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Número de registro residente no Japão

### <a name="format"></a>Formatar

11 dígitos

### <a name="pattern"></a>Padrão

11 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_jp_resident_registration_number"></a>\_Número de \_ \_ registro residente \_ da palavra-chave JP

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Número de seguro social do Japão (SIN)

### <a name="format"></a>Formatar

7-12 dígitos

### <a name="pattern"></a>Padrão

7-12 dígitos:

- quatro dígitos
- um hífen (opcional)
- seis dígitos ou
- 7-12 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_jp_sin"></a>Palavra-chave \_ JP \_ sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>Número do cartão de identificação da Malásia

### <a name="format"></a>Formatar

12 dígitos contendo hifens opcionais

### <a name="pattern"></a>Padrão

12 dígitos:

- seis dígitos no formato YYMMDD, que são a data de nascimento
- um traço (opcional)
- código de ponto de nascimento de duas letras
- um traço (opcional)
- três dígitos aleatórios
- código de gênero de um dígito

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_malaysia_id_card_number"></a>Palavra-chave \_ ID da Malásia \_ \_ número do cartão \_

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>Número do serviço do cidadão (BSN) dos Países Baixos

### <a name="format"></a>Formatar

oito a nove dígitos contendo espaços opcionais

### <a name="pattern"></a>Padrão

oito a nove dígitos:

- três dígitos
- um espaço (opcional)
- três dígitos
- um espaço (opcional)
- dois-três dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_netherlands_eu_national_id_card"></a>Palavras-chave \_ Holanda \_ UE \_ \_ cartão de ID nacional \_

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>Ministério da Nova Zelândia do número de saúde

### <a name="format"></a>Formatar

três letras, um espaço (opcional) e quatro dígitos

### <a name="pattern"></a>Padrão

- três letras (não diferencia maiúsculas de minúsculas), exceto ' I ' e ' O '
- um espaço (opcional)
- quatro dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_nz_terms"></a>Termos de palavra-chave \_ NZ \_

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Número de identificação da Noruega

### <a name="format"></a>Formatar

11 dígitos

### <a name="pattern"></a>Padrão

11 dígitos:

- seis dígitos no formato DDMMYY, que são a data de nascimento
- número individual de três dígitos
- dois dígitos de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_norway_id_number"></a>Palavra-chave \_ Noruega \_ número de ID \_

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Filipinas número de identificação de multipropósito unificado

### <a name="format"></a>Formatar

12 dígitos separados por hifens

### <a name="pattern"></a>Padrão

12 dígitos:

- quatro dígitos
- um hífen
- sete dígitos
- um hífen
- um dígito

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_philippines_id"></a>ID da palavra-chave \_ \_

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>Cartão de identidade da Polônia

### <a name="format"></a>Formatar

três letras e seis dígitos

### <a name="pattern"></a>Padrão

três letras (não diferencia maiúsculas de minúsculas) seguidas por seis dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_poland_national_id_passport_number"></a>Palavra-chave \_ Polônia \_ National \_ ID \_ passaporte \_ número

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>ID nacional da Polônia (PESEL)

### <a name="format"></a>Formatar

11 dígitos

### <a name="pattern"></a>Padrão

- 6 dígitos representando a data de nascimento no formato YYMMDD
- 4 dígitos
- 1 dígito de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_pesel_identification_number"></a>Número de identificação da palavra-chave \_ PESEL \_ \_

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Número do Passport da Polônia

Essa entidade de tipo de informações confidenciais está incluída no tipo de informações confidenciais do número do Passport da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

duas letras e sete dígitos

### <a name="pattern"></a>Padrão

Duas letras (não diferencia maiúsculas de minúsculas) seguidas por sete dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_poland_national_id_passport_number"></a>Palavra-chave \_ Polônia \_ National \_ ID \_ passaporte \_ número

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>Número do cartão do cidadão de Portugal

### <a name="format"></a>Formatar

oito dígitos

### <a name="pattern"></a>Padrão

oito dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_portugal_citizen_card"></a>Palavra-chave de \_ cidadão de Portugal \_ \_

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Número da licença do driver Portugal

Essa entidade de tipo de informações confidenciais só está disponível no tipo de informação confidencial do número de licença do driver da UE.

### <a name="format"></a>Formatar

dois padrões-duas letras seguidas por 5-8 dígitos com caracteres especiais

### <a name="pattern"></a>Padrão

Padrão 1: duas letras seguidas por 5/6 com caracteres especiais:

- Duas letras (não diferencia maiúsculas de minúsculas)
- Um hífen
- Cinco ou seis dígitos
- Um espaço
- Um dígito

Padrão 2: uma letra seguida por 6/8 dígitos com caracteres especiais:

- Uma letra (não diferencia maiúsculas de minúsculas)
- Um hífen
- Seis ou oito dígitos
- Um espaço
- Um dígito

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_eu_drivers_license_number"></a>O \_ \_ número da carteira do driver da UE \_ \_

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>\_ \_ \_ Número da licença do driver da UE \_ em palavras-chave \_

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>ID nacional da Arábia Saudita

### <a name="format"></a>Formatar

10 dígitos

### <a name="pattern"></a>Padrão

10 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_saudi_arabia_national_id"></a>ID nacional da palavra-chave \_ Arábia \_ Saudita \_ \_

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>Número do NRIC (cartão de identidade de Registro Nacional) de Cingapura

### <a name="format"></a>Formatar

nove letras e dígitos

### <a name="pattern"></a>Padrão

- nove letras e dígitos:
- a letra &quot; F &quot; , &quot; G &quot; , &quot; S &quot; ou &quot; T &quot; (não diferencia maiúsculas de minúsculas)
- sete dígitos
- um dígito de verificação alfabética

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_singapore_nric"></a>Palavra-chave \_ Cingapura \_ NRIC

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>Número de identificação da África do Sul

### <a name="format"></a>Formatar

13 dígitos que podem conter espaços

### <a name="pattern"></a>Padrão

13 dígitos:

- seis dígitos no formato YYMMDD, que são a data de nascimento
- quatro dígitos
- um indicador de cidadania de dígito único
- o dígito &quot; 8 &quot; ou &quot; 9&quot;
- um dígito, que é um dígito de soma de verificação

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_south_africa_identification_number"></a>Palavra-chave \_ número de identificação da África do Sul \_ \_ \_

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Número de registro residente da Coreia do Sul

### <a name="format"></a>Formatar

13 dígitos contendo um hífen

### <a name="pattern"></a>Padrão

13 dígitos:

- seis dígitos no formato YYMMDD, que são a data de nascimento
- um hífen
- um dígito determinado pelo século e sexo
- código de região de nascimento de quatro dígitos
- um dígito usado para diferenciar as pessoas para as quais os números anteriores são idênticos
- um dígito de verificação.

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_south_korea_resident_number"></a>Palavra-chave \_ \_ \_ número residente da Coreia do Sul \_

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>Número do seguro social da Espanha (SSN)

Essa entidade de tipo de informações confidenciais está incluída no número de segurança social da UE ou no tipo de informações confidenciais da ID equivalente e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

11-12 dígitos

### <a name="pattern"></a>Padrão

11-12 dígitos:

- dois dígitos
- uma barra (opcional)
- sete a oito dígitos
- uma barra (opcional)
- dois dígitos

### <a name="keywords"></a>Palavras-chave

Nenhum

## <a name="sweden-national-id"></a>ID nacional da Suécia

### <a name="format"></a>Formatar

10 ou 12 dígitos e um delimitador opcional

### <a name="pattern"></a>Padrão

10 ou 12 dígitos e um delimitador opcional:

- dois dígitos (opcional)
- Seis dígitos no formato de data YYMMDD
- delimitador de &quot; - &quot; ou &quot; + &quot; (opcional)
- quatro dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_swedish_national_identifier"></a>Identificador nacional de palavras-chave \_ sueca \_ \_

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Número do passaporte da Suécia

Essa entidade de tipo de informações confidenciais está incluída no tipo de informações confidenciais do número do Passport da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

oito dígitos

### <a name="pattern"></a>Padrão

oito dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_sweden_passport"></a>Palavra-chave \_ Suécia \_ Passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Palavra-chave \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>Código SWIFT

### <a name="format"></a>Formatar

quatro letras seguidas por 5-31 letras ou dígitos

### <a name="pattern"></a>Padrão

quatro letras seguidas por 5-31 letras ou dígitos:

- código bancário de quatro letras (não diferencia maiúsculas de minúsculas)
- um espaço opcional
- 4-28 letras ou dígitos (o número da conta bancária básica (BBAN))
- um espaço opcional
- uma a três letras ou dígitos (restante do BBAN)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_swift"></a>Palavra-chave \_ Swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>Número de identificação nacional de Taiwan

### <a name="format"></a>Formatar

uma letra (em inglês) seguida de nove dígitos

### <a name="pattern"></a>Padrão

uma letra (em inglês) seguida por nove dígitos:

- uma letra (em inglês, não diferencia maiúsculas de minúsculas)
- o dígito &quot; 1 &quot; ou &quot; 2&quot;
- oito dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_taiwan_national_id"></a>\_ \_ ID nacional de Taiwan de palavra-chave \_

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Número de passaporte de Taiwan

### <a name="format"></a>Formatar

- número do passaporte biométrico: nove dígitos
- número de passaporte não biométrico: nove dígitos

### <a name="pattern"></a>Padrão

número do passaporte biométrico:

- o caractere &quot; 3&quot;
- oito dígitos

número de passaporte não biométrico:

- nove dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_taiwan_passport"></a>Palavra-chave de \_ Taiwan do \_ Passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Número de certificado residente (ARC/TARC) de Taiwan

### <a name="format"></a>Formatar

10 letras e dígitos

### <a name="pattern"></a>Padrão

10 letras e dígitos:

- duas letras (não diferencia maiúsculas de minúsculas)
- oito dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_taiwan_resident_certificate"></a>Palavra-chave de \_ \_ certificado residente em Taiwan \_

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>Inglaterra número da licença do driver

Essa entidade de tipo de informações confidenciais está incluída no tipo de informação confidencial do número de licença do driver da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

Combinação de 18 letras e dígitos no formato especificado

### <a name="pattern"></a>Padrão

18 letras e dígitos:

- cinco letras (não diferencia maiúsculas de minúsculas) ou o dígito &quot; 9 &quot; no lugar de uma letra
- um dígito
- cinco dígitos no formato de data MMDDY para data de nascimento (o sétimo caractere é incrementado por 50 se o driver for feminino, ou seja, 51 a 62 em vez de 01 a 12)
- duas letras (não diferencia maiúsculas de minúsculas) ou o dígito &quot; 9 &quot; no lugar de uma letra
- cinco dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_uk_drivers_license"></a>Palavra-chave de \_ \_ licença de drivers UK \_

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>Inglaterra número de reversão do eleitoral

### <a name="format"></a>Formatar

duas letras seguidas por 1-4 dígitos

### <a name="pattern"></a>Padrão

duas letras (não diferencia maiúsculas de minúsculas) seguidas por números de 1-4

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_uk_electoral"></a>Palavra-chave \_ UK \_ eleitoral

- indicação do Conselho
- formulário de nominação
- registro de eleitoral
- eleitoral

## <a name="uk-national-health-service-number"></a>Inglaterra número do serviço de integridade nacional

### <a name="format"></a>Formatar

10-17 dígitos separados por espaços

### <a name="pattern"></a>Padrão

10-17 dígitos:

- três ou 10 dígitos
- um espaço
- três dígitos
- um espaço
- quatro dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_uk_nhs_number"></a>Palavra-chave \_ \_ NHS número do Reino Unido \_

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Palavra-chave \_ \_ NHS Número1 do Reino Unido \_

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Palavra-chave \_ UK \_ NHS \_ Number \_ DOB

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>Inglaterra número de seguro nacional (NINO)

Essa entidade de tipo de informações confidenciais está incluída no tipo de informações confidenciais do número de identificação nacional da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

sete caracteres ou nove caracteres separados por espaços ou traços

### <a name="pattern"></a>Padrão

dois padrões possíveis:

- duas letras (NINOs válidas usam apenas determinados caracteres neste prefixo, que esse padrão valida; não diferencia maiúsculas de minúsculas)
- seis dígitos
- ' A ', ' B ', ' C' ou ' d' (como o prefixo, somente determinados caracteres são permitidos no sufixo; não diferencia maiúsculas de minúsculas)

OU

- duas letras
- um espaço ou um traço
- dois dígitos
- um espaço ou um traço
- dois dígitos
- um espaço ou um traço
- dois dígitos
- um espaço ou um traço
- ' A ', ' B ', ' C' ou ' d'


### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_uk_nino"></a>Palavra-chave \_ UK \_ Nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>Inglaterra Número de referência de contribuidor exclusivo

Esse tipo de informações confidenciais só está disponível para uso no:

- políticas de prevenção de perda de dados
- políticas de conformidade de comunicação
- Governança de informações
- gerenciamento de registros
- Microsoft Cloud app Security

### <a name="format"></a>Formatar

10 dígitos sem espaços e delimitadores

### <a name="pattern"></a>Padrão

10 dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keywords_uk_eu_tax_file_number"></a>Palavras-chave \_ \_ número do \_ arquivo de imposto da UE no \_ Reino Unido \_

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>Número da conta bancária dos EUA

### <a name="format"></a>Formatar

6-17 dígitos

### <a name="pattern"></a>Padrão

6-17 dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_usa_bank_account"></a>\_ \_ Conta bancária de palavra-chave usa \_

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>Número da licença do driver dos EUA

### <a name="format"></a>Formatar

Depende do estado

### <a name="pattern"></a>Padrão

depende do estado – por exemplo, Nova York:

- nove dígitos formatados como DDD serão correspondentes.
- nove dígitos como ddddddddd não serão correspondentes.

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_us_drivers_license_abbreviations"></a>\_ \_ \_ Abreviações de licença de drivers dos EUA \_

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Licença de drivers de palavra-chave \_ US \_ \_

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Palavra-chave \_ [nome do estado \_ ] \_ nome da licença de drivers \_ \_

- abreviação de estado (por exemplo, &quot; NY &quot; )
- nome do estado (por exemplo, &quot; Nova York &quot; )

## <a name="us-individual-taxpayer-identification-number-itin"></a>Número de identificação de contribuinte (certa) dos EUA

### <a name="format"></a>Formatar

nove dígitos que começam com um &quot; 9 &quot; e contêm um &quot; 7 &quot; ou &quot; 8 &quot; como o quarto dígito, opcionalmente formatado com espaços ou traços

### <a name="pattern"></a>Padrão

binário

- o dígito &quot; 9&quot;
- dois dígitos
- um espaço ou um traço
- um &quot; 7 &quot; ou &quot; 8&quot;
- um dígito
- um espaço, ou Dash
- quatro dígitos

não formatado

- o dígito &quot; 9&quot;
- dois dígitos
- um &quot; 7 &quot; ou &quot; 8&quot;
- cinco dígitos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_itin"></a>Palavra-chave \_ certa

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>Número do seguro social (SSN) dos EUA

### <a name="format"></a>Formatar

nove dígitos, que podem estar em um padrão formatado ou não formatado

>[!Note]
> Se for emitido antes de meados de 2011, um SSN terá formatação forte, onde determinadas partes do número devem estar dentro de determinados intervalos para serem válidas (mas não há nenhuma soma de verificação).
>

### <a name="pattern"></a>Padrão

quatro funções procuram CPFs em quatro padrões diferentes:

- Func \_ SSN localiza CPFs com formatação forte 2011 que são formatadas com traços ou espaços (DDD-DD-dddd ou DDD DD dddd)
- Func não \_ formatado \_ SSN localiza CPFs com formatação forte 2011 que não é formatada como nove dígitos consecutivos (ddddddddd)
- O functed \_ \_ formated \_ SSN localiza CPFs de pós-2011 formatados com traços ou espaços (DDD-DD-dddd ou DDD DD dddd)
- \_O SSN aleatório não formatado de Func \_ \_ localiza CPFs de pós-2011 que não são formatados como nove dígitos consecutivos (ddddddddd)

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_ssn"></a>Palavra-chave \_ SSN

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>EUA/REINO UNIDO número do passaporte

O Reino Unido a entidade do tipo de informações confidenciais do número do Passport está disponível no tipo de informações confidenciais do número do Passport da UE e está disponível como uma entidade de tipo de informações confidenciais autônoma.

### <a name="format"></a>Formatar

nove dígitos

### <a name="pattern"></a>Padrão

nove dígitos consecutivos

### <a name="keywords"></a>Palavras-chave

#### <a name="keyword_passport"></a>Palavra-chave \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
