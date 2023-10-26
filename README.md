# Capture the flag - Chall CatchemAll
Write up do chall CatchemAll
![Dale](https://github.com/dahiwas/ctf_catchemall/blob/main/ImagensCTF/CatchemAll.jpeg)

O desafio fornecia apenas a imagem abaixo como base:

![Dale](https://github.com/dahiwas/ctf_catchemall/blob/main/ImagensCTF/FigureCatchemAl.jpeg)

Dessa forma, fomos encorajados a assimilar cada pokemon a seu respectivo número na pokedex, por exemplo:

O primeiro pokemon da lista dada é o Kingler, então, buscamos na PokeDex(PokeAgenda) o número que o representa e guardamos seu número
![Dale](https://github.com/dahiwas/ctf_catchemall/blob/main/ImagensCTF/Pokedex.png)
E fizemos com todos os pokemons do que nos foi fornecido. Chegando na seguinte lista:

```
099 072 074 112 098 110 081 111 073 108
085 121 082 110 078 107 082 049 090 114
087 068 069 052 089 083 115 120 085 069
108 104 097 106 086 086 082 108 074 110
089 087 053 081 084 048 112 082 086 109
057 051 097 107 073 114 097 048 074 066
084 048 100 077 084 122 074 112 086 048
100 082 085 067 115 118 083 088 074 120
100 071 090 082 097 072 103 120 083 070
090 119 083 087 077 105 075 081 111 061
```

Com essa tabela em mãos, transformamos esses números decimais para o alfabeto ASCII:

```
#Python - transformando decimal no alfabeto ASCII
codigo = """
099 072 074 112 098 110 081 111 073 108
085 121 082 110 078 107 082 049 090 114
087 068 069 052 089 083 115 120 085 069
108 104 097 106 086 086 082 108 074 110
089 087 053 081 084 048 112 082 086 109
057 051 097 107 073 114 097 048 074 066
084 048 100 077 084 122 074 112 086 048
100 082 085 067 115 118 083 088 074 120
100 071 090 082 097 072 103 120 083 070
090 119 083 087 077 105 075 081 111 061
"""

linhas = codigo.strip().split('\n')

caracteres = ''.join([chr(int(numero)) for linha in linhas for numero in linha.split()])
print(caracteres)
~
```
Dá uma saída:
```
cHJpbnQoIlUyRnNkR1ZrWDE4YSsxUElhajVVRlJnYW5QT0pRVm93akIra0JBT0dMTzJpV0dRUCsvSXJxdGZRaHgxSFZwSWMiKQo=
```
Certo, agora vemos que é uma cifra de base64, o convertemos para UTF-8 com o seguinte código:

```
#Python - Conversão BASE64 - UTF-8
import base64

codigo_base64 = "cHJpbnQoIlUyRnNkR1ZrWDE4YSsxUElhajVVRlJnYW5QT0pRVm93akIra0JBT0dMTzJpV0dRUCsvSXJxdGZRaHgxSFZwSWMiKQo="

# transformando a sequência Base64 em bytes
bytes_decodificados = base64.b64decode(codigo_base64)

# convertendo os bytes em uma string UTF-8
texto_utf8 = bytes_decodificados.decode('utf-8')

print(texto_utf8)
```
O que nos gerou o resultado:
```
print("U2FsdGVkX18a+1PIaj5UFRganPOJQVowjB+kBAOGLO2iWGQP+/IrqtfQhx1HVpIc")'
```
Se fizermos uma dupla conversão, ou seja, se convertermos 'U2FsdGVkX18a+1PIaj5UFRganPOJQVowjB+kBAOGLO2iWGQP+/IrqtfQhx1HVpIc' utilizando o mesmo processo, temos como saída:
```
Salted__ыSИj>Tњу‰AZ0Њ¤†,нўXdыт+ЄЧР‡GV’)'
```
Pesquisando um pouco acerca, descobrimos em: https://asecuritysite.com/symmetric/aes_python, que esse salted é um tipo de criptografia e que seria possível utilizar a biblioteca openssl para decodificar, além disso, um dos parâmetros para se utilizar a decodificação por essa biblioteca, é a utilização de uma key, senha.

A descrição inicial do challenge dizia: 'Pokemon is the asnwer for everything', e portanto, colocamos a palavra 'pokemon' como password do código.
Por fim, colocamos 'U2FsdGVkX18a+1PIaj5UFRganPOJQVowjB+kBAOGLO2iWGQP+/IrqtfQhx1HVpIc' em um arquivo txt chamado cipherSecret.txt e rodamos o seguinte o código:
```
$ openssl aes-256-cbc -d -a -pass pass:pokemon -in cipherSecret.txt
```
E temos como resultado a flag que buscavamos:
```
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
flag{4Tr4p4l0Z_Y444}
```
Finalizando o desafio.
