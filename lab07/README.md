# Lab 07
## Exercício 1

Calcule o Pagerank do exemplo da Wikipedia em Cypher:

~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/network/pagerank/pagerank-wikipedia.csv' AS line
MERGE (p1:Page {name:line.source})
MERGE (p2:Page {name:line.target})
CREATE (p1)-[:LINKS]->(p2)

CALL gds.graph.create(
  'wikiGraph',
  'Page',
  'LINKS'
)

CALL gds.pageRank.stream('wikiGraph')
YIELD nodeId, score
MATCH (p:Page {name: gds.util.asNode(nodeId).name})
SET p.pagerank = score

CALL gds.pageRank.stream('wikiGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name, score AS pagerank
~~~

> Eu tentei criar a representação visual da tabela, porém os programas não abriram, então coloquei uma imagem da Tabela do Neo4j mesmo:

![](https://github.com/TheRomito/DataBaseLabs_MC536/blob/master/lab07/imagem_wikiGraph.png)

## Exercício 2

Departing from a Drug-Drug graph created in a previous lab, whose relationship determines drugs taken together, apply a community detection in it to see the results:

~~~cypher
CALL gds.graph.create(
  'FAERSGraph',
  'Drug',
  {
    Relates: {
      orientation: 'UNDIRECTED'
    }
  }
)

CALL gds.louvain.stream('FAERSGraph')
YIELD nodeId, communityId
RETURN gds.util.asNode(nodeId).name AS name, communityId
ORDER BY communityId ASC

CALL gds.louvain.stream('FAERSGraph')
YIELD nodeId, communityId
MATCH (d:Drug {name: gds.util.asNode(nodeId).name})
SET d.community = communityId

CALL gds.louvain.stream('FAERSGraph')
YIELD nodeId, communityId
RETURN gds.util.asNode(nodeId).name AS name, communityId
~~~

> Mesmo problema do exercício acima.

![]()
