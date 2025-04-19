# Redis Search

Install on Macos
```bash
# Install redis stack
brew install redis-stack

# Start redis stack
redis-stack-server

# Start redis cli
redis-cli

# Verify installed redis modules
MODULE LIST

# Install and start redis insight
brew install --cask redisinsight
open /Applications/RedisInsight.app
```

# Create index
```bash
FT.CREATE idx:docs ON HASH PREFIX 1 doc: SCHEMA title TEXT content TEXT timestamp NUMERIC SORTABLE vec VECTOR FLAT 6 TYPE FLOAT32 DIM 4 DISTANCE_METRIC COSINE
```


## 🔍 RediSearch Index Creation – Command Breakdown

### Command
```redis
FT.CREATE idx:docs ON HASH PREFIX 1 doc: SCHEMA title TEXT content TEXT timestamp NUMERIC SORTABLE vec VECTOR FLAT 6 TYPE FLOAT32 DIM 4 DISTANCE_METRIC COSINE
```

---

### 🧠 Breakdown

| Part | Description |
|------|-------------|
| `FT.CREATE idx:docs` | Creates a RediSearch index named `idx:docs`. |
| `ON HASH` | Specifies that the index will be built on Redis hashes (e.g., created using `HSET`). |
| `PREFIX 1 doc:` | Only keys starting with the prefix `doc:` will be indexed. |
| `SCHEMA` | Declares the fields and their types for indexing. |

---

### 📄 Schema Fields

#### `title TEXT`
- Full-text search field.
- Supports stemming, fuzzy search, tokenization, etc.

#### `content TEXT`
- Additional full-text search field (e.g., body text).

#### `timestamp NUMERIC SORTABLE`
- Numeric field, useful for filtering (e.g., by date range).
- `SORTABLE` allows sorting search results by this field.

#### `vec VECTOR FLAT 6 TYPE FLOAT32 DIM 4 DISTANCE_METRIC COSINE`
- `vec`: Name of the vector field.
- `VECTOR`: Indicates this field supports vector similarity search.
- `FLAT`: Uses brute-force algorithm (recommended for small datasets).
- `6`: Number of configuration parameters (required syntax).
- `TYPE FLOAT32`: Vectors consist of 32-bit floating-point numbers.
- `DIM 4`: Dimensionality of the vectors (use 768 or 1536 for real embeddings).
- `DISTANCE_METRIC COSINE`: Cosine similarity will be used for comparisons.

---

### ✅ Summary

This command creates a RediSearch index over documents like `doc:1`, enabling:

- **Full-text search** on `title` and `content`
- **Filtering and sorting** by `timestamp`
- **Vector similarity search** on `vec` using cosine distance


---

## 📄 Insert Documents

### Document 1

```redis
HSET doc:1 title "Redis Search" content "Fast in-memory full-text search engine" timestamp 1713500000
```

Add a 4D float32 vector: `[0, 1, 2, 3]`
```redis
HSET doc:1 vec "\x00\x00\x00\x00\x00\x00\x80?\x00\x00\x00@\x00\x00@@"
```

---

### Document 2

```redis
HSET doc:2 title "Vector Search" content "Similarity search using embeddings" timestamp 1713600000
```

Add vector: `[1, 2, 3, 4]`
```redis
HSET doc:2 vec "\x00\x00\x80?\x00\x00\x00@\x00\x00@@\x00\x00\x40@"
```

---

## 🔎 Step 3: Text Search Examples

### Search for documents containing "redis":
```redis
FT.SEARCH idx:docs "redis"
```

### Search for documents containing both "vector" and "embeddings":
```redis
FT.SEARCH idx:docs "vector embeddings"
```

### Filter by timestamp range:
```redis
FT.SEARCH idx:docs "*" FILTER timestamp 1713500000 1713600000
```

---

## 🤖 Step 4: Vector Similarity Search

Search for the 2 documents most similar to `[1, 2, 3, 4]`:

```redis
FT.SEARCH idx:docs "*" RETURN 3 title content __vec_score \
  DIALECT 2 PARAMS 2 vec "\x00\x00\x80?\x00\x00\x00@\x00\x00@@\x00\x00\x40@" \
  SORTBY __vec_score ASC \
  LIMIT 0 2 \
  VECTOR vec KNN 2 @vec
```

This performs:
- Approximate Nearest Neighbor search on the `vec` field
- Using cosine similarity
- Sorted by similarity score (`__vec_score`)