Transações principais (Máximo de transações permitidas em 10 segundos, por cofre, por região<sup>1</sup>):

|Tipo de chave|Chave HSM<br>Chave CREATE|Chave HSM<br>Todas as outras transações|Chave de software<br>Chave CREATE|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA de 2048 bits|5|1000|10|2000|
|RSA de 3072 bits|5|250|10|500|
|RSA de 4096 bits|5|125|10|250|
|

Transações de cofre, Segredos e Chaves de Conta de Armazenamento Gerenciadas:
| Tipos de transação | Máximo de transações permitidas em 10 segundos, por cofre, por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2000 |
|

<sup>1</sup> Há um limite que afeta toda a assinatura para todos os tipos de transação, que é 5 vezes por limite do cofre de chaves. Por exemplo, HSM- outras transações por assinatura são limitadas a 5000 transações em 10 segundos por assinatura.
