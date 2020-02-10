Graph, colection of node and relationships


Pessoa, Predio, Veiculo
Pessoa possui(OWNER) Predio
Carro estacionado em Predio


Properties

não suporta nested objects

-- Criar Favoritos

//Comentário com o nome
MATCH (n) RETURN n LIMIT 3

MATCH (anyone:Person) RETURN anyone Limit 5

Relação

MATCH (actor:Person)--(movie:Movie)

MATCH (actor:Person)-[rel:ACTED_IN]->(movie:Movie)
RETURN actor, rel, movie
LIMIT 1


MATCH (movie:Movie)
MATCH (director:Person)-[:DIRECTED]->(movie)
MATCH (director)-[:ACTED_IN]->(movie)
RETURN movie.title, director.name

//OPTIONAL = if

MATCH (movie:Movie)
OPTIONAL MATCH (director:Person)-[:DIRECTED]->(movie)<-[:ACTED_IN]-(director)
RETURN movie.title, director.name


//Filter tom hanks
//Mais parametros podem ser passados no objeto person

MATCH (tom:Person{name: 'Tom Hanks'})
Return tom
limit 1

//O mesmo que o de cima, escrito de maneira diferente
MATCH (tom:Person)
where tom.name = 'Tom Hanks'
Return tom
limit 1


MATCH (someone:Person)
where someone.born <> 1956
Return someone
limit 5

//OR AND IN NOT

MATCH (someone:Person)
where someone.born = 1955 OR someone.born IN [1956,1957,1958]
Return someone
limit 5


//Expressão regular

MATCH (movie:Movie)
WHERE movie.title =~ 'The.*'
RETURN movie.title


//Transform Results

WHERE movie.title = 'Top Gun'
RETURN actor.name AS Name, role.earnings AS Earnt
ORDER BY role.earnings DESC
SKIP 3
LIMIT 3

//DISTINCT

MATCH (actor:Person)-[role:ACTED_IN]=>(:Movie) 
where role.earnings > 100000000 
return DISTINCT actor.name

//Funções string

trim
replace("string", "s", "r")
upper