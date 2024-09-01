
### Ollama official rest api document
https://github.com/ollama/ollama/blob/main/docs/api.md

Generate Embeddings: 

Parameters
- `model`: name of model to generate embeddings from
- `input`: text or list of text to generate embeddings for


Request:

```
curl http://localhost:11434/api/embed -d '{
  "model": "llama3",
  "input": "Why is the sky blue?"
}'
```

