# Key Test Dimensions

The following parameters were explored across tests:

| Dimension           | Range            |
|--------------------|-------------------------|
| Concurrency (VUs)    | 30 → 1000+  |
| Max Output Tokens    | 128 → 4096|
| Timeouts        | 60s → 200s → unlimited        |
| Pods       | 1 vs 2 |
| Pods       | 1 vs 2 |


Each request was a multi-turn chat completion with:

- Streaming enabled
- Mixed prompt content (including Persian text)
- Temperature = 0

This ensures we are measuring true API-level inference behavior, not offline token generation.
		
