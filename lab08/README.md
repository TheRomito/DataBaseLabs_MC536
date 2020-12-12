## LAB 08 - PubChem e DRON com XQuery/XPath

## Questão 1
Construa uma comando SELECT que retorne dados equivalentes a este XPath
~~~xquery
//individuo[idade>20]/@nome
~~~

### Resolução
~~~xquery
/fichario/individuo[idade>20]/@nome
~~~

## Questão 2
Qual a outra maneira de escrever esta query sem o where?

~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')
 
for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
~~~
### Resolução
~~~xquery
for $i in ($fichariodoc//individuo[idade>17])
return {data($i/@nome)}
~~~

## Questão 3
Escreva uma consulta SQL equivalente ao XQuery:
~~~xquery
let $fichariodoc := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/xml/fichario.xml')

for $i in ($fichariodoc//individuo)
where $i[idade>17]
return {data($i/@nome)}
~~~

### Resolução
~~~sql
SELECT nome FROM Individuo WHERE idade > 17;
~~~

## Questão 4
Retorne quantas publicações são posteriores ao ano de 2011.

### Resolução
~~~xquery
let $publications:= doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

return count($publications//publication[year>2011])
~~~

## Questão 5
Retorne a categoria cujo `<label>` em inglês seja 'e-Science Domain'.

### Resolução
~~~xquery
let $publications:= doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

let $p := ($publications//category[label='e-Science Domain'])
return {$p}
~~~

## Questão 6
Retorne as publicações associadas à categoria cujo `<label>` em inglês seja 'e-Science Domain'. A associação entre o label e a key da categoria deve ser feita na consulta.

### Resolução
~~~xquery
let $publications:= doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/publications/publications.xml')

let $c := ($publications//category[label='e-Science Domain'])
for $p in ($publications//publication)
where $p/key = $c/@key
return {'Título publicacao:', $p/title/text(), '&#xa;'}
~~~

## Tarefas com DRON e PubChem

## Questão 1

Liste o nome de todas as classificações que estão apenas dois níveis imediatamente abaixo da raiz.

### Resolução
~~~xquery
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')

for $i in ($dron/*/*/*)
return {data($i/@name)}
~~~

## Questão 2

Apresente todas as classificações de um componente a sua escolha (diferente de `Acetylsalicylic Acid`) que estejam hierarquicamente dois níveis acima. Note que no exemplo dado em sala foi considerado um nível hierárquico acima.

### Resolução
~~~xquery
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')

let $e := ($dron//drug[drug/drug/@name='OLMESARTAN'])

return {data($e/@name)}
~~~

ou, solução dois:

~~~xquery
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')

for $e in ($dron//drug[drug/drug/@name='OLMESARTAN'])
return {data($e/@name), '&#xa;'}
~~~

## Questão 3

### Questão 3.1

Liste todos os códigos ChEBI dos componentes disponíveis.

#### Resolução
~~~xquery
let $pubchem:= doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')

for $i in ($pubchem//Synonym)
let $aux := substring($i/text(), 1, 5)
where $aux = 'CHEBI'
return {substring($i/text(), 7)}
~~~

### Questão 3.2

Liste todos os códigos ChEBI e primeiro nome (sinônimo) de cada um dos componentes disponíveis.

#### Resolução
~~~xquery
let $pubchem:= doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')

for $i in ($pubchem//Information)
for $j in ($i/Synonym)
let $aux := substring($j/text(), 1, 5)
where $aux = 'CHEBI'
return {$i/Synonym[1]/text(),  substring($j/text(), 7), '&#xa;'}
~~~

### Questão 3.3

Para cada código ChEBI, liste os sinônimos e o nome que aparece para o mesmo componente no DRON (se existir). Para isso faça um JOIN com o DRON.

#### Resolução
~~~xquery
let $pubchem := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/pubchem/pubchem-chebi-synonyms.xml')
let $dron := doc('https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017-dron/dron.xml')

for $i in ($pubchem//Information)
for $j in ($i/Synonym)
let $aux := substring($j/text(), 1, 5)
where $aux = 'CHEBI'
for $p in ($j),
    $d in ($dron//drug)
where substring($d/@id, 38) = substring($j/text(), 7)
return {'Código CHEBI:', data(substring($d/@id, 38)), '&#xa;', data($d/@name), data($i), '&#xa;'}
~~~
