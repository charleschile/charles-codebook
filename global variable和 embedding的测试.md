SHOW VARIABLES LIKE 'disable_txn_trace';

 SET SESSION disable_txn_trace = 1;

vector_embedding_model


SHOW VARIABLES LIKE 'vector_embedding_model';

SET vector_embedding_model = "ernie";

SELECT embedding("This is text... bla ");


CREATE DATABASE vector_embeddings;

use vector_embeddings;

 SET vector_embedding_model = "ollama";

DROP TABLE IF EXISTS embeddings;

CREATE TABLE embeddings (
  id INT PRIMARY KEY AUTO_INCREMENT,
  text TEXT,
  embedding VECF32
);

INSERT INTO embeddings (text, embedding) VALUES ("ollama",  embedding("This is text... bla "))

SET vector_embedding_model = "gemini";