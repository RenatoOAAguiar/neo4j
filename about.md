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


//Funções numéricas
RETURN floor(0.9)  = 0
RETURN floor(2.1)  = 2
RETURN ceil(0.9)  = 1
RETURN round(0.49)  = 0


//CREATE

CREATE ()  // Cria um node
CREATE (:Cat) // Cria um node com label cat
CREATE (cat:Cat:Animal) // Cria um node com labels cat e animal
RETURN cat

CREATE (cat:Cat:Animal{sound: 'meow', eats: 'birds'})
RETURN cat


//CREATE RELATIONSHIP

CREATE (cat:Cat{name: 'Fluffy'})-[:GROOMS]->(cat)

CREATE (joe:Bunny:Animal{name: 'JOse Bunny'}),
(sarah:Bunny:Animal{name: 'Sarah Bunny'})
RETURN joe, sarah

//Se executada novamente vai repetir
MATCH (joe:Bunny{name: 'JOse Bunny'}),
(sarah:Bunny{name: 'Sarah Bunny'})
CREATE (joe)-[:LIKES]->(sarah), (joe)<-[:LIKES]-(sarah)
RETURN joe, sarah

MATCH (joe:Bunny{name: 'JOse Bunny'}),
(sarah:Bunny{name: 'Sarah Bunny'})
MERGE (joe)-[:LIKES]->(sarah)
MERGE (joe)<-[:LIKES]-(sarah)
RETURN joe, sarah

MATCH (joe:Bunny{name: 'JOse Bunny'}),
(sarah:Bunny{name: 'Sarah Bunny'})
MERGE (joe)-[:LIKES]->(sarah)
MERGE (joe)<-[:LIKES]-(sarah)
MERGE (foxy:Fox:Animal{name: 'Foxy fox'})-[:LIKES]->(sarah)
RETURN joe, sarah


CREATE (movie:Movie{title: 'The Hateful Eight'}),
(quentin:Person{name: 'Quentin Tarantino'}),
(quentin)-[:DIRECTED]->(movie)
RETURN quentin, movie

CREATE  (zoe:Person{name: 'Zoe Bell', born: 1978})
RETURN zoe

MATCH  (zoe:Person{name: 'Zoe Bell', born: 1978}), 
(quentin:Person{name: 'Quentin Tarantino'}),
(movie:Movie{title: 'The Hateful Eight'})
CREATE (quentin)-[:HAS_CONTACT]->(zoe),
(zoe)-[:ACTED_IN{earnings: 10000000}]->(movie)
RETURN zoe,quentin, movie


//Deletar nodes
//Para deletar nodes, necessário deletar as relações que ele pode ter

MATCH (node)
DELETE node

MATCH (node)-[rel]-()
DELETE node, rel

MATCH (node)
OPTIONAL MATCH (node)-[rel]-()
DELETE node, rel


//Update SET
MATCH (fan:Person)-[w:WATCHED]->(movie)
WHERE fan.name = 'Mikey'
SET w.rating = 5

//Remove label
MATCH (fan:Person)-[w:WATCHED]->(movie)
WHERE fan.name = 'Mikey'
REMOVE w.rating

//Valores Dinamicos
MATCH (tom:Person{name: 'Tom Hanks'})-[:HAS_CONTACT]->(contact)
WITH tom, count(contact) AS num_of_contacts
SET tom.num_of_contacts = num_of_contacts
RETURN tom

//Alterar relações
DELETE and SET


//Null (empty)
(empty) = {}
Null = null


//ON CREATE SET
MERGE (location:location{name: 'San Francisco'})
ON CREATE SET location.created_at = timestamp(),
location.created_by = 'Louis'

//ON MATCH SET
MERGE (location:location{name: 'San Francisco'})
ON MATCH SET location.updated_at = timestamp()
RETURN location

//PATHS

MATCH path=((keanu:Person(name: 'Keanu Reeves')))

//Shortest path

MATCH (matrixActor:Person)-[:ACTED_IN]->(matrix:Movie{title: 'The Matrix'}),
(topGunActor:Person)-[:ACTED-IN]->(topGun:Movie{title: 'Top Gun'})
path = shortestPath((matrixActor)-[*..20]->(topGunActor))
WHERE matrixActor <> topGunActor
RETURN matrix, topGun, path, length(path) AS pathLength
ORDER BY pathLength
LIMIT 3