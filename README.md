# redis-tutorials

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
