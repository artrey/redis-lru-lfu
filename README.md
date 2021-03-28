# Redis: LRU vs LFU

## Commands for testing

```cli
eval 'for i=1, 1000 do redis.call("SET", KEYS[1] .. i, i) end' 1 A

eval 'for i=1, 1000 do redis.call("SET", KEYS[1] .. i, i) end' 1 B

eval 'for i=1, 1000 do redis.call("GET", KEYS[1] .. i) end' 1 A

eval 'for i=1, 1000 do redis.call("GET", KEYS[1] .. i) end' 1 A

eval 'for i=1, 1000 do redis.call("GET", KEYS[1] .. i) end' 1 A

eval 'for i=1, 1000 do redis.call("GET", KEYS[1] .. i) end' 1 B

eval 'for i=1, 2000 do redis.call("SET", KEYS[1] .. i, i) end' 1 C

keys A*

keys B*

keys C*
```

## LRU

### Configuration

```txt
maxmemory 1mb
maxmemory-policy allkeys-lru
maxmemory-samples 10
```

### Results (average)

| A\* | B\* | C\*  |
| --- | --- | ---- |
| 199 | 925 | 2000 |

## LFU

### Configuration

```txt
maxmemory 1mb
maxmemory-policy allkeys-lfu
maxmemory-samples 10
```

### Results

| A\*  | B\*  | C\*  |
| ---- | ---- | ---- |
| 1000 | 1000 | 1124 |

## Explanation

In **LRU** mode the keys that were retrieved earlier are evicted with higher priority. Regardless of the frequency of extraction.

In **LFU** mode frequently retrieved keys are evicted with the lowest priority. Regardless of the time of extraction.
